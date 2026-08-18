# Brent Crude Oil: ARDL Modeling, Structural Breaks & Forecast Evaluation

*An independent research project on the short-run determinants of Brent crude prices, tested for long-run equilibrium, structural stability, and out-of-sample forecast performance against a random walk. January 2010 – July 2026.*

**[Live site](#)** · **[Notebook](#)** · Built manually in NumPy/SciPy — no `statsmodels`

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

<!-- TODO: paste the fitted equation / coefficient table -->

## Bounds test — is there a long-run relationship?

Pesaran, Shin & Smith (2001) bounds testing procedure, critical values verified directly from the published paper.

> **Result:** Neither the F-test nor the t-test supports a long-run cointegrating relationship. The t-test rejects cointegration at all conventional levels; the F-test is inconclusive at 10%/5% and tips toward no cointegration at 1%.

<!-- TODO: paste actual F-stat, t-stat, and PSS critical value bounds -->

This is treated as a finding, not a failure: a short-run-only relationship means Brent's drivers act as shocks and adjustments rather than a stable equilibrium anchor.

## Structural breaks

Chow tests flag two significant breaks — **February 2020** and **February 2022** — meaning a single pooled regression implicitly assumes a stability the data rejects. A rolling 30-month window regression traces how coefficients actually move:

- **USD_Index** — sign reversal across the sample window
- **GPR_Index** — step-change in sensitivity around the break dates
- **China_PMI** — demand signal effectively goes dark during 2020–22

## Forecast evaluation vs. random walk

Rolling-window walk-forward evaluation (not a single static split) across multiple horizons — 1-step, and multi-step h = 3, 6, 12 — benchmarked against a random walk.

**Diebold–Mariano test:** formal test of whether ARDL forecast errors are significantly lower than the random walk benchmark, not just numerically lower.
<!-- TODO: fill in DM test result once run -->

<!-- TODO: note whether test-period forecasts are truly out-of-sample for regressors, or conditional on realized future regressor values -->

## Extension — MYR/USD & fiscal exposure

A lightweight supplement, not a second empirical chapter: how much of MYR/USD variation traces back to the dollar versus Brent, framed as fiscal/policy exposure rather than political analysis.

- DXY explains roughly **50%** of monthly MYR variation and is highly significant
- Brent's coefficient is directionally consistent with terms-of-trade logic but **not statistically significant** in either the short-run or levels specification
- Engle–Granger cointegration test: inconclusive

## Limitations & next steps

- Endogeneity between Brent, DXY, and China PMI — VAR / Granger causality as a robustness check
- Possible removal of the insignificant crude-stocks variable for a leaner specification
- Residual diagnostics (Breusch–Pagan, Jarque–Bera) ahead of the forecast split

## Repo structure

```
.
├── index.html              # GitHub Pages site
├── assets/charts/           # exported chart images
├── notebook/                 # full Jupyter notebook
├── data/merged_monthly.csv   # dataset
└── README.md
```

## Tools

Python (NumPy, SciPy, pandas) · Jupyter Notebook · manual econometrics implementation, no `statsmodels`
