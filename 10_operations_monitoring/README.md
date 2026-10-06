# Module 10 — AI DC Management & Monitoring (Domain 3.1, 3.3)

## Blueprint objectives
- 3.1 AI data center management and monitoring essentials
- 3.3 Key measures and criteria for monitoring GPUs

## အဓိက အကြောင်းအရာ
1. **GPU health metrics** — utilization, memory usage/bandwidth, temperature, power draw, clock throttling (SM clock), ECC errors, PCIe/NVLink throughput
2. **DCGM (Data Center GPU Manager)** — cluster-wide telemetry, health checks, policy; dcgmi, DCGM Exporter → Prometheus → Grafana
3. **nvidia-smi** — single-node tool vs DCGM (cluster)
4. **Xid errors** — GPU ရဲ့ error code system (Xid 63 = retirement, Xid 79 = fallen off bus...); dmesg/serial log
5. **BMC / out-of-band management** — node အဆင့် hardware management
6. **Bottleneck ရှာနည်း** — GPU util low → storage? network? dataloader? power/thermal throttling?
7. **Alerting/threshold** — ဘယ် metric ဘယ်အခါ စိုးရိမ်

## လက်တွေ့ (သင့်စက် အစစ်)
- `nvidia-smi -q`, `nvidia-smi dmon`, `dcgmi` (မရှိရင် install) — ကိုယ့် GPU metrics ဖတ်
