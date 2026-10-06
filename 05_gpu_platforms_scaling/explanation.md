# Module 05 — GPU Platforms & Scaling (ပြည့်စုံသော ရှင်းလင်းချက်)

> ဤ module ၏ ရည်ရွယ်ချက် — DGX/HGX/MGX/IGX platform များ ခွဲခြားနိုင်ခြင်း၊ scale-up vs scale-out ကို မမှားစေခြင်း၊ multi-GPU parallelism နှင့် cluster reference architecture ကို နားလည်စေခြင်း ဖြစ်ပါသည်။

---

## 1. NVIDIA Platform မိသားစု

| Platform | ရှင်းလင်းချက် | မည်သူအတွက် |
|---|---|---|
| **DGX** | 8×GPU ပါ ပြီးပြည့်စုံ system (hardware + software stack အပြည့်) | ဝယ်ပြီး ချက်ချင်း run |
| **HGX** | GPU baseboard — OEM များ ကိုယ်ပိုင် server ဆောက်ရန် | Dell, Supermicro, HPE |
| **MGX** | modular reference design (mixed GPU/CPU/DPU) | enterprise/edge စိတ်ကြိုက် |
| **IGX** | edge/industrial platform (medical, robotics) | real-time edge AI |

**မှတ်ရနည်း** — DGX = ကား အပြည့်အစုံ ဝယ်ခြင်း၊ HGX = chassis + engine ဝယ်ပြီး ကိုယ်တိုင် တပ်ဆင်ခြင်း၊ MGX = modular အစိတ်အပိုင်း၊ IGX = စက်ရုံ/edge အတွက်။

![HGX A100 8-way baseboard](../images/hgx-a100-8way.jpg)
*ပုံ — HGX A100 8-way GPU baseboard (ရင်းမြစ်: NVIDIA Developer Blog)*

![HGX-2 platform](../images/hgx2-platform.jpg)
*ပုံ — HGX-2 platform (ရင်းမြစ်: NVIDIA Developer Blog)*

---

## 2. DGX မျိုးဆက်များ — စွမ်းရည် တိုးတက်မှု

| System | GPU | GPU Memory | Performance (FP8) | Power |
|---|---|---|---|---|
| DGX A100 | 8×A100 | 320GB (640GB ဗားရှင်း ရှိ) | ~5 PFLOPS | 6.5kW |
| DGX H100 | 8×H100 | 640GB HBM3 | 32 PFLOPS | 10.2kW |
| DGX H200 | 8×H200 | 1.1TB HBM3e | ~32 PFLOPS | 10.2kW |
| **DGX B200** | 8×B200 | **1.4TB** HBM3e | 72 PFLOPS (FP4) | **14.3kW** |
| GB200 NVL72 | 72×B200 (rack) | 13.4TB | exascale class | ~120kW |

⚠️ DGX B200 တစ်လုံး = 14.3kW — ရုံးအဆောက်အဦ တစ်ခုလောက် ပါဝါ စားသုံးပါသည်။ ဤ table သည် rack density မေးခွန်းများ၏ အခြေခံ ဖြစ်ပါသည်။

**GB200 NVL72** — GPU 72 လုံးကို rack တစ်ခုလုံးအဖြစ် NVLink ဖြင့် ချိတ်ဆက်ပြီး "တစ်ခုတည်းသော ကြီးမား GPU" အဖြစ် မြင်နိုင်အောင် ဆောက်လုပ်ထားခြင်း (rack-scale computing) ဖြစ်ပါသည်။

---

## 3. Scale-up vs Scale-out ⭐ (Exam trap အကြီးဆုံး)

| အချက် | **Scale-up** | **Scale-out** |
|---|---|---|
| အကွာအဝေး | node (သို့ rack) အတွင်း | node များကြား |
| Technology | **NVLink, NVSwitch** | **InfiniBand, Spectrum-X Ethernet** |
| အမြန်နှုန်း | TB/s class (NVLink 5 = 1.8TB/s) | 400-800Gb/s per port |
| ရည်ရွယ်ချက် | GPU ချင်း ကြား အနီးကပ် (tensor parallel) | cluster ချဲ့ထွင် (data parallel) |

```mermaid
flowchart LR
    subgraph Node အတွင်း Scale-up
    G1[GPU] ---|NVLink TB/s| G2[GPU]
    G2 ---|NVLink| G3[GPU]
    G3 ---|NVLink| G4[GPU]
    end
    subgraph Node ကြား Scale-out
    N1[Node 1] ---|InfiniBand 400G| SW[Fabric Switch]
    SW ---|InfiniBand| N2[Node 2]
    SW ---|InfiniBand| N3[Node N]
    end
```

**မှတ်ရနည်း** — NVLink = အိမ်အတွင်း ဆက်သွယ်ရေး (အလွန်မြန်)၊ InfiniBand = အိမ်ချင်းကြား လမ်းမကြီး။ NVSwitch သည် node အတွင်း NVLink connection များကို စီမံသော switch chip ဖြစ်ပါသည်။

---

## 4. Multi-GPU Parallelism

```mermaid
flowchart TD
    subgraph Data Parallel
    D1[GPU 1: model copy] 
    D2[GPU 2: model copy]
    D1 <-->|gradient all-reduce| D2
    end
    subgraph Tensor Parallel
    T1[GPU 1: layer ၏ တစ်ဝက်] <-->|NVLink| T2[GPU 2: layer ၏ တစ်ဝက်]
    end
    subgraph Pipeline Parallel
    P1[GPU 1: layer 1-10] --> P2[GPU 2: layer 11-20] --> P3[GPU 3: layer 21-30]
    end
```

1. **Data parallel** — GPU တိုင်းက model အပြည့် ပိုင်ဆိုင်ပြီး data ခွဲဝေစားသုံး — step တိုင်း gradient all-reduce (NCCL)
2. **Tensor parallel** — layer တစ်ခုချင်း matrix ကို GPU များကြား ခွဲဝေ (NVLink လိုအပ်)
3. **Pipeline parallel** — layer အလွှာ အလိုက် GPU များ ခွဲဝေ (assembly line ကဲ့သို့)
4. **Expert parallel** — MoE model များတွင် expert အလိုက် ခွဲဝေ

---

## 5. Reference Architecture — BasePOD vs SuperPOD

| အဆင့် | ရှင်းလင်းချက် |
|---|---|
| Node | DGX တစ်လုံး (8 GPU) |
| Rack | node 16-64 ပါ (power/cooling ဒီအဆင့်တွင်) |
| **BasePOD** | validated reference pod — compute + fabric + storage + management |
| **SuperPOD** | multi-rack supercomputer — GPU ရာ/ထောင်ချီ |

⚠️ BasePOD vs SuperPOD = pod တစ်ခု vs supercomputer တစ်ခု — စာမေးပွဲတွင် ရှုပ်အောင် မေးလေ့ရှိပါသည်။

---

## 6. Use Case အလိုက် Hardware ရွေးချယ်ခြင်း (Objective 2.1)

| Use case | GPU requirement | Network |
|---|---|---|
| 7B fine-tune | 8×H100 တစ် node (NVLink) | NVLink ဖြင့် လုံလောက် |
| 100B+ pretrain | GPU ရာ/ထောင်၊ multi-node | InfiniBand မဖြစ်မနေ |
| Real-time inference | L40S/H100၊ latency သေချာ | standard (+NVLink TP) |
| Offline batch inference | throughput မြင့် | standard |
| VDI / graphics | L4/L40 | MIG/vGPU sharing |

**ဆုံးဖြတ်ချက် အဆင့်များ** — ① model size × precision → GPU memory → ② training/inference → ③ single/multi node → ④ network fabric → ⑤ storage I/O → ⑥ power/cooling budget

---

## 7. လက်တွေ့ မြင်ကွင်း — 70B Model Training စဉ်းစားပုံ

**မေးခွန်း** — Llama-70B ကို fine-tune မည်။ GPU အနည်းဆုံး မည်မျှ လိုအပ်သလဲ။

**စဉ်းစားချက်** — 70B × 12-16 bytes ≈ 840GB-1.1TB — H100 80GB တစ်လုံးတွင် မဝင်ဆန့်ပါ — node အနည်းဆုံး ၂-၄ ခု (GPU 16-32 လုံး) လိုအပ်ပြီး: node အတွင်း tensor parallel (NVLink) + node ကြား data parallel (InfiniBand) + parallel file system ။

---

## 8. အဓိက မှားယွင်းနိုင်သော အချက်များ

1. NVLink/NVSwitch = scale-up vs InfiniBand/Spectrum-X = scale-out
2. DGX vs HGX — ပြီးပြည့်စုံ system vs baseboard
3. BasePOD vs SuperPOD — pod vs supercomputer
4. Training cluster တွင် network က ဦးစားပေး
5. Power figure များ — rack planning မေးခွန်းနှင့် ချိတ်ဆက်

---

## 9. Mini-Quiz

1. OEM က ကိုယ်ပိုင် server တွင် 8×H100 ချိတ်ဆက် ဆောက်လုပ်လိုပါက မည်သည့် platform လိုအပ်သလဲ။
2. GPU 1000 ပါ training cluster အတွက် fabric ၂ မျိုး ခွဲခြားဖော်ပြပါ။
3. DGX B200 တစ်လုံး ပါဝါ မည်မျှ စားသုံးသလဲ။

**အဖြေများ** — 1) HGX baseboard · 2) node ကြား = InfiniBand (scale-out)၊ node အတွင်း = NVLink/NVSwitch (scale-up) · 3) 14.3kW

---

## 10. Official References

- NVIDIA DGX B200: https://www.nvidia.com/en-us/data-center/dgx-b200/
- NVIDIA HGX: https://www.nvidia.com/en-us/data-center/dgx/
- NVLink and NVSwitch: https://www.nvidia.com/en-us/data-center/nvlink/
- BasePOD / SuperPOD: https://www.nvidia.com/en-us/data-center/data-center-reference-architecture/
