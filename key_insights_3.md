# 📊 Model Interpretation: SHAP Analysis and Nonlinearities

**Project:** Part 3 — model interpretation  
**Author:** Nadezhda Silkina  
**Date:** 22.08.2026  

---

## 📌 Table of Contents

1. [SHAP Analysis](#1-shap-analysis-ridge-regression)
2. [Nonlinearity Analysis](#2-nonlinearity-analysis)
3. [Final Conclusions](#3-final-conclusions)
4. [Next Steps](#4-next-steps)

---

## 📌 Introduction

This document presents the results of model interpretation from the first part of the project. Main objectives:
1. **SHAP analysis** — identify which factors actually influence deposits.
2. **Nonlinearity analysis** — check for nonlinear relationships.
3. **Model comparison** — evaluate whether model complexity is justified.

---

## 1. SHAP Analysis (Ridge Regression)

**Model analyzed:** Ridge (alpha=1.0) — the best model selected in Block 1.

### 📊 Top-5 Features by Importance

| Feature | SHAP Importance | Interpretation |
|---------|-----------------|----------------|
| **DEPOS_lag_1** | 14,531 | Strongest effect — deposit inertia |
| **DEPOS_lag_3** | 7,847 | Effect persists for 3 months |
| **DEPOS_lag_6** | 3,865 | Effect persists for 6 months |
| **SERV** | 1,725 | Economic activity → deposit growth |
| **DEPOS_lag_12** | 1,072 | Annual inertia |

**Conclusion:** Lag features dominate. This confirms that deposits have strong inertia — a key factor for forecasting.

---

### 📈 Direction of Influence

![SHAP Summary Plot](outputs/v2/03_shap_summary.png)

- **Positive influence:** high feature values → deposit growth (`DEPOS_lag_1`, `SERV`, `DEP1`).
- **Negative influence:** high feature values → deposit decline (`CPI`, `USDind`, `IPI`).

**Conclusion:** The directions of influence align with economic logic (lag features have positive influence, while inflation and exchange rates have negative influence).

---

### 📉 SHAP Dependence on Feature Value

![SHAP Dependence Plot](outputs/v2/03_shap_dependence.png)

Using `DEPOS_lag_1` as an example:
- Increasing the lag value increases its influence on the forecast — past month deposits linearly increase the forecast.
- There are outliers that require verification.

**Conclusion:** The influence of lags is linear, but there are points worth checking for anomalies.

---

## 2. Nonlinearity Analysis

### 🔍 Ramsey Test (Checking for Omitted Nonlinearities)

**What it is:** The Ramsey RESET test checks whether there are omitted nonlinear relationships in the model. It adds powers of predicted values (y_pred², y_pred³) and tests whether the model improves significantly.

**Results:**

| Metric | F-statistic | p-value | Conclusion |
|--------|-------------|---------|------------|
| **y_pred²** | 4537.57 | 0.0000 | ✅ Omitted nonlinearities |
| **y_pred³** | 1309.32 | 0.0000 | ✅ Omitted nonlinearities |

**Interpretation:** The test detected statistically significant nonlinearities. However, as shown below, directly adding them to the model leads to overfitting due to the small sample size (n=122).

**Important:** The test was performed on the **linear model** (not Ridge). Ridge already compensates for nonlinearities through regularization.

---

### 🔍 Models for Testing Nonlinearities

| Model | Description | Features |
|-------|-------------|----------|
| **Baseline (Linear)** | Linear regression without regularization | 13 |
| **All polynomials** | All quadratic features and interactions (degree=2) | 104 |
| **Lasso (selection)** | L1-regularization for automatic selection from 104 polynomials | 98 |
| **EDA hypotheses** | DEP1², UNEM², UNEM×DEP1, SERV² (from EDA hypotheses) | 17 |
| **Stepwise (selection)** | Stepwise selection of 10 best features out of 17 | 10 |

### 📊 Model Comparison Table

**Metrics on training sample (n=122):**

| Model | Features | R²_train | R²_adj_train | AIC | BIC |
|-------|----------|----------|--------------|-----|-----|
| Baseline (Linear) | 13 | 0.9972 | 0.9968 | 1889.06 | 1928.32 |
| All polynomials | 104 | 0.9998 | 0.9988 | 1948.19 | 2242.61 |
| Lasso (selection) | 98 | 0.9993 | 0.9961 | 2077.59 | 2355.19 |
| EDA hypotheses | 17 | 0.9975 | 0.9970 | 1887.82 | 1938.29 |
| **Stepwise (selection)** | **10** | **0.9971** | **0.9968** | **1882.04** | **1912.89** |

**Metrics on test sample (n=12):**

| Model | R²_test | RMSE_test | R²_gap* |
|-------|---------|-----------|---------|
| **Baseline (Linear)** | **0.8433** | **932.00** | **0.1538** |
| All polynomials | -32.34 | 13,597 | 33.34 |
| Lasso (selection) | -6.07 | 6,261 | 7.07 |
| EDA hypotheses | -0.13 | 2,500 | 1.12 |
| Stepwise (selection) | 0.8287 | 974.44 | 0.1684 |

**\*R²_gap** = R²_train - R²_test — the difference between quality on training and test samples. A large R²_gap indicates overfitting.

### 🏆 Best Models by Different Criteria

| Criterion | Winner | Value |
|-----------|--------|-------|
| **R²_train** | All polynomials | 0.9998 |
| **R²_test** | Baseline (Linear) | 0.8433 |
| **AIC** | Stepwise (selection) | 1882.04 |
| **BIC** | Stepwise (selection) | 1912.89 |

**Conclusion:** Criteria diverge:
- R²_test selects the baseline model (best predictive ability)
- AIC/BIC select Stepwise (best quality/complexity balance)
- All polynomials win on R²_train but fail completely on the test sample

### 📊 Overfitting Analysis

| Model | R²_gap | Status |
|-------|--------|--------|
| Baseline (Linear) | 0.1538 | ⚠️ Moderate |
| All polynomials | 33.34 | ❌ CRITICAL |
| Lasso (selection) | 7.07 | ❌ CRITICAL |
| EDA hypotheses | 1.12 | ❌ STRONG |
| Stepwise (selection) | 0.1684 | ⚠️ Moderate |

---

### 🔍 Detailed Testing of EDA Hypotheses

**Method:** Each hypothesis was tested separately — one feature was added to the baseline model, and changes in metrics were evaluated on training and test samples.

| Hypothesis | Feature | ΔR²_train | ΔR²_test | ΔAIC | ΔR²_gap |
|------------|---------|-----------|----------|------|---------|
| DEP1² | DEP1_sq | +0.0000 | -0.0003 | +3.13 | +0.0003 |
| UNEM² | UNEM_sq | +0.0000 | +0.0030 | +2.45 | -0.0030 |
| UNEM × DEP1 | UNEM_DEP1 | +0.0001 | +0.0212 | +0.69 | -0.0211 |
| SERV² | SERV_sq | +0.0002 | -0.9897 | -4.39 | +0.9899 |

**Interpretation of metrics:**
- **ΔR²_train > 0** — model better explains training data
- **ΔR²_test > 0** — model better predicts new data
- **ΔAIC < 0** — model is better with complexity penalty
- **ΔR²_gap > 0** — overfitting increased

**Conclusions for each hypothesis:**

1. **DEP1²** — ❌ NOT CONFIRMED: no improvement on either sample

2. **UNEM²** — ⚠️ PARTIALLY: improves prediction (ΔR²_test = +0.0030), but AIC worsened (ΔAIC = +2.45). Quality improvement does not compensate for model complexity.

3. **UNEM × DEP1** — ⚠️ PARTIALLY: improves prediction (ΔR²_test = +0.0212), but AIC worsened (ΔAIC = +0.69). This is the best hypothesis, but the improvement is not enough to justify additional complexity.

4. **SERV²** — ❌ NOT CONFIRMED: significantly worsens the test sample (ΔR²_test = -0.9897) due to multicollinearity with SERV.

**Overall conclusion:** None of the EDA hypotheses provide significant improvement. Nonlinearities exist (Ramsey test), but directly adding them leads to overfitting.

---

## 3. Final Conclusions

1. **The baseline linear model remains the best** in terms of predictive ability (R²_test = 0.8433)
2. **Lag features** are the main drivers of deposits (based on SHAP analysis)
3. **The Ramsey test detected nonlinearities**, but adding them leads to overfitting
4. **UNEM × DEP1** is the only hypothesis with notable R²_test improvement (+0.0212), but AIC worsened
5. **All polynomial models are overfitted** (R²_gap > 1)
6. **Stepwise model** has the best AIC/BIC, but slightly worse R²_test (0.8287 vs 0.8433)
   → In **Block 4**, Ridge with 38 features achieves R²_test = 0.9422.

**Recommendation:** Keep the linear model. In subsequent blocks, add:
- Dummy variables for seasonality and structural breaks (Block 4)
- SARIMA / Prophet for comparison with classical time series models

---

## 4. Next Steps

- [x] SHAP analysis
- [x] Nonlinearity analysis with full metrics
- [x] Ramsey test
- [x] EDA hypothesis testing with full metrics
- [ ] Dummy variables (seasonality, structural breaks) → [Block 4](key_insights_4.md)
- [ ] SARIMA / Prophet → [Block 5](key_insights_5.md)
- [ ] Final forecast for 2026-2027 → [Block 6](key_insights_6_itog.md)

---

*Document updated: 22.08.2026*
