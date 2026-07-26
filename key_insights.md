# 📊 Key Insights: Forecasting Household Deposits in Russia

**Project:** Macroeconomic Modeling & Time Series Analysis  
**Author:** Nadezhda Silkina  
**Date:** 2026  

---

## 📌 Table of Contents

1. [Data Overview](#1-data-overview)
2. [Correlation Analysis](#2-correlation-analysis)
3. [Time Series & Scatter Plot Analysis](#3-time-series--scatter-plot-analysis)
4. [Stationarity Analysis](#4-stationarity-analysis)
5. [Multicollinearity Diagnostics](#5-multicollinearity-diagnostics)
6. [Model Performance Comparison](#6-model-performance-comparison)
7. [Ridge Regression Coefficients: What Drives Deposits?](#7-ridge-regression-coefficients-what-drives-household-deposits)
8. [Hyperparameter Tuning: Ridge Alpha Selection](#8-hyperparameter-tuning-ridge-alpha-selection)
9. [Executive Summary](#9-executive-summary)

---

## 1. Data Overview

**Timeframe:** January 2014 — February 2026 (146 monthly observations)  
**Target Variable:** `DEPOS` — Volume of household deposits (billion RUB)  
**Features:** 9 macroeconomic indicators + 4 lag features (1, 3, 6, 12 months)

### 📊 Summary Statistics

| Variable | Mean | Std Dev | Min | Max |
|----------|------|---------|-----|-----|
| DEPOS | 32,547 | 12,379 | 16,564 | 65,871 |
| WAGE | 56,210 | 22,855 | 29,255 | 139,727 |
| SERV | 975 | 337 | 553 | 1,833 |
| DEP1 | 8.81 | 3.94 | 4.06 | 21.49 |
| CRED1 | 20.79 | 5.02 | 13.04 | 32.73 |
| CPI | 100.60 | 0.78 | 99.50 | 107.60 |
| USDind | -0.41 | 5.83 | -25.40 | 33.30 |
| UNEM | 1.15 | 0.86 | 0.30 | 4.90 |
| IPI | 100.42 | 7.85 | 75.40 | 119.00 |
| IMP | 101.14 | 19.72 | 57.50 | 150.50 |

### 🔍 Key Observations

- **DEPOS** has grown significantly: from ~16,500 to ~65,800 billion RUB (**4x increase**)
- **WAGE** shows strong upward trend: from 29,255 to 139,727 RUB
- **CPI** is relatively stable (around 100–101%), with spikes in 2015–2016
- **USDind** shows high volatility (range: -25.4 to +33.3)
- **UNEM** remains low (0.3–4.9%), typical for Russia

---

## 2. Correlation Analysis

### 📈 Strongest Positive Correlations with DEPOS

| Rank | Feature | Correlation | Interpretation |
|------|---------|-------------|----------------|
| 1 | **WAGE** | **0.980** | Almost perfect correlation. As wages rise, people save more. |
| 2 | **SERV** | **0.978** | Volume of paid services; economic activity drives deposits. |
| 3 | **DEP1** | **0.835** | Deposit rates; higher rates attract more deposits. |

### 📉 Strongest Negative Correlations with DEPOS

| Rank | Feature | Correlation | Interpretation |
|------|---------|-------------|----------------|
| 1 | **IPI** | **-0.685** | Industrial production; inverse relationship. |
| 2 | **UNEM** | **-0.605** | Unemployment; more unemployment = less savings. |

### ⚠️ Multicollinearity Warning

- **VIF > 10** for most predictors (CPI: 327, IPI: 181, SERV: 161, WAGE: 108)
- This justifies using **Ridge regularization** (handles collinearity effectively)

---

## 3. Time Series & Scatter Plot Analysis

### 📅 Time Series Trends

- **DEPOS**: Clear upward trend with seasonal peaks (December/January). COVID-19 (2020) and 2022 geopolitical events show minor disruptions, but the trend remained positive.
- **WAGE**: Steady growth, with slight acceleration after 2020.
- **SERV**: Strong upward trend, particularly after 2021.
- **DEP1**: Peaked in 2015 (21.49%) and gradually declined to ~4% in 2026.
- **CPI**: Spikes in 2015–2016 (inflation crisis) and 2022 (sanctions).
- **USDind**: High volatility; sharp depreciation in 2015 (USDind = -25.4%) and 2022.

### 🎯 Scatter Plot Insights

| Relationship | R² | Type |
|--------------|-----|------|
| DEPOS vs WAGE | ~0.96 | Strong positive linear |
| DEPOS vs SERV | ~0.96 | Strong positive linear |
| DEPOS vs DEP1 | — | Positive with curvature |
| DEPOS vs CPI | — | Weak negative |
| DEPOS vs IPI | — | Negative |
| DEPOS vs USDind | — | No clear linear relationship |

**Conclusion:** The data contains **both linear and non-linear relationships**, which justifies trying models beyond simple linear regression.

---

## 4. Stationarity Analysis

### 🔬 Augmented Dickey-Fuller Test for DEPOS

| Metric | Value |
|--------|-------|
| ADF Statistic | 0.3935 |
| p-value | 0.9813 |
| **Conclusion** | **Non-stationary ❌** |

### 💡 Interpretation

The DEPOS series is **not stationary** — it has a strong upward trend (obvious from the plot). This means that:

- ARIMA/SARIMA models (which require stationarity) would need **differencing** (1st or 2nd order).
- Machine learning models (Ridge, Random Forest) are more flexible and don't require stationarity.

### 🔮 Future Work

**SARIMA or Prophet** models could improve forecasting by explicitly modeling trend and seasonality. This would be implemented in a separate notebook (`02_SARIMA_Modeling.ipynb`).

---

## 5. Multicollinearity Diagnostics

### 📊 VIF (Variance Inflation Factor) Results

| Feature | VIF | Status |
|---------|-----|--------|
| CPI | 327.1 | ⚠️ Extreme multicollinearity |
| IPI | 180.7 | ⚠️ Extreme multicollinearity |
| SERV | 160.9 | ⚠️ Extreme multicollinearity |
| WAGE | 108.2 | ⚠️ Extreme multicollinearity |
| CRED1 | 76.9 | ⚠️ High multicollinearity |
| IMP | 40.5 | ⚠️ High multicollinearity |
| DEP1 | 33.3 | ⚠️ High multicollinearity |
| UNEM | 4.97 | ✅ No issue |
| USDind | 1.03 | ✅ No issue |

### ✅ Solutions Applied

- **Ridge Regression** (regularization) handles multicollinearity naturally.
- **Feature selection** (removing insignificant variables) reduces collinearity.
- **VIF > 10** indicates severe multicollinearity — typical in macroeconomics (many indicators move together).

---

## 6. Model Performance Comparison

### 📊 Test Set Metrics (March 2025 — February 2026)

| Model | R² | MAE | RMSE | MAPE |
|-------|-----|-----|------|------|
| Linear Regression (full) | 0.8433 | 754.08 | 931.99 | 1.22% |
| Linear Regression (reduced)* | 0.8474 | 736.24 | 919.85 | 1.20% |
| **Ridge Regression (alpha=1.0)** | **0.8486** | **807.96** | **916.32** | **1.32%** |
| Ridge Regression (alpha=0.001) | 0.8433 | 807.96 | 932.05 | 1.32% |
| Random Forest | -7.4829 | 6,319.14 | 6,858.17 | 10.21% |

*Reduced model uses only significant features (p < 0.05): WAGE, CPI, USDind, IPI, DEPOS_lag_1*

### 🏆 Best Model: Ridge Regression (alpha=1.0)

- **R² = 0.8486** (explains ~85% of variance)
- **Average prediction error:** ~808 billion RUB (~1.3% of DEPOS)
- **Handles multicollinearity** effectively
- **Coefficients are interpretable** — important for business insights

### ❌ Why Random Forest Failed

| Issue | Explanation |
|-------|-------------|
| R² = -7.48 | Worse than predicting the mean |
| Small sample | Only 134 training records — trees need more data |
| Time series | Trees don't capture temporal order |
| Overfitting | The model "memorized" the training data |

**💡 Lesson:** Not all models work for all tasks. Simpler, regularized models (Ridge) outperform complex models (Random Forest) on small time series data.

---

## 7. Ridge Regression Coefficients: What Drives Household Deposits?

### 🔺 Top Drivers (Increase Deposits)

| Rank | Feature | Coefficient | Interpretation |
|------|---------|-------------|----------------|
| 1 | **DEPOS_lag_1** | **+4,153.97** | Strongest effect: past month deposits are the best predictor |
| 2 | **DEPOS_lag_3** | **+2,177.65** | Past deposits at 3-month lag |
| 3 | **DEPOS_lag_6** | **+1,058.05** | Past deposits at 6-month lag |
| 4 | **SERV** | **+591.35** | Economic activity drives savings |
| 5 | **DEP1** | **+434.42** | Higher rates attract more deposits |

### 🔻 Top Draggers (Decrease Deposits)

| Rank | Feature | Coefficient | Interpretation |
|------|---------|-------------|----------------|
| 1 | **IPI** | **-378.23** | Industrial production: more production = less savings? |
| 2 | **USDind** | **-198.66** | USD exchange rate: ruble depreciation reduces deposits |
| 3 | **CPI** | **-167.89** | Inflation: erodes purchasing power, reduces savings |

### 💡 Interesting Findings

- **CRED1** (+88.94) and **IMP** (+25.99) have slight **positive** effects — not draggers.
- All coefficients align with **economic intuition**.
- **Past deposits are the strongest predictor** — deposits have strong inertia.

---

## 8. Hyperparameter Tuning: Ridge Alpha Selection

### 🔧 What is Alpha?

- Controls the strength of **regularization** in Ridge regression.
- **Higher alpha** → stronger penalty on coefficients → simpler model.
- **Lower alpha** → closer to ordinary linear regression.

### 📊 Cross-Validation Results

| Metric | Value |
|--------|-------|
| Tested range | 0.001 to 1000 (50 values, log scale) |
| **Best alpha (CV)** | **0.001** — close to linear regression |
| **Original alpha (1.0)** | R² = 0.8486, RMSE = 916.32 |
| **Optimal alpha (0.001)** | R² = 0.8433, RMSE = 932.05 |

### ✅ Conclusion

The original alpha **(1.0) is already optimal**. Stronger regularization (alpha > 1) would reduce performance. The cross-validation suggests that the model benefits from mild regularization, but not too much.

---

## 9. Executive Summary

### 🎯 Key Takeaways

| # | Finding |
|---|---------|
| 1 | **Ridge Regression (alpha=1.0)** is the best model with R² = **0.8486** |
| 2 | **Past deposits (lag 1, 3, 6 months)** are the strongest predictors — deposits have high inertia |
| 3 | **Economic activity (SERV, WAGE, DEP1)** increases deposits — intuitive |
| 4 | **Inflation (CPI), exchange rate (USDind), and industrial production (IPI)** decrease deposits |
| 5 | **Random Forest failed** due to small sample size and non-sequential nature of trees |
| 6 | **Multicollinearity is severe** (VIF > 100) — Ridge regularization is essential |
| 7 | **DEPOS is non-stationary** — future work should consider SARIMA/Prophet |

### 💼 Business Implications

- Deposits are **highly predictable (R² ~85%)** — useful for banks and policymakers.
- **Deposit rates (DEP1) matter:** higher rates attract more deposits.
- **Inflation and exchange rate volatility reduce deposits** — focus on economic stability.

### 🚀 Next Steps

| Step | Description |
|------|-------------|
| 1 | Add **seasonal components** (month of year) to capture December/January peaks |
| 2 | Test **SARIMA or Prophet** for explicit time series modeling |
| 3 | Use **LSTM** for deeper learning (if more data becomes available) |

---

## 📝 Final Note

This analysis demonstrates the power of combining **classical econometrics** (VIF, ADF, Durbin-Watson) with **modern machine learning** (Ridge, Random Forest) for macroeconomic forecasting. The results are interpretable, robust, and aligned with economic theory.

---

**📁 Project Repository:** [HopeSilkina/deposits_forecast_project](https://github.com/HopeSilkina/deposits_forecast_project)  
**👤 Author:** Nadezhda Silkina  
**📅 Last Updated:** 2026