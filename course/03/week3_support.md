# Week 3 — Support

This file contains preparation, the information-timing record, detailed checks, troubleshooting, and extensions for [Week 3 main](week3_main.md).

## Before class

- Bring the Week 2 return definitions and a working notebook with `numpy` and `pandas`.
- Confirm that `shift(1)` moves existing values down one row and creates a missing value at the start.
- No additional notebook or online data is required. Older integrated prediction notebooks span topics assigned to several current weeks and are not part of the Week 3 sequence.

## Information-timing record

```text
Variable or feature:
Financial meaning:
Source observation:
Observation time:
Period represented, when relevant:
Publication time:
Transformation and lookback:
Last source value used:
Signal time:
Decision time:
Earliest execution time:
First eligible evaluation return:
Join key and direction, when separately published data are aligned:
Leakage checks performed:
What this variable cannot establish:
```

## Leakage checklist

Check each candidate for:

- a negative shift such as `shift(-1)`;
- a centered rolling window;
- a target column included among features;
- normalization, imputation, or selection estimated with future rows;
- revised data substituted for the version available at the historical decision time;
- a same-period return multiplied by a signal that was known only after that return; and
- a join that matches on a future rather than most recently available publication date.

## Terminology support

| Term | Week 3 meaning |
|---|---|
| observation time | When the underlying value occurs |
| publication time | When the analyst can obtain the value |
| signal time | When all required inputs allow signal calculation |
| decision time | When the portfolio rule accepts the signal |
| execution time | When the stated trading assumption permits a transaction |
| information leakage | Future or otherwise unavailable information entering the workflow |
| look-ahead bias | Evaluation that uses information unavailable at the historical decision time |

## Troubleshooting

| Symptom | First check | Action |
|---|---|---|
| Feature starts later than expected | Count required prices and returns | State the minimum history before changing `min_periods` |
| Feature has values at the beginning | Inspect fills applied after rolling | Remove fills that invent unavailable history |
| Signal and target look identical | Search for `shift(-1)` in feature columns | Remove the future outcome from the feature set |
| Centered mean appears smoother | Inspect `center=True` | Use a trailing window for a decision at time \(t\) |
| Full-sample standardization gives strong results | Locate where mean and standard deviation were fitted | Estimate preprocessing parameters only from permitted training rows |
| A value appears before publication | Compare the join key and direction with `publication_time` | Align backward from each decision time; do not join by period end alone |

## Optional extensions

- Replace five-session momentum with a trailing moving-average gap and document the new lookback.
- Add a one-session publication delay to an artificial variable and use `shift(1)` to enforce it.
- Write a prefix test that recalculates a feature using data only through selected historical dates.
- Compare a long-or-cash signal with a three-state signal `{-1, 0, 1}` without claiming performance.

## Official software references

- [`pandas.DataFrame.shift`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.shift.html)
- [`pandas.DataFrame.rolling`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.rolling.html)
- [`pandas.merge_asof`](https://pandas.pydata.org/docs/reference/api/pandas.merge_asof.html)
- [`pandas.bdate_range`](https://pandas.pydata.org/docs/reference/api/pandas.bdate_range.html)
- [`numpy.where`](https://numpy.org/doc/stable/reference/generated/numpy.where.html)

## Financial research reference

- Jegadeesh, N., & Titman, S. (1993). [Returns to Buying Winners and Selling Losers: Implications for Stock Market Efficiency](https://onlinelibrary.wiley.com/doi/10.1111/j.1540-6261.1993.tb04702.x). *The Journal of Finance, 48*(1), 65–91.

The paper supports established academic use of past-return momentum, but its assets, formation periods, holding periods, and evidence are not the Week 3 five-session artificial exercise. Software documentation explains operations. None of these sources determines when a value in another dataset was historically available; that requires the provider definition and publication record.
