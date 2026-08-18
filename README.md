# Brent Crude Oil: ARDL Modeling, Structural Breaks & Forecast Evaluation

*An independent research project on the short-run determinants of Brent crude prices, tested for long-run equilibrium, structural stability, and out-of-sample forecast performance against a random walk. January 2010 – July 2026.*

---

## The question

Brent crude is driven by a mix of inventories, the dollar, global demand, and geopolitical risk — but which of these actually hold up as short-run predictors once tested properly, and does the relationship stay stable across a decade that includes a pandemic crash and a land war in Europe?

## Data

| Variable | Source | Role |
|---|---|---|
| Brent spot price | EIA | Dependent variable |
| `US_Crude_Stocks_Total_kbbl` | EIA | Inventory / supply signal |
| `USD_Index` | FRED / ALFRED | Currency channel |
| `China_PMI_Manufacturing` | NBS | Global demand proxy |
| `GPR_Index` | Caldara & Iacoviello | Geopolitical risk |

199 monthly observations, no missing values.

## Model

Lag structure selected via grid search over AIC/BIC rather than assumed. The winning specification is **ARDL(3,1)**: three lags of Brent, one lag of each regressor. In-sample fit: **R² ≈ 0.946**.

p=3, q=1
General ARDL(p, q1, q2, q3, q4):
Brent_t = α + Σ(i=1..p) βᵢ·Brent_(t−i)
            + Σ(j=0..q1) γⱼ·Stocks_(t−j)
            + Σ(j=0..q2) δⱼ·USD_Index_(t−j)
            + Σ(j=0..q3) θⱼ·China_PMI_(t−j)
            + Σ(j=0..q4) φⱼ·GPR_Index_(t−j)
            + εₜ

## Coefficient estimates

Significant drivers: own lags (persistence, sum of AR coefficients ≈ 0.90 — stable, non-explosive), USD_Index (contemporaneous and lag 1), GPR_Index (contemporaneous and lag 1).

## Bounds test — is there a long-run relationship?

Pesaran, Shin & Smith (2001) bounds testing procedure, critical values verified directly from the published paper.

> **Result:** Neither the F-test nor the t-test supports a long-run cointegrating relationship. The t-test rejects cointegration at all conventional levels; the F-test is inconclusive at 10%/5% and tips toward no cointegration at 1%.

>Bounds F-statistic: 3.099 
>Critical values - Case III (unrestricted intercept, no trend), k=4:
| Sig. Lvl | I(0) | I(1) |
|---|---|---|
| 10% | 2.45 | 3.52 |
| 5% | 2.86 | 4.01 |
| 1% | 3.74 | 5.06 |
   
>Error-correction term t-stat (Brent_L1): -2.556
>Critical values - Case III (unrestricted intercept, no trend), k=4:
| Sig. Lvl | I(0) | I(1) |
|---|---|---|
| 10% | -2.57 | -3.66 |
| 5% | -2.86 | -3.99 |
| 1% | -3.43 | -4.60 |

Finding: a short-run-only relationship means Brent's drivers act as shocks and adjustments rather than a stable equilibrium anchor.

## Structural breaks

Chow tests flag two significant breaks — **February 2020** and **February 2022** — meaning a single pooled regression implicitly assumes a stability the data rejects. A rolling 30-month window regression traces how coefficients actually move:

- **USD_Index** — sign reversal across the sample window
- **GPR_Index** — step-change in sensitivity around the break dates
- **China_PMI** — demand signal effectively goes dark during 2020–22
See visualizations in figures.

## Forecast evaluation vs. random walk

Rolling-window walk-forward evaluation (not a single static split) across multiple horizons — 1-step, benchmarked against a random walk.

> **Result:**
| Metric | ARDL(3,1) | Random Walk |
|---|---|---|
| RMSE |7.534 | 8.419 |
| MAE | 5.329 | 5.260 |
| Error std | 7.519 |8.417 |

> **Diebold–Mariano test:**  stat = -1.068, p = 0.285 (not significant at 5%)

Finding:  The model's primary value lies in explaining historical price determinants, not in exploitable short-term forecasting.
Caveat: Exogenous regressors (Stocks, DXY, PMI, GPR) use realized future values in the forecast — this is a conditional/ex-post forecast, not a true unconditional forecast.

## Limitations & next steps

- Endogeneity between Brent, DXY, and China PMI — VAR / Granger causality as a robustness check
- Residual diagnostics (Breusch–Pagan, Jarque–Bera) ahead of the forecast split

## Repo structure

```
.
├── code/                 # full Jupyter notebook
├── data/   # dataset
├── figures/           # exported chart images
└── README.md
```

## Tools

Python (NumPy, SciPy, pandas) · Jupyter Notebook 
