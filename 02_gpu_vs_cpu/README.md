# Module 02 — GPU vs CPU Architecture (Domain 1.8)

## Blueprint objective
- 1.8 Compare and contrast GPU and CPU architectures

## အဓိက အကြောင်းအရာ
1. **CPU** — core နည်း, complex logic, low latency (sequential မြန်) | **GPU** — core များ, simple, high throughput (parallel မြန်)
2. **GPU ရဲ့ အဆောက်အဦ** — SM, CUDA core, **Tensor Core** (matrix အတွက် သီးသန့်), memory hierarchy
3. **SIMT / warp** — warp divergence ဘာကြောင့် ဆိုးလဲ
4. **Memory bandwidth အရေးကြီးမှု** — HBM vs GDDR; 70B model weight ရွှေ့ပုံ
5. **Precision formats** — FP32/FP16/BF16/FP8/INT8/INT4 — Tensor Core နဲ့ ဘယ်လို ကိုက်ညီ
6. **GPU မျိုးဆက်** — Ampere (A100) → Ada (L40S) → Hopper (H100/H200) → **Blackwell (B200/GB200)** — ဘာတွေ တိုးလာ

## သင့်စက်နဲ့ ဆက်စပ်
- RTX 5070 Ti (Blackwell consumer, SM120) — data center Blackwell (B200) နဲ့ ဘာကွာ
- SGLang-course Part I ch.3 (GPU) ဖတ်ပြီးသား — ဒီ module က အဲဒီကို exam-focused ပြန်ဖြည့်
