# Module 11 — Orchestration & Job Scheduling (Domain 3.2)

## Blueprint objective
- 3.2 AI cluster orchestration and job scheduling essentials

## အဓိက အကြောင်းအရာ
1. **Kubernetes** — containerized, inference/mixed workloads; Pod, Deployment, Job; `nvidia.com/gpu` resource
2. **GPU Operator** — driver, device plugin, toolkit, monitoring ကို automate (node တိုင်း manual မလုပ်ရ); **Network Operator** (fabric)
3. **Slurm** — HPC/batch training အတွက်; job queue, partition, sacct/squeue
4. **Run:ai / Base Command** — GPU sharing/scheduling platform (multi-tenant)
5. **Job scheduling concept** — queue, priority, preemption, gang scheduling (multi-GPU job)
6. **NGC containers** — registry ကနေ ဆွဲ run

## သင့်အတွက် အားသာချက်
- k8s (drlinuxer-prod) + kubectl ကျွမ်းကျင်ပြီး — GPU Operator/device plugin concept ချိတ်ရုံ
