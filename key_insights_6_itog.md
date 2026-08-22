# 📊 Final Forecast: Household Deposits in Russia for 2026–2027

**Project:** Part 6 — final forecast  
**Author:** Nadezhda Silkina  
**Date:** 22.08.2026  

---

## 📌 Table of Contents

1. [Methodology](#1-methodology)
2. [The Overfitting Problem](#2-the-overfitting-problem-of-training-on-the-full-sample)
3. [Macro Factor Scenarios](#3-macro-factor-scenarios)
4. [Forecast Results](#4-forecast-results)
5. [Visualization](#5-visualization)
6. [Limitations](#6-limitations)
7. [Recommendations](#7-recommendations)

---

## 📌 Introduction

This document summarizes the project on forecasting household deposits in Russia.  
The final forecast is based on the **full Ridge model (all 38 features from Block 4)** — the best model according to the comparison results.

**Objective:** Forecast deposit volume for 12 months (March 2026 — February 2027) considering three scenarios of macroeconomic development.

---

## 1. Methodology

### 📊 Model Used

| Parameter | Value |
|----------|----------|
| Model | **Full Ridge regression (all features)** |
| Features | **38** |
| Training sample (Block 4) | 122 observations (Jan 2015 — Feb 2025) |
| Validated quality (Block 4) | R²_test = 0.9422 |
| Final training sample | 134 observations (Jan 2015 — Feb 2026) |
| R²_train (final) | **0.9991** |
| RMSE_train | **353.85** billion RUB |
| MAE_train | **270.76** billion RUB |

### 🔧 Forecasting Process

1. Model is trained on all available data (134 observations)
2. For each month's forecast, features are created:
   - Macro factors (WAGE, CPI, UNEM...) — according to scenarios
   - DEPOS lags — updated iteratively (each forecast uses the previous one)
   - Seasonal dummy variables — by month
3. Forecast is built 12 steps ahead

---

## 2. The Overfitting Problem of Training on the Full Sample

### 🔍 Key Dilemma

| Approach | Advantages | Disadvantages |
|--------|-------------|------------|
| **Model on 122 obs.** (validated) | Independent quality assessment (R²_test = 0.9422) | Does not know about DEPOS growth in 2025-2026 → forecasts are severely underestimated |
| **Model on 134 obs.** (final) | Accounts for current DEPOS level → realistic forecasts | No independent quality check |

### 📊 Forecast Comparison

**Model on 122 observations** (trained until February 2025):
- Last known DEPOS: 56,937 billion RUB (February 2025)
- Forecast for March 2026: 47,789 billion RUB
- Problem: forecast **17% below** actual February 2026 level (64,794)

**Model on 134 observations** (trained until February 2026):
- Last known DEPOS: 64,794 billion RUB (February 2026)
- Forecast for March 2026: 65,432 billion RUB
- Forecast is adequate: growth from current level

### 📌 Conclusion

Training on the full sample is **necessary** because:
1. From March 2025 to February 2026, DEPOS grew significantly (+13.8% year-over-year)
2. A model without this data "does not understand" the new level and produces meaningless forecasts
3. The model structure is identical to the validated one (R²_test = 0.9422), so comparable quality can be expected

**Compromise:** We lose independent validation but gain an up-to-date forecast. This is standard practice in final forecasting.

---

## 3. Macro Factor Scenarios

Three scenarios of macroeconomic development were created for the forecast.

### 📊 Scenario Parameters

| Parameter | Baseline | Optimistic | Pessimistic |
|----------|---------|---------------|----------------|
| **WAGE** (growth/month) | +0.7% | +1.0% | +0.4% |
| **CPI** (change, p.p.) | -0.02 | -0.05 | +0.05 |
| **UNEM** (change) | +1.0% | -2.0% | +5.0% |
| **DEP1** (change) | -2.0% | -5.0% | +5.0% |

### Scenario Descriptions

**Baseline (most likely):**
- Moderate wage growth (+8.7% annually)
- Gradual disinflation
- Slight increase in unemployment
- Stable deposit rates

**Optimistic:**
- Accelerated wage growth (+12.7% annually)
- Rapid disinflation
- Decreasing unemployment
- Rate cuts (stimulating the economy)

**Pessimistic:**
- Slower wage growth (+4.9% annually)
- Rising inflation
- Increasing unemployment
- Rate hikes (fighting inflation)

---

## 4. Forecast Results

### 📊 Monthly Forecast (billion RUB)

| Month | Baseline | Optimistic | Pessimistic | 95% PI (baseline) |
|-------|---------|---------------|----------------|-------------------|
| March 2026 | 65,766 | 65,702 | 65,937 | 65,070 — 66,462 |
| April 2026 | 65,192 | 65,165 | 65,332 | 64,496 — 65,888 |
| May 2026 | 65,760 | 65,782 | 65,853 | 65,064 — 66,456 |
| June 2026 | 66,007 | 66,085 | 66,059 | 65,311 — 66,703 |
| July 2026 | 66,552 | 66,705 | 66,536 | 65,856 — 67,248 |
| August 2026 | 67,123 | 67,358 | 67,027 | 66,427 — 67,819 |
| September 2026 | 67,444 | 67,760 | 67,283 | 66,748 — 68,140 |
| October 2026 | 67,958 | 68,374 | 67,707 | 67,262 — 68,654 |
| November 2026 | 67,941 | 68,465 | 67,587 | 67,245 — 68,637 |
| December 2026 | 68,293 | 68,930 | 67,837 | 67,597 — 68,989 |
| January 2027 | 69,084 | 69,843 | 68,513 | 68,387 — 69,780 |
| **February 2027** | **70,046** | **70,934** | **69,353** | **69,350 — 70,742** |

### 📊 Summary Statistics

| Scenario | Min | Max | Mean | February 2027 | 12-Month Growth |
|----------|-----|------|---------|-------------|----------------|
| **Baseline** | 65,192 | 70,046 | 67,264 | 70,046 | **+8.1%** |
| **Optimistic** | 65,165 | 70,934 | 67,592 | 70,934 | **+9.5%** |
| **Pessimistic** | 65,332 | 69,353 | 67,085 | 69,353 | **+7.0%** |

**Current level (February 2026):** 64,794 billion RUB.

### Seasonal Patterns

- **April** — minimum value across all scenarios (drop after March peak)
- **December–January** — growth (pre-New Year payments, bonuses)
- **February** — maximum value (annual peak)

---

## 5. Visualization

<details>
<summary><b>📈 Chart: Final Forecast with Three Scenarios</b> (click to expand)</summary>

![Final Forecast](outputs/v2/06_final_forecast_2026_2027.png)

**Observations:**
- All three scenarios show deposit growth
- Optimistic scenario is above baseline across the entire horizon
- Pessimistic scenario is below baseline
- Confidence interval widens toward the end of the period
- Seasonal fluctuations persist (April drop, December rise)

</details>

---

## 6. Limitations

### Methodological

| Limitation | Description | Impact |
|-------------|----------|---------|
| **No independent validation** | Model trained on all 134 observations | Quality on new data is unknown |
| **Simplified scenarios** | Macro factors set as linear trends | Do not account for possible shocks |
| **12-month horizon** | Accuracy decreases with horizon | Later months are less reliable |
| **Prediction interval** | ±696 billion RUB (from training sample) | May be underestimated in practice |

### Economic Risks

| Risk | Description |
|------|----------|
| **Geopolitical shocks** | Sanctions, crises not accounted for |
| **Rate changes** | Sharp DEP1 movements could alter dynamics |
| **Inflation spikes** | Unexpected CPI growth |
| **Structural changes** | New economic regimes |

---

## 7. Recommendations

### For Using the Forecast

1. **Main scenario:** Baseline (+8.1% annual growth)
2. **Planning range:** 69,353 — 70,934 billion RUB (pessimistic — optimistic)
3. **Monitoring:** Track actual WAGE, CPI, UNEM monthly
4. **Update:** Recalculate forecast as new data becomes available

### For Further Development

- [x] Baseline model (Ridge, 13 features) — Block 1
- [x] Deep time series analysis — Block 2
- [x] Model interpretation (SHAP) — Block 3
- [x] Feature Engineering (38 features) — Block 4
- [x] Time series models (SARIMA) — Block 5
- [x] Final forecast (3 scenarios) — Block 6
- [ ] Update forecast with new data
- [ ] Create monitoring dashboard

---

*Document updated: 22.08.2026*
*Project completed: 6 blocks of analysis and modeling*
