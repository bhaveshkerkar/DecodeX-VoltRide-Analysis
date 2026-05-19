# DecodeX 2026 — Case GRIDSHIELD

### Constrained Financial Optimization Under Regulatory Shock

**Team:** CodeXplorers | **Competition:** DecodeX 2026 Finale, NLD Synapse  
**Host:** N.L. Dalmia Institute of Management Studies & Research  
**Duration:** 22 continuous hours (28 Feb – 01 Mar 2026)

---

## Overview

Case GRIDSHIELD simulated a real-world advisory engagement for **Lumina Energy**,
a Power Distribution Company operating a suburban Mumbai distribution zone under
Maharashtra's **Availability Based Tariff (ABT)** regulations.

The core challenge: submit a **2-day ahead electricity load forecast at 15-minute
resolution** to the State Load Dispatch Centre (SLDC) — while minimising financial
penalty exposure under an asymmetric regulatory penalty structure.

> **Key Insight:** This is not a statistical forecasting problem.  
> It is a **financial exposure minimisation problem under regulatory constraint.**

---

## Problem Statement

Under ABT regulations, deviations from submitted load schedules attract penalties:

| Condition                          | Stage 1 Penalty | Stage 2 Penalty (Peak) |
| ---------------------------------- | --------------- | ---------------------- |
| Under-forecast (Actual > Forecast) | ₹4 / kWh        | ₹6 / kWh (6 PM–10 PM)  |
| Over-forecast (Forecast > Actual)  | ₹2 / kWh        | ₹2 / kWh               |

**Peak Hours:** 6:00 PM – 10:00 PM  
**Asymmetry:** Under-forecasting costs 2×–3× more than over-forecasting

---

## Competition Structure

| Stage   | Time               | Description                                                    |
| ------- | ------------------ | -------------------------------------------------------------- |
| Stage 1 | 28 Feb \| 11:00 AM | Baseline dataset released. Build cost-aware forecasting model. |
| Stage 2 | 28 Feb \| 7:00 PM  | Regulatory shock — peak penalty escalated ₹4 → ₹6/kWh.         |
| Interim | 28 Feb \| 11:00 PM | 5-slide recalibration brief submitted.                         |
| Stage 3 | 01 Mar \| 1:00 AM  | Confidential Board Directive — 4 binding constraints imposed.  |
| Final   | 01 Mar \| 9:00 AM  | Executive deck + technical appendix submitted.                 |

---

## Our Approach

### Stage 1 — Baseline Cost-Aware Forecasting

Built an **XGBoost Regressor** trained on historical load data (2013–2019),
validated on 2020, tested out-of-time on 2021.

**Feature Engineering (20+ features):**

- **Lag features:** 15-min, 1-hour, 1-day, 7-day backward lags
- **Rolling statistics:** 24-hour rolling mean, 4-hour trend, 24-hour volatility (std)
- **Cyclical encodings:** sin/cos transformation of hour-of-day for continuity
- **Time indicators:** hour, day-of-week, month, weekend flag, peak-hour flag
- **External drivers:** Temperature, Heat Index, Humidity, Rainfall, Cool Factor, Holiday indicator

**Bias Strategy:**  
Since under-forecasting (₹4) costs 2× over-forecasting (₹2), the optimal
forecast quantile is 4/(4+2) = **67th percentile**. We introduced a controlled
+0.35% upward bias, achieving an **86% penalty reduction** vs the naive seasonal baseline.

**Leakage Controls:**

- Strict chronological split — no random shuffling
- All lag/rolling features use backward-only windows
- No test data used during hyperparameter tuning

---

### Stage 2 — Regulatory Shock & Recalibration

At 7:00 PM, peak under-forecast penalty escalated from ₹4 → ₹6/kWh (asymmetry 2:1 → 3:1).

**Without recalibration:**
| Metric | Value |
|--------|-------|
| Total Penalty | ₹94.6 Lakhs |
| Peak Penalty | ₹18.3 Lakhs |
| Off-peak Penalty | ₹76.3 Lakhs |

**Recalibration approach:**  
Applied **peak-aware bias recalibration** — no model retraining. Shifted peak-hour
forecasts toward the **75th percentile** (optimal quantile = 6/(6+2) = 75%) using a
structured grid search evaluated against the financial penalty function.

**After recalibration:**
| Metric | Before | After | Reduction |
|--------|--------|-------|-----------|
| Total Penalty | ₹94.6L | ₹35.7L | ~60% |
| Peak Penalty | ₹18.3L | ₹4.4L | ~75% |
| Off-peak Penalty | ₹76.3L | ₹31.3L | ~59% |

---

### Stage 3 — Constrained Board Optimization

At 1:00 AM, a confidential Board Directive imposed four binding constraints:

| Constraint        | Requirement                       | Status |
| ----------------- | --------------------------------- | ------ |
| Total Penalty Cap | Below Board-defined threshold     | ✅ Met |
| Forecast Bias     | −2% to +3%                        | ✅ Met |
| Peak Reliability  | Max 3 intervals >5% underestimate | ✅ Met |
| Average Uplift    | ≤ 3% across test window           | ✅ Met |

**Final strategy:** Moderate peak-aware bias centred near +2% — balancing
financial prudence with regulatory compliance. Sensitivity analysis performed
across ±10% peak demand amplification scenarios.

---

## Model Specifications

Model: XGBoost Regressor
Objective: Minimise asymmetric financial penalty (not RMSE)
Forecast horizon: 2-day ahead
Resolution: 15-minute intervals
Training period: April 2013 – December 2019
Validation: January 2020 – December 2020
Test (OOT): January 2021 – April 2021

---

## Key Results

- **86%** penalty reduction vs naive seasonal baseline (Stage 1)
- **~60%** total penalty reduction post regulatory shock (Stage 2)
- **~75%** peak-hour penalty reduction after recalibration (Stage 2)
- All **4 Board constraints** satisfied simultaneously (Stage 3)
- Bias maintained within **[-2%, +3%]** regulatory bound
- Peak violations limited to **≤ 3 intervals**

---

## Repository Structure

DecodeX-2026-CodeXplorers-Hackathon/
├── README.md
├── presentation/
│ └── CodeXplorers_GRIDSHIELD_Final_Presentation.pdf
├── technical/
│ └── Technical_Appendix_GRIDSHIELD.pdf
└── guidelines/
├── Finale_Round_Guidelines.pdf
├── Case_02_Stage1_Guidelines.pdf
├── Case_02_Stage2_Guidelines.pdf
└── Case_02_Stage3_Guidelines.pdf

---

## Tools & Technologies

![Python](https://img.shields.io/badge/Python-3776AB?style=flat&logo=python&logoColor=white)
![XGBoost](https://img.shields.io/badge/XGBoost-FF6600?style=flat)
![Pandas](https://img.shields.io/badge/Pandas-150458?style=flat&logo=pandas&logoColor=white)
![NumPy](https://img.shields.io/badge/NumPy-013243?style=flat&logo=numpy&logoColor=white)

- **Model:** XGBoost, Scikit-learn
- **Data Processing:** Pandas, NumPy
- **Feature Engineering:** Custom lag/rolling pipeline
- **Optimisation:** Grid search over penalty-minimising bias values
- **Visualisation:** Matplotlib, Seaborn
- **Presentation:** PowerPoint → PDF

---

## Team

**CodeXplorers**  
Bhavesh Kerkar & Devraj Pujari — B.Sc. Information Technology, PTVA's Sathaye College, Mumbai

---

_DecodeX 2026 | NLD Synapse | N.L. Dalmia Institute of Management Studies & Research_
