# Week 3 — Information Timing, Features, and Signals

## Observable outcome

By the end of this 150-minute class, you will convert one financial hypothesis into a documented feature and a simple signal without using information that was unavailable at the decision time. You will preserve a time-boundary table, feature calculations, leakage checks, an alignment based on publication times, and a signal table. The artificial data demonstrate mechanics only and do not support a claim about a real market.

## Teaching Objectives

After completing this class, you should be able to:

- distinguish observation, publication, signal, decision, execution, and evaluation times;
- translate a financial hypothesis into a measurable feature with a stated economic interpretation;
- apply lags that respect when information becomes available;
- align a separately published variable to decision times using only values already available;
- identify future values, centered calculations, and full-data preprocessing that cause information leakage; and
- produce a simple signal while postponing performance claims until a valid backtest exists.

## Before you begin

Complete Week 2 or be able to calculate returns from a sorted `DatetimeIndex`. Use a notebook with `numpy` and `pandas`. Week 2 supplied the return definitions used here; Week 4 will add positions, execution, and performance measurement after the timing in this lesson is correct. The detailed preparation and terminology are in [Week 3 Support](week3_support.md).

## 150-minute learning sequence

| Time | Focus | Evidence produced |
|---:|---|---|
| 0–10 minutes | Can this variable be used now? | Initial timing judgment |
| 10–25 minutes | Six times in a financial decision | Completed time-boundary table |
| 25–40 minutes | Hypothesis before feature | Hypothesis–feature statement |
| 40–55 minutes | Lags and availability | Correctly aligned example |
| 55–70 minutes | Build artificial data with dated rows | Audited input table |
| 70–85 minutes | Construct momentum and volatility features | Feature table with definitions |
| 85–100 minutes | Detect leakage | Candidate-feature audit |
| 100–115 minutes | Convert a feature into a signal | Signal rule and coverage evidence |
| 115–145 minutes | Align a separately published variable | Alignment by publication time and an independent repair exercise |
| 145–150 minutes | Exit evidence | Supported and unsupported claims |

## 1. Six different times

Financial data become usable through a sequence of events:

- **Observation time**: when the underlying event occurs.
- **Publication time**: when the value becomes available to the analyst.
- **Signal time**: when the feature and signal can be calculated.
- **Decision time**: when the portfolio rule accepts the signal.
- **Execution time**: when a trade can occur under the stated assumption.
- **Evaluation time**: when the return generated after execution can be measured.

These times may be equal for a carefully defined end-of-day price workflow, but they must not be assumed equal. A closing price is not known before that close. A financial statement for quarter \(q\) cannot be treated as known throughout quarter \(q\) if it was published later.

### Worked example — one artificial decision timeline

Assume an artificial closing price is observed at 16:00 on March 2 and released to the class dataset at 16:01. A trailing-price feature is calculated at 16:05, the decision is recorded at 16:06, and the stated execution boundary is 09:00 on March 3. The first eligible holding return therefore begins at 09:00 on March 3 and ends at the next stated boundary. A return that ended at 16:00 on March 2 cannot be attributed to this decision because it ended before the signal existed.

### Activity 1 — Complete the boundary

Assume a feature uses today's closing price and is calculated immediately after the market closes. Complete the missing cells:

| Item | Earliest stated time |
|---|---|
| Today's closing price is observed |  |
| Today's closing price becomes available |  |
| Five-session momentum is calculated |  |
| Position decision is recorded |  |
| Earliest permitted execution in this course example |  |
| First return attributable to that position |  |

Your table is complete when no return used for evaluation occurs before the execution time.

Expected evidence places observation no later than publication, publication no later than signal, signal no later than decision, decision no later than execution, and evaluation after execution. If a source has no separate publication delay, observation time and publication time may be the same. If the order fails, first identify the latest source value required by the feature and its actual availability time.

## 2. Start with a hypothesis

A feature is a measurable input. It becomes financially interpretable only when connected to a hypothesis. For this class, use the following working hypothesis for a later backtest:

> In this artificial setting, observations with positive five-session momentum may have a higher mean next-session return than observations with nonpositive five-session momentum.

This is a proposed association to test, not an empirical result. Even if the association appears in a later sample, it would not by itself establish profitability after costs. Define five-session momentum as

\[
m_{t,5}=\frac{P_t}{P_{t-5}}-1.
\]

Define twenty-session realized volatility as

\[
\widehat{\sigma}_{t,20}=\operatorname{sd}(r_{t-19},\ldots,r_t)\sqrt{252}.
\]

The factor \(\sqrt{252}\) is an explicit classroom convention for annualizing daily volatility. It is not a universal statement about the number of trading days in every market or year.

Both features include information through the close at \(t\), so they are available only after that close under this class convention.

### Activity 2 — Write before calculating

Complete these sentences:

```text
Financial idea:
Observable variable:
Formula and lookback:
Last observation used:
Earliest signal time:
Earliest execution time:
What result would contradict the idea:
```

Do not use future profitability as the definition of the feature.

The expected statement identifies an observable input and a result that could contradict the idea; it does not define success as “the strategy makes money.” If the hypothesis cannot be contradicted by any result, first rewrite the proposed relationship before calculating a feature.

## 3. Build the artificial data

Run this cell in a new notebook. The seed fixes the artificial path so another student can reproduce it.

```python
import numpy as np
import pandas as pd

rng = np.random.default_rng(2026)
dates = pd.bdate_range("2024-01-02", periods=180)
artificial_log_return = rng.normal(0.0002, 0.012, len(dates))
adjusted_price = 100 * np.exp(np.cumsum(artificial_log_return))

data = pd.DataFrame({"Adj_Close": adjusted_price}, index=dates)
data.index.name = "Date"
data["ret_1d"] = data["Adj_Close"].pct_change(fill_method=None)

print(data.head())
print(data.tail())
print("Shape:", data.shape)

assert data.index.is_unique
assert data.index.is_monotonic_increasing
assert data["Adj_Close"].gt(0).all()
assert data["ret_1d"].isna().sum() == 1
assert pd.isna(data["ret_1d"].iloc[0])
```

Expected evidence: 180 rows, a sorted and unique business-day index, positive prices, and one missing first return. This confirms internal construction only; it does not make the series real market data.

The business-day index is an artificial sequence and is not evidence of any market's actual holidays or trading sessions.

## 4. Construct features using observations through time \(t\)

```python
data["mom_5"] = data["Adj_Close"] / data["Adj_Close"].shift(5) - 1
data["vol_20"] = data["ret_1d"].rolling(20).std(ddof=1) * np.sqrt(252)
data["next_return_target"] = data["ret_1d"].shift(-1)

feature_table = data[["Adj_Close", "ret_1d", "mom_5", "vol_20"]]
print(feature_table.tail(8).round(6))

assert data["mom_5"].first_valid_index() == data.index[5]
assert data["vol_20"].first_valid_index() == data.index[20]
```

Expected evidence: `mom_5` first appears on the sixth price date, `vol_20` first appears after 20 returns are available, and the last `next_return_target` is missing. If either first-valid-date assertion fails, count the prices or returns required by the formula before changing the window. `next_return_target` is stored only as a future outcome for later model evaluation. It is not an allowed feature at time \(t\).

### Activity 3 — Explain the first valid date

Before running the feature cell, predict the first row position with a valid `mom_5` and the first row position with a valid `vol_20`. After running it, revise the prediction if necessary and explain why the two features begin on different dates. Your explanation must refer to the number of required prices or returns, not merely say that `pandas` created missing values.

The expected explanation distinguishes six prices needed for a five-session price change from 20 nonmissing returns needed for the volatility estimate. If the dates still appear unexplained, first list every source row required for the first candidate value.

## 5. Audit candidate features for leakage

Leakage can enter directly through a future value, indirectly through a centered transformation, or through preprocessing estimated with the full dataset.

Run the intentionally invalid candidates:

```python
data["leaked_future_return"] = data["ret_1d"].shift(-1)
data["leaked_centered_mean"] = data["Adj_Close"].rolling(5, center=True).mean()

candidate_audit = pd.DataFrame(
    {
        "candidate": ["mom_5", "vol_20", "leaked_future_return", "leaked_centered_mean"],
        "uses_value_after_t": [False, False, True, True],
        "allowed_after_close_t": [True, True, False, False],
        "reason": [
            "uses prices through t",
            "uses returns through t",
            "contains return at t+1",
            "centered window includes later prices",
        ],
    }
).set_index("candidate")

print(candidate_audit)
assert not candidate_audit.loc[candidate_audit["uses_value_after_t"], "allowed_after_close_t"].any()
```

Expected evidence: `mom_5` and `vol_20` are allowed after the close under the stated timing, while both intentionally leaked candidates are rejected. The Boolean columns document the student's source-date audit; this table does not automatically detect leakage. If an invalid candidate is marked allowed, inspect its latest source date rather than its column name.

### Activity 4 — Audit a preprocessing rule

A student standardizes `mom_5` using the mean and standard deviation calculated from all 180 rows before splitting the data. Decide whether the feature formula itself leaks, whether the preprocessing leaks, and how the parameters should instead be estimated. Record the answer in three sentences.

The expected judgment separates a valid trailing feature formula from invalid full-sample parameter estimation. If both are labeled valid or both invalid, first identify which rows enter each calculation and when those rows become available.

## 6. Convert the feature into a signal

When the momentum feature is available, define a simple long-or-cash signal after close \(t\):

\[
s_t=\begin{cases}
1,&m_{t,5}>0,\\
0,&m_{t,5}\leq 0.
\end{cases}
\]

Before the first valid momentum value, the signal remains missing rather than being treated as a cash decision. A valid signal is not yet a position and does not yet have a return. Week 4 will state the execution rule and shift the signal before measuring strategy performance.

```python
data["signal_at_close"] = np.where(
    data["mom_5"].isna(), np.nan,
    np.where(data["mom_5"] > 0, 1.0, 0.0),
)

signal_evidence = pd.Series(
    {
        "observations": len(data),
        "feature_available": int(data["mom_5"].notna().sum()),
        "signal_available": int(data["signal_at_close"].notna().sum()),
        "long_signals": int((data["signal_at_close"] == 1).sum()),
        "cash_signals": int((data["signal_at_close"] == 0).sum()),
    }
)
print(signal_evidence)

assert set(data["signal_at_close"].dropna().unique()).issubset({0.0, 1.0})
assert data["signal_at_close"].isna().equals(data["mom_5"].isna())
```

Expected evidence: 175 available features and signals, with available signals partitioned into long and cash decisions. If `long_signals + cash_signals` does not equal `signal_available`, inspect missing-value handling before interpreting the counts.

### Activity 5 — Interpret without overclaiming

Write one sentence describing exactly what a signal of 1 means. Then write one sentence that would be invalid because Week 3 has not yet defined execution, costs, or a backtest.

The valid sentence describes only the rule output after close \(t\). If it mentions an earned return, profit, or trade fill, first remove that claim because position and execution rules begin in Week 4.

## 7. Align a separately published variable

A variable can describe an earlier period but remain unavailable until its publication time. Joining it to prices by the period-end label would make it appear available too early. The following worked example uses artificial timestamps and values only.

```python
decisions = pd.DataFrame(
    {
        "decision_time": pd.to_datetime(
            ["2026-04-02 16:05", "2026-04-03 16:05", "2026-04-06 16:05"]
        )
    }
).sort_values("decision_time")

releases = pd.DataFrame(
    {
        "period_end": pd.to_datetime(["2026-03-31"]),
        "publication_time": pd.to_datetime(["2026-04-03 08:00"]),
        "indicator_value": [1.20],
    }
).sort_values("publication_time")

aligned = pd.merge_asof(
    decisions,
    releases,
    left_on="decision_time",
    right_on="publication_time",
    direction="backward",
)

print(aligned)

assert pd.isna(aligned.loc[0, "indicator_value"])
assert aligned.loc[1:, "indicator_value"].eq(1.20).all()
assert (
    aligned.loc[aligned["indicator_value"].notna(), "publication_time"]
    <= aligned.loc[aligned["indicator_value"].notna(), "decision_time"]
).all()
```

Expected evidence shows a missing value for the April 2 decision and 1.20 for the April 3 and April 6 decisions. If April 2 receives 1.20, first check whether the join used `period_end` instead of `publication_time` or allowed a forward match.

### Independent alignment exercise

Create a different artificial release with a period end of May 31, a value of -0.40, and a publication time of June 3 at 12:00. Align it to decisions at June 2 at 16:05, June 3 at 10:00, and June 3 at 15:00. Before coding, predict which decisions can use the value. Then implement the alignment, assert that no matched publication time exceeds its decision time, and revise the prediction from the output.

Expected evidence contains three decision rows, two missing aligned values, and one aligned value of -0.40. If the counts differ, first display `decision_time`, `publication_time`, and the join direction. Save the prediction, both input tables, aligned output, assertion, and one sentence explaining why period end is not the availability time.

## 8. Required evidence and completion

The Week 3 evidence supplies the timing and leakage documentation needed to defend the quantitative trading work in Report 1. The detailed report rubric remains pending; this lesson does not add a new grading rule.

Submit:

1. the completed time-boundary table and an interpretation of the worked artificial timeline;
2. the hypothesis–feature statement;
3. the artificial input and feature tables, including an explanation of the first valid date for each trailing feature;
4. the candidate-feature audit and preprocessing judgment;
5. the signal rule and signal evidence;
6. the worked and independent publication-time alignments; and
7. the timing record in [Week 3 Support](week3_support.md#information-timing-record).

Your work is complete when another student can identify every observation used by a feature, state when the signal becomes available, rerun the code, and explain why no Week 3 result is yet a trading-performance claim.

## Exit evidence

```text
The latest value used by my feature is ______.
The signal becomes available at ______.
The earliest permitted execution is ______.
One leakage mechanism I checked is ______.
Week 3 does not yet show that ______.
```
