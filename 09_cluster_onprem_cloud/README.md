# Module 09 — Cluster Components & On-prem vs Cloud (Domain 2.4, 2.5)

## Blueprint objectives
- 2.4 On-prem vs cloud: advantages, challenges, considerations
- 2.5 Key components and considerations of a cluster of accelerated infrastructure

## အဓိက အကြောင်းအရာ
1. **Cluster ရဲ့ component များ** — compute node, network fabric, storage, management node, cooling/power, scheduler
2. **On-prem** — CapEx, control/customization, data sovereignty; စိန်ခေါ်မှု: power/cooling, upfront cost, staffing
3. **Cloud** — OpEx, elasticity, speed; စိန်ခေါ်မှု: egress cost, data gravity, quota, long-run cost
4. **Hybrid / reserved vs spot** consideration
5. **Reference architecture** — BasePOD/SuperPOD blueprint အတိုင်း ဆောက်ခြင်း vs custom
6. **Planning** — capacity planning, growth, power budget, TCO

## သင့်အတွက်
- သင့် drlinuxer-prod k8s cluster နဲ့ laptop — on-prem vs "local" experience ချိတ်
