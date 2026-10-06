# Module 06 — Power, Cooling & Facility (Domain 2.3, 2.6)

## Blueprint objectives
- 2.3 Power and cooling requirements within a datacenter
- 2.6 Facility requirements

## အဓိက အကြောင်းအရာ
1. **Power density** — DGX B200 တစ်လုံး ~14.3kW; rack တစ်ခုက office အဆောက်အဦ တစ်ခုလောက် စားနိုင်
2. **Rack density** — kW/rack; power delivery (3-phase), PDU, redundancy (N+1)
3. **Cooling** — air cooling vs liquid cooling (direct-to-chip, rear-door heat exchanger); GPU-dense ဖြစ်လာရင် liquid လို
4. **Thermal metrics** — inlet/outlet temp, Delta T; hot aisle/cold aisle containment
5. **Facility** — floor loading (rack အလေးချိန်), space, power availability, cooling capacity, network cabling
6. **Energy efficiency** — PUE; efficiency ရဲ့ စီးပွားရေး သက်ရောက်မှု
7. **On-prem planning** — အခြေခံ building requirement များ

## လက်တွေ့
- သင့် laptop RTX 5070 Ti (140W) vs DGX B200 (14.3kW) — ၁၀၀ ဆ ကွာခြားပုံ တွက်ကြည့်
