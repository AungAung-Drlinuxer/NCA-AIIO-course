# Module 03 — NVIDIA Software Stack + Solutions (Domain 1.1, 1.6, 1.7)

## Blueprint objectives
- 1.1 Describe the NVIDIA software stack used in an AI environment
- 1.6 Purpose and use case of various NVIDIA solutions
- 1.7 Software components of AI development and deployment lifecycle

## Layer အလိုက် stack (exam ရဲ့ အသည်းနှလုံး)
| Layer | Tool | ဘာလုပ် |
|---|---|---|
| Low-level | **CUDA** | GPU programming platform |
| Libraries | **cuDNN** (DL primitives), **NCCL** (multi-GPU comm), **cuBLAS** | kernels/collections |
| Training | **NeMo** (LLM framework), **TAO Toolkit** | model တည်ဆောက် |
| Inference optimize | **TensorRT** | model ကို GPU အတွက် optimize/compile |
| Serving | **Triton Inference Server**, **NIM** (microservice) | model serve/batch |
| Data | **RAPIDS** | GPU dataframe/ML |
| Platform | **NGC** (container/model catalog), **AI Enterprise** (support), **Base Command** (cluster mgmt), **LaunchPad** | ecosystem |

## AI Lifecycle ဆက်စပ်မှု
Data → Train (NeMo/PyTorch) → Optimize (TensorRT) → Serve (Triton/NIM) → Monitor (DCGM)

## NVIDIA Solutions
- **DGX** (complete system) vs **HGX** (baseboard for OEM) vs **IGX** (edge) vs **MGX** (modular)
- **DGX BasePOD / SuperPOD** — reference architecture အဆင့်ဆင့်
- **NVIDIA AI Enterprise** — production-grade support/SLA

⚠️ Exam trap: **TensorRT vs Triton vs NIM**, **Base Command Manager vs Platform**, **NGC** — တစ်ခုချင်း တစ်ကြောင်းတည်း ပြောနိုင်ရမည်
