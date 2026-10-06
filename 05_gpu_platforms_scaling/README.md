# Module 05 — GPU Platforms & Scaling (Domain 2.1, 2.2)

## Blueprint objectives
- 2.1 Hardware requirements for specific AI training task use cases
- 2.2 Scale a GPU infrastructure for different use cases

## အဓိက အကြောင်းအရာ
1. **Platform ရွေးချယ်မှု** — DGX (turnkey) vs HGX (OEM baseboard) vs MGX (modular) vs IGX (edge)
2. **DGX မျိုးဆက်** — DGX A100/H100/H200/B200/GB200 — GPU count, memory, bandwidth, power
3. **Scale-up vs Scale-out** (⚠️ exam trap အမြဲ)
   - **Scale-up** = node အတွင်း/rack အတွင်း — **NVLink, NVSwitch** (TB/s အမြန်နှုန်း)
   - **Scale-out** = node ကြား — **InfiniBand, Spectrum-X Ethernet** (Gb/s)
4. **Multi-GPU communication** — NCCL, all-reduce; data parallel vs model parallel vs pipeline parallel
5. **Sizing** — training အတွက် ဘယ်နှစ် GPU (GPU-weeks, memory per GPU vs model size), inference အတွက် replicas/TPOT
6. **BasePOD vs SuperPOD** — reference architecture အဆင့် (node → rack → pod → superpod)

## လက်တွေ့
- nvidia-smi topo ဖြင့် သင့်စက်ရဲ့ 1-GPU topology ကြည့်
