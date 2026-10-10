# Module 12 — Virtualization (ပြည့်စုံသော ရှင်းလင်းချက်)

> ဤ module ၏ ရည်ရွယ်ချက် — GPU ကို မျှဝေ အသုံးပြုနည်း ၃ မျိုး (Time-slicing, MIG, vGPU) ကို isolation ရှုထောင့်မှ ခွဲခြား မှတ်မိပြီး scenario အလိုက် မှန်ကန်စွာ ရွေးချယ်တတ်စေရန် ဖြစ်ပါသည်။

---

## 1. ပြဿနာ — GPU တစ်လုံးလုံးကို user တစ်ယောက်တည်း ဘာကြောင့် မပေးနိုင်တာလဲ (Objective 3.4)

Data center GPU တစ်လုံး၏ တန်ဖိုးသည် ဒေါ်လာ ၃ သောင်းကျော် ရှိပြီး အချို့ workload များသည် GPU တစ်လုံးလုံး မလိုအပ်ပါ။ ဥပမာ — inference service ငယ်တစ်ခုသည် GPU အင်အား ၁၀% သာ သုံးပြီး developer တစ်ဦး၏ dev/test workload က အခြားတစ်ဦးနှင့် ရောမွှေမိပါက စွမ်းဆောင်ရည် မတည်ငြိမ်ပါ။ ထို့ကြောင့် GPU တစ်ခုကို workload/user အမျိုးမျိုးအကြား မျှဝေ အသုံးပြုနည်း ၃ မျိုး — **Time-slicing, MIG, vGPU** — ကို isolation အဆင့်အလိုက် ခွဲခြား နားလည်ရန် လိုအပ်ပါသည်။

## 2. Core Concept — Access Model ၃ မျိုး (အခြေခံ)

1. **Bare metal** — စက်တစ်လုံးလုံး တစ်ဦးတည်း — performance အပြည့်အဝ
2. **Virtual Machine (hypervisor)** — VM အများ တစ်ခုတည်းသော hardware ပေါ် — strong isolation
3. **Container** — OS kernel မျှဝေ — lightweight၊ AI workload တွင် အသုံးများ

---

## 3. Core Concept — GPU Sharing / Partitioning နည်းလမ်း ၃ မျိုး ⭐

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

## 4. နှိုင်းယှဉ်ချက် — ၃ မျိုး ⭐

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

H100 80GB ကို ဥပမာ ယူပါက (NVIDIA MIG User Guide အတိုင်း) —

| Profile | Memory | ရရှိနိုင်မှု |
|---|---|---|
| 1g.10gb | 10GB | 7 instances |
| 2g.20gb | 20GB | 3 instances |
| 3g.40gb | 40GB | 2 instances |
| 7g.80gb | 80GB | 1 instance (full) |

⚠️ အမြင့်ဆုံး instance အရေအတွက်ကို "7" အဖြစ် မှတ်သားထားပါ — ဤဂဏန်းကို စာမေးပွဲတွင် တိုက်ရိုက် မေးလေ့ရှိပါသည်။ profile တစ်ခုချင်းစီ၏ instance အရေအတွက်သည် memory/SM slice ခွဲဝေပုံပေါ် မူတည်၍ ပြောင်းလဲနိုင်ပြီး GPU model အလိုက်လည်း မတူနိုင်ပါ။

---

## 6. လက်တွေ့ Configuration — MIG ဖွင့်ခြင်းနှင့် Kubernetes ပေါင်းစပ်ခြင်း

MIG instance များကို ဖန်တီးရန် အောက်ပါ အဆင့်များ အသုံးပြုပါသည် (illustrative — driver version ပေါ် မူတည်) —

```bash
sudo nvidia-smi -i 0 -mig 1        # GPU 0 အတွက် MIG mode ဖွင့်ခြင်း
nvidia-smi mig -lgip               # ရရှိနိုင်သော GPU instance profile များ စာရင်း ကြည့်ခြင်း
sudo nvidia-smi mig -cgi 9,9 -C    # profile ID ဖြင့် instance ၂ ခု ဖန်တီးပြီး compute instance ချိတ်ပေးခြင်း
nvidia-smi -L                      # ဖန်တီးပြီး MIG device များ စစ်ဆေးခြင်း
```

Kubernetes နှင့် ပေါင်းစပ်ရာတွင် MIG instance များကို device plugin ဖြင့် resource အဖြစ် ဖြန့်ဝေနိုင်ပါသည် —
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

## 8. လည်ပတ်မှု သတိပေးချက်များနှင့် စာမေးပွဲ Trap များ

- Time-slicing တွင် memory မခွဲသောကြောင့် workload တစ်ခု OOM ဖြစ်ပါက အခြား workload များပါ ထိခိုက်နိုင် — production multi-tenant အတွက် မသင့်ပါ
- MIG instance များကို ဖန်တီး/ဖျက်ပါက running workload များ ရပ်တန့်နိုင်သဖြင့် ပြောင်းလဲမှုကို change window အတွင်း ပြုလုပ်ပါ
- vGPU သည် license မပါဘဲ အလုပ်မလုပ်ပါ — deployment မတိုင်မီ license စီစဉ်ထားရန် လိုအပ်ပါသည်

### အဓိက မှားယွင်းနိုင်သော အချက်များ

1. MIG = hardware partition / vGPU = mediated / time-slicing = time-share
2. MIG instance အမြင့်ဆုံး = 7
3. MIG သည် consumer GPU တွင် မရရှိ
4. vGPU သည် license လိုအပ်သည်
5. Time-slicing တွင် memory isolation မရှိ

---

## 9. Self-check

1. Time-slicing, MIG, vGPU ကို isolation ရှုထောင့်မှ နှိုင်းယှဉ်ပါ။
2. Hardware-isolated instance ၇ ခု ခွဲဝေလိုပါက မည်သည့်နည်း သုံးမလဲ။
3. MIG ကို GeForce GPU တွင် အသုံးပြုနိုင်/မနိုင် ရှင်းပြပါ။
4. Time-slicing တွင် workload တစ်ခု crash ပါက အခြား workload များ ဘာကြောင့် ထိခိုက်နိုင်သလဲ။
5. VDI အတွက် မည်သည့်နည်း အသင့်တော်ဆုံးလဲ၊ ဘာကြောင့်လဲ။

**အဖြေများ**

1. Time-slicing = isolation မရှိ၊ MIG = hardware-level အပြည့်အဝ၊ vGPU = hypervisor-level
2. **MIG**
3. မနိုင် — Ampere+ data center GPU feature
4. memory သီးခြား မခွဲထားသောကြောင့် — memory isolation မရှိပါ
5. vGPU (GRID) — desktop profile နှင့် management feature ပါဝင်သောကြောင့်

---

## 10. Official References

- NVIDIA MIG User Guide: https://docs.nvidia.com/datacenter/tesla/mig-user-guide/
- NVIDIA vGPU: https://www.nvidia.com/en-us/enterprise/virtual-gpu-technology/
- Multi-Instance GPU (MIG): https://www.nvidia.com/en-us/technologies/multi-instance-gpu/
- GPU Operator MIG strategies: https://docs.nvidia.com/datacenter/cloud-native/gpu-operator/latest/

---

## Technical review notes

- မူလ MIG profile ဇယားတွင် 1g.10gb = 8 instances၊ 2g.20gb = 4 instances ဟု ပါရှိခဲ့သော်လည်း NVIDIA MIG User Guide (Supported MIG Profiles) အရ H100 80GB တွင် 1g.10gb = 7 instances၊ 2g.20gb = 3 instances ဖြစ်သဖြင့် မှန်ကန်သော ဂဏန်းများဖြင့် ပြင်ဆင်ထားပါသည်။
- Section 6 ၏ `nvidia-smi mig` command များသည် illustrative ဖြစ်ပြီး profile ID များ (ဥပမာ `9`) သည် GPU model နှင့် driver version ပေါ် မူတည်၍ မတူနိုင်ပါ (needs verification)။
- "DGX/GeForce တွင် မရ" စသည့် hardware ကန့်သတ်ချက်များသည် GPU generation အသစ်များတွင် ပြောင်းလဲနိုင်ပြီး စာမေးပွဲအတွက် မူလ အဓိပ္ပာယ်အတိုင်း မှတ်သားပါ (needs verification)။
