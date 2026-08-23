# Week 7 — Support

This file contains the performance record, audit checklist, troubleshooting, and readings for [Week 7 main](week7_main.md).

## Before class

Bring the Week 6 net-return ledger and cost convention. Confirm that the notebook can import `numpy` and `pandas`, and keep the 252-observation annualization and zero target assumptions visible beside the outputs.

## Performance and audit record

```text
Return series and whether costs are included:
Observation frequency and annualization factor:
Target or cash return:
Date range and number of observations:
Annualized return definition:
Volatility definition:
Sharpe definition:
Sortino target and downside definition:
Maximum drawdown definition:
Drawdown-duration unit:
Turnover convention:
Bootstrap type, block length, repetitions, and seed:
Number of model or strategy variants tried:
Selection period:
Frozen evaluation period:
Failed or unclear audit items:
Permitted conclusion:
```

## Audit checklist

- Information existed before the historical decision.
- Signal, position, return, and execution intervals align.
- Costs apply to every stated position change.
- Benchmarks use common dates and compatible assumptions.
- Missing observations and initial returns are handled explicitly.
- Metrics show units, frequency, target, and cost status.
- Window, threshold, and model choices were not tuned on the frozen period.
- Every tried alternative is counted or described.
- Uncertainty and market-period sensitivity are reported.
- Conclusions stay within the tested data and assumptions.

## Troubleshooting

| Symptom | First check | Action |
|---|---|---|
| Sharpe is infinite | Inspect return standard deviation | Report undefined when variability is zero |
| Sortino is unexpectedly large | Inspect target and downside denominator | Replace nonnegative deviations with zero and average squared deviations over all observations, matching the stated formula |
| Drawdown is positive or misses an initial loss | Inspect wealth divided by a running peak that includes initial wealth 1 | Drawdown must be zero or negative, and a first-period loss must appear |
| Duration is reported as percent | Inspect the unit | Report observations or convert with a stated calendar assumption |
| Bootstrap changes each run | Inspect the seed | Record the seed and repetitions |
| Selected model collapses later | Count alternatives and selection steps | Report selection risk rather than hiding later evidence |

## Optional extensions

- Report calendar-day and trading-observation drawdown durations separately.
- Add a nonzero cash or target return and recompute Sharpe and Sortino.
- Compare fixed-length and stationary block bootstraps.
- Divide the artificial path into two periods and compare all metrics without retuning.

## Supporting resource and reading

- The [older risk-control notebook](../resources/notebooks/04_TypeB_風險控管_績效診斷與策略提案.ipynb) is optional background.
- Bailey, D. H., Borwein, J. M., López de Prado, M., & Zhu, Q. J. (2016). [The Probability of Backtest Overfitting](https://www.davidhbailey.com/dhbpapers/backtest-prob.pdf). *The Journal of Computational Finance, 20*(4), 39–69. DOI: `10.21314/JCF.2016.322`.
- Lo, A. W. (2002). [The Statistics of Sharpe Ratios](https://doi.org/10.2469/faj.v58.n4.2453). *Financial Analysts Journal, 58*(4), 36–52.
- Politis, D. N., & Romano, J. P. (1992). [A Circular Block-Resampling Procedure for Stationary Data](https://statistics.stanford.edu/technical-reports/circular-block-resampling-procedure-stationary-data). In R. LePage & L. Billard (Eds.), *Exploring the Limits of Bootstrap* (pp. 263–270). Wiley.
- Sortino, F. A., & Price, L. N. (1994). [Performance Measurement in a Downside Risk Framework](https://doi.org/10.3905/joi.3.3.59). *The Journal of Investing, 3*(3), 59–64.
- White, H. (2000). [A Reality Check for Data Snooping](https://onlinelibrary.wiley.com/doi/abs/10.1111/1468-0262.00152). *Econometrica, 68*(5), 1097–1126.

These readings support analysis of Sharpe-ratio uncertainty, downside-risk measurement, block resampling, and selection risk. Week 7 states its exact Sortino denominator convention; its percentile circular block-bootstrap and small repeated-selection demonstration do not reproduce the sources' full procedures or inference.
