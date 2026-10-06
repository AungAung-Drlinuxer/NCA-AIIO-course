# Module 12 — Virtualizing Accelerated Infrastructure (Domain 3.4)

## Blueprint objective
- 3.4 Key considerations for virtualizing accelerated infrastructure

## အဓိက အကြောင်းအရာ
1. **Bare metal vs VM vs container** — GPU access ပုံစံ (passthrough vs virtualized)
2. **MIG (Multi-Instance GPU)** — GPU ကို hardware-isolated instance မျိုး ခွဲ (A100/H100: 7 instances); isolation, fixed slices
3. **vGPU (GRID)** — time-sliced, mediated; VDI/multi-tenant
4. **Time-slicing** — sharing အရိုးရှင်းဆုံး (isolation မရှိ)
5. **ဘယ်အခါ ဘာသုံး** — MIG: stable partition; vGPU: VM/VDI; time-slicing: bursty/dev
6. **Considerations** — memory isolation, scheduling overhead, driver/licensing
