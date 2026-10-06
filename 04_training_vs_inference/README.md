# Module 04 — Training vs Inference (Domain 1.2)

## Blueprint objective
- 1.2 Compare and contrast training and inference architecture requirements

## နှိုင်းယှဉ်ချက် (exam မှာ အမြဲ ထွက်)
| | Training | Inference |
|---|---|---|
| Compute | အလွန်ကြီး (GEMM, backprop) | နည်း (GEMV forward) |
| Memory | weights + gradients + optimizer state (2-8x) | weights + KV cache |
| Network | **အလွန်အရေးကြီး** (all-reduce) | အနည်းငယ် |
| Scale | multi-node, multi-GPU | single GPU → scale-out replicas |
| Latency | အချိန်ကြာခွင့်ရ | TTFT/TPOT SLA |
| Precision | mixed precision | quantization (FP8/INT8/INT4) |
| Failure cost | checkpoint/restart | replica ပြန်တင် |

## အခြား အကြောင်း
- **Batch size / gradient accumulation** effect
- **Checkpointing, storage I/O** — training မှာ ဘာကြောင့် စားသုံး
- **Inference workload အမျိုးအစား** — real-time chat vs offline batch
- Roofline: prefill compute-bound / decode memory-bound (SGLang ch.2 နဲ့ ဆက်)

## လက်တွေ့ (သင့်စက်)
- Qwen3-0.6B serve (SGLang) = inference; နှိုင်းယှဉ်ချက်ကို ကိုယ်ပိုင် benchmark နဲ့ ချိတ်
