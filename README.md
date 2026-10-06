# NCA-AIIO Prep Course — AI Infra & Ops Associate

> ရည်ရွယ်ချက် — NVIDIA NCA-AIIO certification exam အောင်မြင်ရန် မြန်မာ-အင်္ဂလိပ် လေ့လာရေး course

## 📋 Exam အကျဉ်း
- 50 questions / 60 min, online proctored (Certiverse), **$125**, 2 years validity
- Domains: Essential AI Knowledge **38%** / AI Infrastructure **40%** / AI Operations **22%**
- Pass mark မထုတ်ပြန် — practice tests မှာ **70%+** ထိ တက်အောင် လေ့ကျင့်

## 🗺️ 4-Week Study Roadmap

### Week 1 — Essential AI Knowledge (38%) — အခြေခံ ခိုင်မာစေ
| နေ့ | အလုပ် |
|---|---|
| Day 1-2 | Module 00 + 01 — exam overview, AI/ML/DL, use cases |
| Day 3-4 | Module 02 — GPU vs CPU, Tensor Core, precision, GPU generations |
| Day 5-6 | Module 03 — NVIDIA software stack (CUDA→cuDNN→NCCL→TensorRT→Triton→NIM→NeMo→RAPIDS→NGC→AI Enterprise) |
| Day 7 | Module 04 — Training vs Inference + week review quiz |
**Official course** (NVIDIA AI Infra & Ops Fundamentals, free ~7h) ကို ဒီအပတ်ထဲ ပြီးအောင် ဖတ်

### Week 2 — AI Infrastructure (40%) — အလေးချိန် အများဆုံး
| နေ့ | အလုပ် |
|---|---|
| Day 1-2 | Module 05 — DGX/HGX, NVLink/NVSwitch vs InfiniBand, scale-up vs scale-out |
| Day 3 | Module 06 — power/cooling (kW/rack, liquid cooling), facility |
| Day 4-5 | Module 07 — networking (RDMA/RoCE/IB, Spectrum-X, topology, GPUDirect Storage) |
| Day 6 | Module 08 (DPU) + 09 (cluster, on-prem vs cloud) |
| Day 7 | Week review + **networking flashcards** (exam trap အများဆုံး ဒီမှာ) |

### Week 3 — AI Operations (22%) + Review
| နေ့ | အလုပ် |
|---|---|
| Day 1-2 | Module 10 — DCGM, nvidia-smi, Xid, bottleneck diagnosis |
| Day 3-4 | Module 11 — K8s GPU Operator, Slurm, Run:ai, Base Command |
| Day 5 | Module 12 — MIG vs vGPU vs time-slicing |
| Day 6-7 | Module 13 — full review, weak-spot list |

### Week 4 — Practice Exams
- Practice exam 3+ စုံ (50Q/60min timing) — **70%+** မရမချင်း ပြန်ဖြေ
- အမှား log ထား — domain အလိုက် score tracking
- Exam-day setup (webcam/ID/room) ပြင်ဆင်

## ⚡ လက်တွေ့ လုပ်ဆောင်ချက်များ (သင့်စက် အစစ်နဲ့)
1. `nvidia-smi -q` / `dmon` — GPU metrics ဖတ်နည်း (Module 10)
2. `nvidia-smi topo -m` — topology (Module 05)
3. SGLang server ကို DCGM/nvidia-smi နဲ့ စောင့်ကြည့် (Module 10-11)
4. k8s cluster ပေါ်မှာ GPU resource request concept ပြောပြ (Module 11)

## 📚 အဓိက resource များ
1. **NVIDIA AI Infrastructure and Operations Fundamentals** (free) — အဓိက course
2. **Official Exam Study Guide PDF** — topic list
3. Suggested readings: DCGM docs, Base Command, BMC, K8s docs, Slurm, Docker, MIG, Run:ai blog
