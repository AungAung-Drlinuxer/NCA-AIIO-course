# Module 13 — Review & Practice Exam (ပြည့်စုံသော ရှင်းလင်းချက်)

> ဤ module ၏ ရည်ရွယ်ချက် — course တစ်ခုလုံးကို ၃၀ မိနစ်အတွင်း ပြန်လည် သုံးသပ်နိုင်ခြင်း၊ trap များ မမေ့စေခြင်း၊ practice exam ဖြင့် ကိုယ့်အဆင့်ကို တိုင်းတာနိုင်ခြင်း ဖြစ်ပါသည်။

---

## 1. ပြဿနာ — ဖတ်ပြီးသမျှကို စာမေးပွဲမှာ ဘယ်လို ပြန်ထုတ်မလဲ

Module 00–12 ကို ဖတ်ပြီးသော်လည်း စာမေးပွဲတွင် ကြုံရနိုင်သည့် ပြဿနာ ၃ ရပ် ရှိပါသည် —

1. **အချက်များ များပြားခြင်း** — domain ၃ ခု၊ objective ဆယ်ချီ — ပြန်ဖတ်ရန် အချိန် မလုံလောက်
2. **Trap များ** — လွယ်ကူစွာ မှားတတ်သော စကားလုံးများ (EXCEPT, best, most) — အမှန်ကို သိသော်လည်း မှားဖြေမိခြင်း
3. **ကိုယ့်အဆင့် မသိခြင်း** — အားနည်းသော domain ကို မသိဘဲ ဖြေဆိုမိခြင်း

ဤ module သည် ပြဿနာ ၃ ရပ်လုံးကို ဖြေရှင်းပေးပါမည် — 30 မိနစ် review (အချက်များ ပြန်ဆန်းစစ်)၊ Top-10 traps (မှားတတ်သော အချက်များ ကြိုတင်ရှောင်)၊ 25 မေးခွန်း practice exam (ကိုယ့်အဆင့် တိုင်းတာ)။

---

## 2. Course တစ်ခုလုံး ပြန်လည် သုံးသပ်ခြင်း — 30 မိနစ် Review

### Domain 1 — Essential AI Knowledge (38%)
- **AI ⊃ ML ⊃ DL** — DL သည် neural network အလွှာ များများဖြင့် ML ၏ အမျိုးအစားခွဲ
- **ML အမျိုးအစား ၄ မျိုး** — supervised, unsupervised, reinforcement, self-supervised (LLM)
- **AI တိုးတက်မှု factor ၄ ခု** — data, compute, algorithm, investment
- **GPU vs CPU** — GPU = throughput (parallel matrix)၊ CPU = latency (sequential)
- **Tensor Core** = matrix engine၊ **HBM** = data center memory (GDDR = consumer)
- **Software stack** — CUDA → cuDNN/cuBLAS/NCCL → NeMo/PyTorch → TensorRT/Triton/NIM → DCGM
- **Training vs Inference** — memory 12-16B/param (training) vs weight+KV cache (inference)

### Domain 2 — AI Infrastructure (40%)
- **DGX** (ပြီးပြည့်စုံ) / **HGX** (baseboard) / **MGX** (modular) / **IGX** (edge)
- **DGX B200** = 14.3kW；GB200 NVL72 = 72×B200 ~120kW
- **Scale-up** (NVLink/NVSwitch) vs **Scale-out** (InfiniBand/Spectrum-X)
- **Parallelism** — data / tensor / pipeline / expert
- **Power** — 40-130kW/rack (GPU) vs 5-10kW (ရိုးရာ)；N+1 vs 2N；**PUE = facility ÷ IT**
- **Cooling** — >40-50kW/rack ဆို liquid cooling
- **Networking** — RDMA (kernel bypass)；RoCEv2；PFC/ECN；GPUDirect；SHARP
- **DPU/BlueField** — networking/storage/security offload；DOCA = DPU platform
- **On-prem (CapEx) vs Cloud (OpEx)** — egress, data gravity, quota

### Domain 3 — AI Operations (22%)
- **nvidia-smi** (node) vs **DCGM** (cluster)；**DCGM Exporter → Prometheus → Grafana**
- **Low GPU util 6 reasons** — data loading, CPU preprocessing, network, batch သေး, checkpoint, throttling
- **Xid errors** — dmesg；BMC = out-of-band
- **K8s** — `nvidia.com/gpu`；**Device Plugin**；**GPU Operator**
- **Slurm** — sbatch/squeue/sacct；**gang scheduling**
- **Virtualization** — Time-slicing / **MIG** (7 instances) / **vGPU** (license, VDI)

---

## 3. Top-10 Exam Traps — ဖြေဆိုမီ ဖတ်ပါ

| # | လွယ်ကူစွာ မှားတတ်သော အချက် | မှန်ကန်သော အဖြေ |
|---|---|---|
| 1 | GPU က CPU ထက် မြန်သည် | parallel workload တွင်သာ မြန် |
| 2 | DL ⊃ ML ⊃ AI | AI ⊃ ML ⊃ DL |
| 3 | NVLink = cluster fabric | NVLink = node အတွင်း (scale-up) |
| 4 | Triton = model optimizer | TensorRT = optimizer၊ Triton = server |
| 5 | MIG = software partition | MIG = hardware partition (7 instances) |
| 6 | vGPU = license မလို | vGPU က license လိုအပ် |
| 7 | Inference memory = weight သာ | weight + KV cache |
| 8 | Cloud က အမြဲ သက်သာ | steady workload တွင် on-prem ပိုသက်သာ |
| 9 | DOCA = GPU platform | DOCA = DPU platform |
| 10 | Base Command Platform = on-prem | Platform = cloud၊ Manager = on-prem |

---

## 4. Practice Exam — 25 မေးခွန်း

### Domain 1 (Q1-10)
1. "GPU က CPU ထက် မြန်" မှားရခြင်း အကြောင်းရင်း?
2. LLM pretraining သည် ML အမျိုးအစား မည်သည့်အရာနှင့် အနီးစပ်ဆုံး?
3. 7B model fine-tune အတွက် memory မည်မျှ (Adam)?
4. Matrix multiplication အတွက် အထူးပြု hardware unit?
5. Distributed training တွင် gradient ပေါင်းချုပ်ရာ အသုံးပြုသော library?
6. Model ကို production inference အတွက် optimize ပြုလုပ်သော tool?
7. NGC သည် မည်သည့်အရာ ဖြစ်သလဲ?
8. Training တွင် network အရေးကြီးရခြင်း အကြောင်းရင်း?
9. Decode အဆင့် bottleneck က ဘာလဲ?
10. Inference memory အဓိက ပါဝင်သော အရာ ၂ ခု?

### Domain 2 (Q11-20)
11. OEM က 8×H100 server ဆောက်လိုပါက မည်သည့် platform?
12. Node ကြား training fabric ၂ ခု?
13. DGX B200 တစ်လုံး power?
14. Rack density မည်မျှ ကျော်ပါက liquid cooling စဉ်းစားသင့်?
15. PUE 1.5 ဆိုပါက non-IT ရာခိုင်နှုန်း?
16. RDMA က TCP ထက် မြန်ရခြင်း အကြောင်းရင်း?
17. InfiniBand ၏ in-network computing feature?
18. DPU ၏ အဓိက အလုပ်?
19. 24/7 steady training workload အတွက် ပိုသင့်တော်သော အရာ?
20. Cloud တွင် dataset ကြီးများ ပြဿနာ ၂ ခု?

### Domain 3 (Q21-25)
21. Cluster အဆင့် GPU monitoring tool?
22. DCGM metrics visualization pipeline ၃ ဆင့်?
23. GPU util နည်းနေခြင်း အကြောင်းရင်း ၃ ခု?
24. Hardware-isolated GPU instance ၇ ခု ခွဲဝေချင်ပါက မည်သည့်နည်း?
25. VDI အတွက် အသင့်တော်ဆုံး virtualization နည်းလမ်း?

---

## 5. Answer Key + ရှင်းလင်းချက်

| Q | အဖြေ | ရှင်းလင်းချက် |
|---|---|---|
| 1 | parallel workload တွင်သာ မြန် | sequential တွင် CPU ပိုမြန် |
| 2 | Self-supervised | next-token prediction |
| 3 | ~84-112GB | 12-16 bytes/param |
| 4 | Tensor Core | matrix multiply-accumulate engine |
| 5 | NCCL | all-reduce collective |
| 6 | TensorRT | optimizer (Triton = serving) |
| 7 | Catalog/registry | container/model catalog |
| 8 | synchronous all-reduce | GPU အားလုံး ပေါင်းချုပ်ရ |
| 9 | Memory bandwidth | GEMV — weight+KV ဖြတ်သန်းရ |
| 10 | weight + KV cache | |
| 11 | HGX | baseboard (DGX = ပြီးပြည့်စုံ) |
| 12 | InfiniBand, Spectrum-X | scale-out fabric |
| 13 | 14.3kW | |
| 14 | ~40-50kW/rack | air cooling limit |
| 15 | 33% | IT load ၏ 1/3 ပိုကုန် |
| 16 | kernel bypass | memory-to-memory တိုက်ရိုက် |
| 17 | SHARP | switch အတွင်း aggregation |
| 18 | infrastructure offload | network/storage/security |
| 19 | On-prem | steady = TCO ပြန်ရ |
| 20 | egress cost, data gravity | |
| 21 | DCGM | nvidia-smi = node သာ |
| 22 | Exporter → Prometheus → Grafana | |
| 23 | data loading, CPU prep, network | |
| 24 | MIG | hardware partition (7 instances) |
| 25 | vGPU (GRID) | desktop profile, license |

---

## 6. Scoring Guide

- **22+/25 (88%+)** — အဆင်သင့် — ချက်ချင်း မှတ်ပုံတင် ဖြေဆိုနိုင်
- **18-21 (72-84%)** — နီးစပ် — weak domain ပြန်ဖတ်ပြီး ၁ ပတ်အကြာ ပြန်ဖြေ
- **<17** — Module 05-09 (Infrastructure) ကို ပြန်လည် ဖတ်ရှုပါ

⚠️ စာမေးပွဲတွင် 70% အနည်းဆုံး ရရှိမှ အောင်မြင်နိုင်မည် မှန်းဆပြီး practice တွင် 85%+ အမြဲ ရအောင် လေ့ကျင့်ပါ — margin ထားခြင်း ဖြစ်ပါသည်။

---

## 7. Exam-Day Checklist

1. **Proctor ပတ်ဝန်းကျင်** — တိတ်ဆိတ်သော အခန်း၊ ရှင်းလင်းသော စားပွဲ၊ government ID အသင့်
2. **24 နာရီ ကြိုတင်** — Certiverse software/platform test လုပ်ထားပါ
3. **အချိန် စီမံခန့်ခွဲမှု** — 72 စက္ကန့်/မေးခွန်း；မသေချာပါက flag လုပ်ပြီး ရှေ့ဆက်
4. **Top-10 traps** ကို ဖြေဆိုမီ ပြန်ဖတ်
5. **မေးခွန်း ဖတ်ရှုပုံ** — "အကောင်းဆုံး/အမှားဆုံး/EXCEPT" စကားလုံးများ အထူး ဂရုပြု
6. **Multiple response** — option အားလုံး ဖတ်ပြီးမှ ရွေးချယ်

---

## 8. ဆက်လက် လေ့လာစရာ

- **NCP-AII (Professional: AI Infrastructure)** — cluster deploy/configure (70-75Q, 120min, $400)
- **NCP-AIO (Professional: AI Operations)** — monitoring/troubleshooting

ဤ NCA အဆင့် ခိုင်မာပါက ပရော်ဖက်ရှင်နယ် အဆင့်သို့ တက်ရောက်ရန် အဆင်သင့် ဖြစ်ပါပြီ။

---

## 9. Self-check

1. GPU က CPU ထက် မြန်သည် ဆိုသော အဆို ဘာကြောင့် မှားသလဲ။
2. NVLink သည် scale-up လား scale-out လား၊ node အတွင်းလား ကြားလား။
3. PUE = 1.5 ဆိုပါက IT load ထက် non-IT ကုန်ကျစရိတ် မည်မျှ ပိုသလဲ။
4. Cluster အဆင့် GPU monitoring အတွက် မည်သည့် tool နှင့် pipeline ၃ ဆင့် သုံးသလဲ။
5. Hardware-isolated GPU instance ၇ ခု ခွဲဝေလိုပါက မည်သည့် virtualization နည်းလမ်း ရွေးမလဲ။

**အဖြေများ**

1. Sequential workload တွင် CPU ပိုမြန်ပြီး GPU ၏ အားသာချက်က parallel matrix workload တွင်သာ ဖြစ်သောကြောင့်
2. Scale-up — node အတွင်း GPU ချိတ်ဆက်မှု (cluster fabric မဟုတ်)
3. IT load ၏ 1/3 (non-IT = 0.5×IT) ပိုကုန်ပါသည်
4. DCGM — DCGM Exporter → Prometheus → Grafana
5. MIG — hardware partition (instance အမြင့်ဆုံး 7)

---

## 10. Official References

- NCA-AIIO Exam Guide: https://www.nvidia.com/en-us/learning/certifications/associate-ai-inf-ops/
- Certiverse: https://certiverse.nvidia.com
- NVIDIA AI Infrastructure Fundamentals: https://www.nvidia.com/en-us/training/
- NCP-AII: https://www.nvidia.com/en-us/learning/certifications/professional-ai-infrastructure/
- NCP-AIO: https://www.nvidia.com/en-us/learning/certifications/professional-ai-operations/

**Course ပြီးပါပြီ — စာမေးပွဲ အောင်မြင်ပါစေ!**

---

## Technical review notes

- **DGX B200 = 14.3kW** သည် NVIDIA DGX B200 documentation (system max power) နှင့် ကိုက်ညီကြောင်း အတည်ပြုပြီးဖြစ်ပါသည်။
- **GB200 NVL72 ~120kW** သည် ခန့်မှန်း ပမာဏဖြစ်ပြီး rack config ပေါ် မူတည်၍ ကွဲပြားနိုင်ပါသည် (needs verification)။
- စာမေးပွဲ ဆိုင်ရာ ဂဏန်းများ — **70% pass mark**, **72 စက္ကန့်/မေးခွန်း**, **NCP-AII 70-75Q / 120min / $400** — သည် ပြောင်းလဲနိုင်သောကြောင့် ဖြေဆိုမီ NVIDIA Exam Guide တွင် ထပ်မံ အတည်ပြုပါ (needs verification)။
- Q3 အဖြေ (~84-112GB) သည် 7B param × 12-16 bytes/param အတိုင်း ခန့်မှန်းချက် ဖြစ်ပြီး optimizer/framework အလိုက် ပြောင်းလဲနိုင်ပါသည် (needs verification)။
