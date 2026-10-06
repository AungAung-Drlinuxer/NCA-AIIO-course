# Module 07 — Networking for AI (Domain 2.7, 2.8, 2.9)

## Blueprint objectives
- 2.7 Networking requirements for AI workloads
- 2.8 DC networking protocols and key concepts
- 2.9 High-speed DC network options and use cases

## အဓိက အကြောင်းအရာ
1. **AI networking requirements** — training မှာ ဘာကြောင့် bandwidth/latency အရေးကြီး (all-reduce, gradient sync); east-west traffic
2. **Protocols/concepts** — TCP/IP vs **RDMA**; **RoCE**, **InfiniBand verbs**; lossless network, congestion control, PFC/ECN
3. **InfiniBand** — NDR/XDR (400/800Gb/s), SHARP in-network compute, rail-optimized topology
4. **Ethernet option** — **Spectrum-X** (AI-optimized Ethernet), RoCEv2
5. **Topology** — fat-tree/rail-optimized; oversubscription ratio
6. **Storage network** — parallel file systems, GPUDirect Storage (data ကို CPU ကြားခံမပါ GPU ဆီ တိုက်ရိုက်)
7. **Management network** — out-of-band (BMC) vs in-band

## Exam trap
- NVLink/NVSwitch = **scale-up (node အတွင်း)**; InfiniBand/Spectrum-X = **scale-out (node ကြား)** — မမှားရ
