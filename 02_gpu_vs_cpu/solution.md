# Module 02 — Solution

1. CPU = few complex cores, latency နိမ့် (serial မြန်); GPU = thousands of simple cores, throughput မြင့် (parallel မြန်)။ "GPU မြန်" ဆိုတာ parallel arithmetic မှာပဲ — sequential/branch-heavy task မှာ CPU က မြန်ဆဲ။
2. CUDA Core = general FMA; Tensor Core = matrix multiply-accumulate သီးသန့်။ LLM က matrix operation မို့ Tensor Core က အဓိက။
3. HBM = stacked, wide bus, TB/s အမြန်နှုန်း (data center); GDDR = narrow bus (consumer)။ decode က weight+KV ရွှေ့တာ memory-bound မို့ bandwidth က တိုက်ရိုက် အမြန်နှုန်း သတ်မှတ်။
4. FP16 = precision ကောင်း, range ကျဉ်း; BF16 = FP32 range ပါ, precision နည်း — training မှာ overflow နည်းလို့ BF16 သုံး။
5. A100: 80GB HBM2e/2TB/s/NVLink 600GB/s; H100: 80GB HBM3/3.35TB/s/900GB/s/FP8 Transformer Engine; H200: 141GB HBM3e/4.8TB/s; B200: 180GB/~8TB/s/NVLink 1.8TB/s/FP4/dual-die။
6. 16GB GDDR7 vs 80GB HBM; ECC မပါ; MIG/vGPU မရ; NVLink မရ; RAS/certified driver မပါ; single workstation vs cluster; SM120 consumer die vs data center die; power 140W vs 400W+။

# Mini-quiz
1. **MIG** (Multi-Instance GPU) — up to 7 isolated instances
2. **H200** (141GB HBM3e) — memory ပိုများ
3. **Intra-node** (node အတွင်း scale-up); inter-node အတွက် InfiniBand/Spectrum-X
