# Week 5 — Support

This file contains the evaluation-design record, checks, troubleshooting, and extensions for [Week 5 main](week5_main.md).

## Before class

Bring the Week 3 information-timing record and Week 4 position alignment. Confirm that a clean notebook can import `numpy` and `pandas`, and preserve the full error message if it cannot.

## Evaluation-design record

```text
Prediction target and horizon:
Feature availability time:
Prediction time and within-boundary calculation order:
Minimum training size:
Window type and length:
Model refit rule:
Signal update rule:
Portfolio rebalance rule:
Training period:
Validation period:
Frozen test period:
Preprocessing fit boundary:
Last permitted training outcome at time t:
Date when that outcome became observable:
Random seed, if applicable:
Choices frozen before final test:
```

## Required checks

- Training indices are strictly earlier than each prediction index.
- Preprocessing is fitted inside each permitted training window.
- The target for the prediction row is absent from training.
- Model versions change only on scheduled refit dates.
- Positions change only according to the stated rebalance rule.
- The final test period is not used to choose windows, thresholds, or frequencies.

## Troubleshooting

| Symptom | First check | Action |
|---|---|---|
| `lstsq` fails | Inspect missing and nonnumeric training values | Stop and repair the input boundary; do not drop arbitrary future rows |
| Predictions begin too early | Compare `minimum_train` with usable rows | Preserve the minimum history and initial missing predictions |
| Last training feature date equals prediction date | Inspect the history slice | End training before the current feature row; the last target may become known at the prediction boundary |
| Model version changes every row | Inspect `refit_every` condition | Separate parameter fitting from signal recalculation |
| Periodic position changes daily | Inspect `.where(rebalance_day).ffill()` | Hold the last permitted signal between rebalance dates |
| Final test improves after repeated edits | Review the experiment log | Treat it as validation and designate a new untouched test if available |

## Optional extensions

- Compare refitting every 1, 5, and 20 observations while holding the rebalance rule fixed.
- Add an intercept-only benchmark prediction.
- Store coefficients for every model version and plot their instability.
- Replace the rolling window length without viewing the frozen test result.

## Supporting resources

The older [prediction notebook](../resources/notebooks/02_TypeB_金融資料處理_特徵工程與機器學習預測.ipynb) and [backtesting notebook](../resources/notebooks/03_TypeB_AI交易訊號_策略設計與回測.ipynb) are optional examples, not the required Week 5 sequence.

## Official software references

- [`numpy.linalg.lstsq`](https://numpy.org/doc/stable/reference/generated/numpy.linalg.lstsq.html)
- [`pandas.DataFrame.expanding`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.expanding.html)
- [`pandas.DataFrame.rolling`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.rolling.html)

## Research reference

- White, H. (2000). [A Reality Check for Data Snooping](https://onlinelibrary.wiley.com/doi/abs/10.1111/1468-0262.00152). *Econometrica, 68*(5), 1097–1126.

The paper supports the warning about reusing data for model selection. Week 5 does not implement White's formal test.
