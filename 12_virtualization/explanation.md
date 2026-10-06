# Module 12 — Virtualization (ပြည့်စုံသော ရှင်းလင်းချက်)

> ဤ module ၏ ရည်ရွယ်ချက် — GPU ကို မျှဝေ အသုံးပြုနည်း ၃ မျိုး (Time-slicing, MIG, vGPU) ကို isolation ရှုထောင့်မှ ခွဲခြား မှတ်မိပြီး scenario အလိုက် မှန်ကန်စွာ ရွေးချယ်တတ်စေရန် ဖြစ်ပါသည်။

---

## 1. ဘာကြောင့် GPU မျှဝေ လိုအပ်သလဲ (Objective 3.4)

Data center GPU တစ်လုံး၏ တန်ဖိုးသည် ဒေါ်လာ ၃ သောင်းကျော် ရှိပြီး အချို့ workload များသည် GPU တစ်လုံးလုံး မလိုအပ်ပါ။ ထို့ကြောင့် GPU တစ်ခုကို workload/user အမျိုးမျိုးအကြား မျှဝေ အသုံးပြုနည်း ၃ မျိုး ရှိပါသည်။

## 2. Access Model ၃ မျိုး (အခြေခံ)

1. **Bare metal** — စက်တစ်လုံးလုံး တစ်ဦးတည်း — performance အပြည့်အဝ
2. **Virtual Machine (hypervisor)** — VM အများ တစ်ခုတည်းသော hardware ပေါ် — strong isolation
3. **Container** — OS kernel မျှဝေ — lightweight၊ AI workload တွင် အသုံးများ

---

## 3. GPU Sharing / Partitioning နည်းလမ်း ၃ မျိုး ⭐

### ① Time-slicing

GPU တစ်လုံးကို time slot များ ခွဲဝေပြီး workload အမျိုးမျိုး အလှည့်ကျ အသုံးပြုခြင်း —
- **Hardware isolation မရှိ** — workload များ အပြည့်အဝ ရောမွှေနေပါသည်
- Memory ကို သီးခြား မခွဲပေးနိုင် — တစ်ခု crash ပါက အားလုံး ထိခိုက်နိုင်
- ကုန်ကျ သက်သာ၊ config လွယ်ကူ (driver config / K8s device plugin)

```
Time-slicing:  [workload A] [workload B] [workload A] [workload B] ...
               ← အချိန် slot အလိုက် အလှည့်ကျ (isolation မရှိ)
```

### ② MIG (Multi-Instance GPU) ⭐ (Ampere မှ စတင်)

GPU ကို **hardware level** တွင် partition ခွဲခြင်း — SM, memory, L2 cache အားလုံး သီးခြား ခွဲဝေပါသည်။

![GPU multi-user without MIG](../images/mig-multiuser-before.png)
*ပုံ — MIG မပါဘဲ multi-user sharing (isolation မရှိ) (ရင်းမြစ်: NVIDIA Developer Blog)*

![GPU multi-user with MIG](../images/mig-multiuser-after.png)
*ပုံ — MIG ဖြင့် hardware-isolated instance များ (ရင်းမြစ်: NVIDIA Developer Blog)*

- **Hardware isolation အပြည့်အဝ** — instance တစ်ခု crash ပါက အခြားတစ်ခု မထိခိုက်
- QoS သေချာ — performance တည်ငြိမ်
- **A100/H100/B200** ကဲ့သို့ data center GPU များတွင်သာ ရရှိ (GeForce တွင် မရ)
- **Instance အမြင့်ဆုံး 7 ခု** အထိ ခွဲဝေနိုင်

### ③ vGPU (NVIDIA GRID / virtual GPU)

Hypervisor ဖြင့် mediated sharing — license လိုအပ်ပြီး VM/desktop မျှဝေအတွက် အဓိက —
- QoS နှင့် management feature ပါဝင်
- **VDI (virtual desktop)** အတွက် အသုံးများ
- License ကုန်ကျစရိတ် ရှိပါသည်

---

## 4. ၃ မျိုး နှိုင်းယှဉ်ချက် ⭐

| အချက် | Time-slicing | MIG | vGPU |
|---|---|---|---|
| Isolation | မရှိ | **hardware-level** | hypervisor-level |
| Memory ခွဲဝေ | မရ | ရ | ရ |
| Performance တည်ငြိမ် | နည်း | မြင့် | အလယ် |
| License | မလို | မလို | **လို** |
| Hardware | driver config | Ampere+ data center GPU | GRID supported GPU |
| အသုံးပြုမှု | dev/test | isolated workload (inference) | VDI/desktop |

**မှတ်ရနည်း** — Time-slicing = အချိန် ခွဲဝေ (ကား တစ်စီး မိသားစု အလှည့်ကျ)၊ MIG = hardware ခွဲဝေ (ကားကို ၇ စီးအဖြစ် ခွဲ)၊ vGPU = hypervisor ဖြင့် ခွဲဝေ (အငှားကား ကုမ္ပဏီ စီမံ)။

---

## 5. MIG Profile များ အသေးစိတ်

H100 80GB ကို ဥပမာ ယူပါက —

| Profile | Memory | ရရှိနိုင်မှု |
|---|---|---|
| 1g.10gb | 10GB | 8 instances |
| 2g.20gb | 20GB | 4 instances |
| 3g.40gb | 40GB | 2 instances |
| 7g.80gb | 80GB | 1 instance (full) |

⚠️ အမြင့်ဆုံး instance အရေအတွက်ကို "7" အဖြစ် မှတ်သားထားပါ — ဤဂဏန်းကို စာမေးပွဲတွင် တိုက်ရိုက် မေးလေ့ရှိပါသည်။

---

## 6. Kubernetes နှင့် ပေါင်းစပ်ခြင်း

MIG instance များကို Kubernetes device plugin ဖြင့် resource အဖြစ် ဖြန့်ဝေနိုင်ပါသည် —
- MIG strategy: `single` (GPU တစ်လုံး = instance တစ်ခု) သို့မဟုတ် `mixed` (profile အမျိုးမျိုး ရောနှော)
- pod တစ်ခုက MIG profile တစ်ခု တောင်းဆိုခြင်း

---

## 7. Scenario ရွေးချယ်ပုံ

| Scenario | ရွေးချယ်မှု | အကြောင်းရင်း |
|---|---|---|
| Dev/test အတွက် GPU မျှဝေ | Time-slicing | ရိုးရှင်း၊ isolation မလို |
| Production inference (isolation လို) | MIG | hardware isolation + QoS |
| VDI / design ဝန်ထမ်း desktop | vGPU | desktop profile + management |
| HPC training | Bare metal (မမျှဝေပါနှင့်) | performance အပြည့်အဝ |

**"Inference service ၄ ခုကို GPU တစ်လုံးပေါ်တွင် isolated ဖြင့် run ချင်"** → MIG
**"Developer ၈ ယောက်ကို dev/test GPU မျှဝေ — cost သက်သာရုံ"** → Time-slicing
**"Design ဝန်ထမ်း ၂၀ ကို virtual desktop (3D app)"** → vGPU

---

## 8. အဓိက မှားယွင်းနိုင်သော အချက်များ

1. MIG = hardware partition / vGPU = mediated / time-slicing = time-share
2. MIG instance အမြင့်ဆုံး = 7
3. MIG သည် consumer GPU တွင် မရရှိ
4. vGPU သည် license လိုအပ်သည်
5. Time-slicing တွင် memory isolation မရှိ

---

## 9. Mini-Quiz

1. Time-slicing, MIG, vGPU ကို isolation ရှုထောင့်မှ နှိုင်းယှဉ်ပါ။
2. Hardware-isolated instance ၇ ခု ခွဲဝေလိုပါက မည်သည့်နည်း သုံးမလဲ။
3. MIG ကို GeForce GPU တွင် အသုံးပြုနိုင်/မနိုင် ရှင်းပြပါ။

**အဖြေများ** — 1) Time-slicing = isolation မရှိ၊ MIG = hardware-level အပြည့်အဝ၊ vGPU = hypervisor-level · 2) **MIG** · 3) မနိုင် — Ampere+ data center GPU feature

---

## 10. Official References

- NVIDIA MIG User Guide: https://docs.nvidia.com/datacenter/tesla/mig-user-guide/
- NVIDIA vGPU: https://www.nvidia.com/en-us/enterprise/virtual-gpu-technology/
- Multi-Instance GPU (MIG): https://www.nvidia.com/en-us/technologies/multi-instance-gpu/
- GPU Operator MIG strategies: https://docs.nvidia.com/datacenter/cloud-native/gpu-operator/latest/
