# Module 04 — Training vs Inference (ပြည့်စုံသော ရှင်းလင်းချက်)

> ဤ module ၏ ရည်ရွယ်ချက် — training နှင့် inference သည် မည်သို့ ကွာခြားသည်၊ memory/network/power requirement များ မည်သို့ မတူညီရသည် ဆိုသည်ကို နားလည်ပြီး scenario မေးခွန်းများကို မှန်ကန်စွာ ဖြေဆိုနိုင်စေရန် ဖြစ်ပါသည်။

---

## 1. ဤ နှိုင်းယှဉ်ချက် ဘာကြောင့် အရေးကြီးသလဲ

စာမေးပွဲ၏ scenario မေးခွန်း အများစုသည် "ဤ workload သည် training လား inference လား" ကို ခွဲခြားနိုင်မှ ဖြေဆိုနိုင်ပါသည်။ ၂ ခုလုံးသည် GPU အသုံးပြုသော်လည်း architecture requirement များသည် ဆန့်ကျင်ဘက်အထိ ကွာခြားပါသည်။

---

## 2. ပင်မ နှိုင်းယှဉ်ချက်

| အချက် | **Training** | **Inference** |
|---|---|---|
| ရည်ရွယ်ချက် | data မှ weight တွက်ထုတ်ခြင်း | weight သုံး၍ အဖြေ ထုတ်ပေးခြင်း |
| Compute | အလွန်ကြီး (forward + backward) | နည်း (forward only) |
| Memory | weight + gradient + optimizer state | weight + KV cache + activation |
| Network | အလွန်အရေးကြီး (all-reduce) | နည်း (replica/load balance) |
| Latency | ရက်/ပတ် ကြာခွင့်ရ | ms-level SLA (TTFT/TPOT) |
| Precision | mixed (BF16/FP8) | quantization (FP8/INT8/INT4) |
| Failure | checkpoint + ပြန်လည်စတင် | replica restart (သက်သာ) |
| အချိန် သဘော | bursty (project အလိုက်) | 24/7 steady |
| Scaling | data/model/pipeline parallel | replica scale-out |

```mermaid
flowchart TD
    subgraph Training
    T1[GPU များ] <-->|gradient all-reduce| T2[GPU များ]
    T2 <-->|checkpoint| T3[Parallel Storage]
    end
    subgraph Inference
    U[User request] --> LB[Load Balancer]
    LB --> R1[GPU replica 1]
    LB --> R2[GPU replica 2]
    LB --> R3[GPU replica N]
    end
```

---

## 3. Memory Footprint တွက်ချက်နည်း ⭐ (မေးခွန်း အမြဲ ထွက်)

### Training (Adam optimizer)

```
parameter တစ်ခုချင်းအတွက်:
  weight 2 bytes + gradient 2 bytes + optimizer state 8 bytes
  ≈ 12-16 bytes/parameter
```

> **7B model** → 7B × 12-16 bytes ≈ **84-112GB** (activation အပါ) — GPU တစ်လုံးတည်းတွင် မဝင်နိုင်ပါ — distributed training မဖြစ်မနေ လိုအပ်ပါသည်

### Inference

```
weight (bf16 = 2 bytes/param) + KV cache + activation
```

> **7B model** → ~14GB + KV cache — GPU ၁-၂ လုံးဖြင့် လုံလောက်ပါသည်

⚠️ "inference တွင် memory ဘာကြောင့် ကြီးရသေးသလဲ" → **KV cache** ကြောင့် ဖြစ်ပြီး context ရှည်လေ ကြီးလေ ဖြစ်ပါသည်။

![Attention mechanisms comparison](../images/attention-mechanisms.png)
*ပုံ — attention mechanism နှိုင်းယှဉ်ချက် (ရင်းမြစ်: NVIDIA Developer Blog, Mastering LLM Inference Optimization)*

---

## 4. Compute Profile — Compute-bound vs Memory-bound

- **Training** — compute-bound (GEMM ကြီးများ၊ arithmetic intensity မြင့်) — Tensor Core အပြည့် အသုံးပြုနိုင်
- **Inference** — ၂ ပိုင်း ခွဲ ကြည့်ရမည် —
  - **Prefill** — prompt တစ်ခုလုံး တစ်ပြိုင်နက် လုပ်ဆောင်ခြင်း — compute-bound (GEMM)
  - **Decode** — token တစ်လုံးချင်း ထုတ်ခြင်း — **memory-bound** (GEMV — weight + KV cache ဖြတ်သန်းရ)

```mermaid
flowchart LR
    P[Prompt] --> PF[Prefill<br/>compute-bound<br/>TTFT သတ်မှတ်]
    PF --> D[Decode<br/>memory-bound<br/>TPOT သတ်မှတ်]
    D --> O[Output tokens]
```

"decode ဘာကြောင့် နှေးရသလဲ" → **memory bandwidth** ဖြစ်ပြီး GPU compute မဟုတ်ပါ။

---

## 5. Networking — အကြီးမားဆုံး ကွာခြားချက်

**Training** — synchronous **all-reduce** — GPU အားလုံးကြား gradient များ ပေါင်းချုပ်ရပါသည်။ GPU 32 လုံး ပါသော job တစ်ခုသည် step တိုင်းတွင် gradient GB အဆင့် လှုပ်ရှားပြီး network နှေးပါက GPU အားလုံး စောင့်ဆိုင်းကာ utilization ကျဆင်းပါသည် — ထို့ကြောင့် InfiniBand ကဲ့သို့ fabric မဖြစ်မနေ လိုအပ်ပါသည်။

**Inference** — replica တစ်ခုချင်း လွတ်လပ်စွာ run ပြီး load balancer က ခွဲဝေပေးပါသည် (model ကြီး tensor parallel ဆိုပါက node အတွင်း NVLink လိုအပ်)။

---

## 6. Scaling Strategy ကွာခြားချက်

| Training | Inference |
|---|---|
| **Data parallel** — data ခွဲပြီး gradient ပေါင်းချုပ် | **Replica** — service အများ တင်ပြီး load balance |
| **Tensor parallel** — layer တစ်ခုချင်း matrix ခွဲ | tensor parallel (ကြီးမား model အတွက်) |
| **Pipeline parallel** — layer အလိုက် GPU ခွဲဝေ | batch tuning (continuous batching) |

---

## 7. Precision နှင့် Optimization ကွာခြားချက်

- **Training** — mixed precision (BF16 + FP32 master weight)၊ gradient checkpointing၊ FlashAttention
- **Inference** — quantization (FP8/INT8/INT4)၊ distillation၊ caching (KV cache, prefix caching)၊ batching

![LLM optimize and deploy](../images/llm-optimize-deploy.png)
*ပုံ — LLM optimize/deploy workflow (ရင်းမြစ်: NVIDIA Developer Blog)*

---

## 8. Hardware Requirement ခွဲခြားပုံ

**Training cluster** — HBM 80GB+ GPU များ၊ NVLink + InfiniBand fabric၊ checkpoint သိမ်းရန် parallel file system၊ power ကြီးမား

**Inference fleet** — latency သေချာမှု ပထမဦးစားပေး၊ power သက်သာ GPU (L4/L40S) မှ H100 အထိ၊ MIG ဖြင့် sharing၊ replica ဖြင့် ချဲ့ထွင်

**Scenario ဖြေဆိုပုံ** — "training job network တွင် ပိတ်နေ" → fabric ပြဿနာ ဦးစားပေး။ "chatbot response နှေး" → TTFT/TPOT, batch size, replica အရေအတွက် စဉ်းစား။

---

## 9. Failure နှင့် Cost Profile

- **Training failure** — checkpoint အဆင့်အထိ ပြန်လည် ဆုံးရှုံးပြီး GPU-week တန်ဖိုး ကြီးမား — checkpoint အမြဲ သိမ်းဆည်းရပါသည်
- **Inference failure** — replica ပြန်လည် တင်ရုံ — SLA ပျက်စီးမှု ပမာဏသာ
- **Cost** — training = CapEx/bursty၊ inference = OpEx/steady (24/7)

---

## 10. အဓိက မှားယွင်းနိုင်သော အချက်များ

1. "Inference သည် training ၏ အပိုင်းငယ်သာ" — မဟုတ်ပါ — requirement များ သီးခြား
2. Training = network အရေးကြီး၊ Inference = latency အရေးကြီး
3. Memory ခန့်မှန်းပါက gradient + optimizer state မမေ့ပါနှင့်
4. Inference memory = weight + KV cache
5. Batch ကြီးလေ throughput ပိုကောင်းလေ — သို့သော် latency ကျနိုင်

---

## 11. Mini-Quiz

1. Training နှင့် Inference ကို compute, memory, network, latency ၄ ချက်ဖြင့် နှိုင်းယှဉ်ပါ။
2. 7B model အတွက် training memory ခန့်မှန်းချက် မည်မျှရှိသလဲ (Adam)။
3. Training cluster တွင် ဘာကြောင့် InfiniBand လိုအပ်ရသလဲ။
4. Prefill နှင့် Decode အနက် မည်သည့်အဆင့်က memory-bound လဲ။

**အဖြေများ** — 1) Training: compute/network ကြီး/ကြာခွင့်ရ၊ Inference: latency SLA/weight+KV · 2) ~84-112GB · 3) all-reduce ကြောင့် GPU အားလုံး စောင့်ဆိုင်းမှု မဖြစ်စေရန် · 4) **Decode**

---

## 12. Official References

- Mastering LLM Techniques — Inference Optimization: https://developer.nvidia.com/blog/mastering-llm-techniques-inference-optimization/
- NVIDIA Transformer Engine: https://developer.nvidia.com/transformer-engine
- NVIDIA AI Enterprise: https://www.nvidia.com/en-us/data-center/products/ai-enterprise/
