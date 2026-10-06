# Module 03 — Solution

1. CUDA = GPU parallel computing platform/API (အခြေခံ); cuDNN = DNN primitive library (conv/attention); cuBLAS = matrix multiply (BLAS); NCCL = multi-GPU collective communication (all-reduce)။
2. TensorRT = model ကို GPU အတွက် optimize/compile engine; Triton = model တွေကို HTTP/gRPC နဲ့ serve တဲ့ server (dynamic batching, multi-model)။ production serve = Triton (ထဲမှာ TensorRT backend ပါနိုင်)။
3. NIM = pre-optimized model + runtime ပါတဲ့ Docker microservice (AI Enterprise ပါ); Triton က generic serving platform, NIM က "ဖွင့်ပြီး သုံးရုံ" package။ enterprise မှာ မြန်မြန် တင်ချင်ရင် NIM။
4. NCCL (all-reduce)။
5. Data (RAPIDS) → Train (NeMo/PyTorch) → Optimize (TensorRT) → Serve (Triton/NIM) → Monitor (DCGM)။
6. Base Command Manager = on-prem DGX cluster OS (provisioning/scheduling); Base Command Platform = cloud-managed platform service။

# Mini-quiz
1. Catalog/registry (container, model, notebook) — tool မဟုတ်။
2. NeMo (TAO = vision/speech transfer learning)။
3. TensorRT။
