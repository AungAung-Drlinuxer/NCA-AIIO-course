# Module 06 — Power, Cooling & Facility (ပြည့်စုံသော ရှင်းလင်းချက်)

> ဤ module ၏ ရည်ရွယ်ချက် — GPU-dense data center ၏ power/cooling/facility လိုအပ်ချက်များကို နားလည်ပြီး rack density, PUE, redundancy ကဲ့သို့ သော့ချက် ဂဏန်းများဖြင့် တွက်ချက် ဆုံးဖြတ်နိုင်စေရန် ဖြစ်ပါသည်။

---

## 1. ပြဿနာ — GPU Rack များ ရိုးရာ Data Center နှင့် ဘာကြောင့် မကိုက်ရသလဲ

IT professional အများစုက power/cooling သည် facility team ၏ အလုပ်ဟု ယူဆထားလေ့ရှိသော်လည်း GPU rack တစ်ခုသည် web server rack တစ်ခုထက် ပါဝါ ၁၀-၂၀ ဆ ပိုမို စားသုံးပါသည်။

```
ရိုးရာ web server rack      ~5-10 kW
DGX H100 တစ်လုံး            10.2 kW  (rack တစ်ခုလုံးထက် ကြီး!)
DGX B200 တစ်လုံး            14.3 kW
GB200 NVL72 rack တစ်ခု      ~120 kW
```

ထို့ကြောင့် GPU-dense infrastructure သည် ရိုးရာ data center ဒီဇိုင်းနှင့် မကိုက်ညီဘဲ ပြန်လည် စဉ်းစား ဒီဇိုင်းဆွဲရပါသည်။ rack တစ်ခုတည်း၏ ပါဝါ မြင့်တက်လာသည်နှင့် အမျှ power distribution, cooling နှင့် floor loading ပြဿနာများ တစ်ပြိုင်နက် ရောက်ရှိလာပါသည်။

---

## 2. အဓိက သဘောတရား — Power ဆိုင်ရာ အခြေခံများ

### Power Density (kW/rack)
- ရိုးရာ data center — rack တစ်ခု 5-10kW
- GPU-dense — 40-130kW/rack → ရိုးရာ facility တွင် မဝင်နိုင်

### Power Distribution Chain

```mermaid
flowchart LR
    G[Grid 3-phase power] --> U[UPS<br/>မီးပြတ် ကာကွယ်]
    U --> P[PDU<br/>rack အတွင်း ဖြန့်ဝေ]
    P --> S[Server PSU]
    S --> GPU[GPU + CPU + Memory]
```

- **3-phase power** — GPU rack များအတွက် လိုအပ်
- **PDU (Power Distribution Unit)** — rack အတွင်း ပါဝါ ဖြန့်ဝေပေး
- **UPS (Uninterruptible Power Supply)** — မီးပြတ်ကာကွယ်ခြင်း
- **Redundancy** — **N+1** (လိုအပ်သော အရေအတွက် + ၁ လုံး အပို) vs **2N** (ပြည့်စုံသော အရေအတွက် တစ်ဆ ထပ်)

⚠️ N+1 vs 2N ကို စာမေးပွဲတွင် ခွဲခြား မေးလေ့ရှိပါသည် — N+1 = အနည်းဆုံး redundancy၊ 2N = အပြည့်အဝ redundancy (ကုန်ကျ ၂ ဆ)။

---

## 3. နှိုင်းယှဉ်ချက် နှင့် တွက်ချက်နည်းများ

### Power Budget တွက်ချက်နည်း

```
စုစုပေါင်း = GPU အရေအတွက် × GPU TDP + server overhead + cooling
```

> **ဥပမာ** — DGX H100 ၄ လုံးပါ rack: 4 × 10.2 ≈ 41kW + cooling overhead → rack အတွက် 50kW+ ပါဝါ လိုအပ်ပါသည်

### PUE (Power Usage Effectiveness) ⭐

```
              facility စုစုပေါင်း ပါဝါ
PUE  =  ─────────────────────────────
              IT equipment ပါဝါ
```

| PUE | အဓိပ္ပာယ် |
|---|---|
| 1.0 | perfect (cooling/lighting ဘာမှ မစား) |
| 1.1-1.2 | ခေတ်မီ / liquid cooling |
| 1.5-2.0 | ရိုးရာ data center |

> **တွက်ချက် ဥပမာ** — IT load 326kW၊ PUE 1.2 → facility 391kW (non-IT 65kW)။ PUE 1.5 → facility 489kW (non-IT 163kW — စုစုပေါင်း၏ 1/3)

### Cooling နည်းလမ်း နှိုင်းယှဉ်ချက်

| နည်းလမ်း | လုပ်ဆောင်ပုံ | အသုံးပြုချိန် |
|---|---|---|
| **Air cooling (hot/cold aisle)** | လေ စီးဆင်းမှုဖြင့် အပူဖယ်ရှား | ~30-50kW/rack အထိ |
| **Direct-to-chip (cold plate)** | GPU heat sink ပေါ်တွင် ရေ စီးဆင်းစေခြင်း | 50kW+/rack (GB200 = ဤနည်း) |
| **Rear-door heat exchanger** | rack တံခါးတွင် liquid coil | air-cooled room upgrade |
| **Immersion** | စက်တစ်ခုလုံး liquid အတွင်း နှစ်မြှုပ် | extreme density / edge case |

---

## 4. Cooling — Air မှ Liquid သို့ ကူးပြောင်းခြင်း

```mermaid
flowchart TD
    A[rack density တိုင်းတာ] --> B{< 20kW?}
    B -->|ဟုတ်| C[Air cooling<br/>hot/cold aisle]
    B -->|20-40kW| D[Air + containment<br/>hot aisle/cold aisle ကာရံ]
    B -->|40-50kW+| E[Liquid cooling<br/>မဖြစ်မနေ]
    E --> E1[Direct-to-chip cold plate<br/>GB200 = ဤနည်း]
    E --> E2[Rear-door heat exchanger]
    E --> E3[Immersion]
```

### Air Cooling
- ရိုးရာ နည်းလမ်း — ~30-50kW/rack အထိသာ တတ်နိုင်
- **Hot aisle / cold aisle** — အေးသော လေနှင့် ပူသော လေ ခွဲခြားပြီး containment လုပ်ပါက ပိုမို ထိရောက်
- **ASHRAE guideline** — inlet temperature ~18-27°C အတွင်း ထားရှိ

### Thermal Throttling

GPU အပူချိန် မြင့်တက်လာပါက GPU သည် clock speed ကို အလိုအလျောက် လျှော့ချ (throttle) ပြီး performance ကျဆင်းသွားပါသည်။

```
အပူချိန် ↑ → clock ↓ → performance ↓ (တိတ်တဆိတ်!)
```

"အလုပ် နှေးလာ" ဆိုသော scenario များတွင် ပထမဆုံး စစ်ဆေးရမည့် အချက်များအနက် တစ်ခု ဖြစ်ပါသည်။

---

## 5. လက်တွေ့ စီစဉ်ခြင်း — Facility Requirements နှင့် တွက်ချက်မှု (Objective 2.6)

### Facility Requirements

1. **Power availability** — grid မှ ပါဝါ ရရှိနိုင်မှု (GPU pod = MW အဆင့်)၊ redundancy
2. **Cooling capacity** — chiller, cooling tower, water supply၊ PUE ပန်းတိုင်
3. **Floor loading** — GPU rack များ အလေးချိန် 1,500kg+ အထိ — ကြမ်းခင်း ခံနိုင်ရည်
4. **Space & layout** — rack အရေအတွက်၊ expansion လမ်းကြောင်း
5. **Network cabling** — fiber လမ်းကြောင်း၊ cross-connect
6. **Physical security** — access control, CCTV
7. **Fire suppression** — gas-based system (ရေဖြင့် မရ — လျှပ်စစ်ပစ္စည်း)
8. **Out-of-band management** — BMC network
9. **Seismic / weight** — တည်နေရာ ပေါ် မူတည်

### လက်တွေ့ တွက်ချက်ကြည့်ခြင်း

**မေးခွန်း** — DGX H100 ၃၂ လုံး ဝယ်မည်။ rack တစ်ခုတွင် ၄ လုံး ထည့်မည်။

- စုစုပေါင်း power = 32 × 10.2kW ≈ 326kW (cooling ပါ ဆို ~400kW)
- Rack ၈ ခု၊ တစ်ခုချင်း ~41kW → **liquid cooling လိုအပ်**
- PUE 1.2 ဆိုပါက facility ပါဝါ = 326 × 1.2 ≈ 391kW — ငယ်ရွယ်သော စက်ရုံ တစ်ခုလောက် စားသုံးမှု

### GPU အပူချိန်/ပါဝါ စစ်ဆေးရန် (ဥပမာ command များ — GPU/driver version ပေါ် မူတည်)

```bash
# GPU တစ်လုံးချင်း၏ အပူချိန်၊ ပါဝါ စားသုံးမှု၊ clock speed ကို အနှစ်ချုပ် ကြည့်ရန်
nvidia-smi -q -d TEMPERATURE,POWER,CLOCK

# ပါဝါ/utilization/clock ကို အချိန်နှင့် အမျှ စောင့်ကြည့်ရန် (throttling သံသယရှိပါက)
nvidia-smi dmon -s puc
```

Temperature limit ကျော်ပြီး clock ကျနေပါက thermal throttling ဟု သံသယဝင်နိုင်ပြီး airflow/rack density/ambient/filter ကို ဆက်လက် စစ်ဆေးပါ။

---

## 6. စစ်ဆေးရန် အချက်များ (Exam traps)

1. kW/rack — 5-10 (ရိုးရာ) vs 40-130 (GPU)
2. N+1 vs 2N redundancy ခွဲခြားပါ
3. PUE ပုံသေနည်း — 1.0 က perfect
4. GPU-dense တွင် air မှ liquid သို့ ပြောင်းရခြင်း
5. Thermal throttling = performance ကျဆင်းခြင်း ၏ အကြောင်းရင်း
6. Floor loading — facility requirement မေးခွန်းများတွင် ပါဝင်

### ထပ်ဆင့် လေ့ကျင့်ခန်း

1. ရိုးရာ rack 8kW ဆိုပါက DGX H100 rack (~41kW) သည် ဆယ်ဆ မည်မျှ ပိုစားသုံးသလဲ — ≈ 5 ဆ
2. IT load 200kW၊ PUE 1.15 → facility = 230kW (cooling/lighting 30kW)
3. GPU အပူချိန် limit 83°C၊ လက်ရှိ 85°C — အရင် စစ်ဆေးရန်: airflow, rack density, ambient, filter

---

## 7. Self-check (Mini-Quiz)

1. GB200 NVL72 rack (~120kW) သည် web server rack (5kW) ထက် ဆယ်ဆ မည်မျှ ပိုစားသုံးသလဲ။
2. PUE 1.5 ဆိုပါက facility ပါဝါ ထဲတွင် cooling/lighting အတွက် မည်မျှ ရာခိုင်နှုန်း ကုန်သလဲ။
3. Rack density မည်မျှ ကျော်လျှင် liquid cooling စဉ်းစားသင့်သလဲ။
4. N+1 နှင့် 2N redundancy ကွာခြားချက် တစ်ချက် ပြောပါ။

**အဖြေများ** — 1) ~24 ဆ · 2) 33% · 3) ~40-50kW/rack · 4) N+1 = လိုအပ်သည့် အရေအတွက် + ၁ လုံး (အနည်းဆုံး redundancy)၊ 2N = ပြည့်အရေအတွက် တစ်ဆ ထပ် (အပြည့် redundancy၊ ကုန်ကျ ၂ ဆ)

---

## 8. Official References

- NVIDIA DGX B200 Datasheet: https://www.nvidia.com/en-us/data-center/dgx-b200/
- NVIDIA GB200 NVL72: https://www.nvidia.com/en-us/data-center/gb200-nvl72/
- ASHRAE Data Center Guidelines: https://www.ashrae.org/technical-resources/bookstore/datacom-equipment-book-series
- NVIDIA Liquid Cooling: https://www.nvidia.com/en-us/data-center/liquid-cooling/

---

## Technical review notes

- PUE တွက်ချက် ဥပမာ ဂဏန်းများကို ပြန်လည် တွက်ချက် အတည်ပြုပြီး ဖြစ်ပါသည် (326 × 1.2 ≈ 391kW, non-IT ≈ 65kW; 326 × 1.5 ≈ 489kW, non-IT ≈ 163kW = စုစုပေါင်း၏ 1/3)။ ဤ ဂဏန်းများသည် သရုပ်ပြ ဥပမာ ဖြစ်ပြီး တိုင်းတာမှု မဟုတ်ပါ။
- DGX H100 10.2kW၊ DGX B200 14.3kW များကို NVIDIA datasheet max figures အဖြစ် အတည်ပြုပြီး ဖြစ်ပါသည် (verified)။ GB200 NVL72 ~120kW သည် NVIDIA nominal figure ဖြစ်ပြီး vendor system အလိုက် 125-135kW အထိ ကွာခြားနိုင်ပါသည် (needs verification per vendor)။
- Air cooling ~30-50kW/rack ကန့်သတ်ချက်နှင့် 20-40kW/40-50kW+ ဆုံးဖြတ်ချက် အဆင့်များသည် အသုံးများသော rule-of-thumb ဖြစ်ပြီး facility design guide တစ်ခုချင်းအလိုက် ကွာခြားနိုင်ပါသည် (needs verification)။
- ASHRAE 18-27°C သည် recommended inlet temperature range (A1 class) အဖြစ် ဖော်ပြလေ့ရှိပြီး လက်ရှိ guideline ဗားရှင်းနှင့် ပြန်စစ်ပါ (needs verification)။
- Floor loading 1,500kg+ နှင့် rack power ဥပမာ ဂဏန်းများသည် ခန့်မှန်းချက် ဖြစ်ပြီး rack/rail config ပေါ် မူတည်ပါသည်။
