# Week 5 — Time-Series Evaluation, Updating, and Rebalancing

## Observable outcome

By the end of this 150-minute class, you will generate walk-forward predictions with expanding and rolling estimation windows, convert them into daily signals, and compare daily with periodic portfolio updates. Each generated prediction will record the model version, number of training rows, last training feature date, and last training outcome date used.

## Teaching Objectives

After completing this class, you should be able to:

- distinguish fixed, rolling, expanding, and walk-forward evaluation;
- explain why a random split can violate the financial decision sequence;
- fit a simple model using only outcomes known at each historical prediction time;
- distinguish model retraining, signal recalculation, and portfolio rebalancing; and
- compare update rules without reusing a final test period for repeated selection.

## Before you begin

Bring the Week 3 timing record and Week 4 position alignment. Use `numpy` and `pandas`. Week 4 fixed the signal-to-position lag; this lesson keeps that lag while changing when a model is refitted, a signal is recalculated, and a position may change. Week 6 will attach costs to the resulting position changes. The simple least-squares model exposes the time sequence; it is not evidence of predictive ability.

## 150-minute learning sequence

| Time | Focus | Evidence produced |
|---:|---|---|
| 0–10 minutes | Which rows are known? | Historical training boundary |
| 10–25 minutes | Fixed, rolling, and expanding windows | Window comparison table |
| 25–45 minutes | Worked prediction-time boundary | Row-by-row availability table |
| 45–60 minutes | Student boundary diagram | Prediction-time diagram |
| 60–80 minutes | Artificial features and target | Audited modeling table |
| 80–105 minutes | Expanding and rolling estimation | Two prediction series |
| 105–120 minutes | Retraining versus signal updating | Model-version evidence |
| 120–135 minutes | Rebalancing and an independent update rule | Position comparison and independent checks |
| 135–145 minutes | Protected test period | Selection rule statement |
| 145–150 minutes | Exit evidence | Valid evaluation description |

## 1. Evaluation must follow time

A fixed split estimates a model once on an earlier block and evaluates it on a later block. An expanding window adds all newly available observations. A rolling window keeps only a fixed number of recent observations. Walk-forward evaluation repeats prediction and later evaluation in chronological order. For a prediction at feature-row position 100, an example fixed design can fit once on positions 0–79, an expanding design can fit on positions 0–99, and an 80-row rolling design can fit on positions 20–99. The exact rows depend on the stated minimum history, target horizon, and refit schedule.

A random assignment can place a later row in training and an earlier row in evaluation. That may answer a question under independent and identically distributed sampling assumptions, but it does not reproduce a historical financial decision in which later outcomes were unavailable. Week 5 therefore preserves chronological order.

At prediction time $t$, a model for $r_{t+1}$ may use features available through $t$ and training outcomes that have already occurred. It may not use $r_{t+1}$ or any later outcome.

### Worked example — one-step-ahead target availability

Assume every row is available after its closing boundary and the target stored on row $j$ is the return that ends at boundary $j+1$. A prediction made after the close at row 5 for the return ending at row 6 has the following status:

| Feature row | Stored target ends at | Target status after close at row 5 | Permitted use |
|---:|---:|---|---|
| 0 | 1 | known | training |
| 1 | 2 | known | training |
| 2 | 3 | known | training |
| 3 | 4 | known | training |
| 4 | 5 | known | training |
| 5 | 6 | unknown | current prediction only; not training |

The final permitted training feature row is 4, but its target becomes known at row 5. This is why a reproducibility record needs both the last training feature date and the time when its target became observable. If row 5 appears inside its own training sample, first inspect the slice endpoint before fitting the model.

### Activity 1 — Draw the permitted rows

For prediction feature-row positions 100, 101, and 102, draw the training rows for:

- an expanding window beginning at row 0; and
- a rolling window of 80 observations.

Mark the feature row used for the current prediction, the ending boundary of the last permitted training target, and the future return that is still unknown. Before drawing, predict whether the last training feature date and last training outcome date are the same. A correct diagram never places the current prediction target inside its own training sample.

Expected evidence shows that the last training feature row precedes the current feature row, while its one-step-ahead target ends at the current boundary. If the two dates are recorded as identical, first separate the feature row from the later boundary at which its target return ends.

## 2. Build an artificial modeling table

```python
import numpy as np
import pandas as pd

rng = np.random.default_rng(2027)
dates = pd.bdate_range("2023-01-03", periods=280)
log_return = rng.normal(0.00015, 0.011, len(dates))
price = 100 * np.exp(np.cumsum(log_return))

model_data = pd.DataFrame({"Adj_Close": price}, index=dates)
model_data["ret_1d"] = model_data["Adj_Close"].pct_change(fill_method=None)
model_data["mom_5"] = model_data["Adj_Close"] / model_data["Adj_Close"].shift(5) - 1
model_data["vol_20"] = (
    model_data["ret_1d"].rolling(20).std(ddof=1) * np.sqrt(252)
)
model_data["target_next_return"] = model_data["ret_1d"].shift(-1)
model_data = model_data.dropna().copy()

print(model_data.head().round(6))
print("Usable rows:", len(model_data))
assert model_data.index.is_monotonic_increasing
assert not model_data.isna().any().any()
assert len(model_data) == 259
```

Expected evidence: 259 usable rows, a sorted index, and no missing value in the modeling table. If the row count differs, inspect the first valid dates of `vol_20` and `target_next_return` before changing `dropna()`. As in Week 3, `vol_20` uses $\sqrt{252}$ as an explicit classroom annualization convention. The target in row $t$ is the return at $t+1$. It can be used for training only after that later return has occurred.

## 3. Walk forward without future outcomes

The model is

$$
\widehat r_{t+1}=\beta_0+\beta_1m_{t,5}+\beta_2\widehat\sigma_{t,20}.
$$

The following function refits every five prediction dates. Between refits, the coefficients remain fixed while a new signal can still be calculated from the latest feature row.

```python
def walk_forward_predict(frame, minimum_train=80, window=None, refit_every=5):
    prediction = pd.Series(np.nan, index=frame.index, name="prediction")
    version = pd.Series(pd.NA, index=frame.index, dtype="Int64", name="model_version")
    train_count = pd.Series(pd.NA, index=frame.index, dtype="Int64", name="training_rows")
    train_end = pd.Series(pd.NaT, index=frame.index, name="last_training_feature_date")
    outcome_end = pd.Series(pd.NaT, index=frame.index, name="last_training_outcome_date")

    beta = None
    version_number = 0
    fitted_through = pd.NaT
    outcomes_known_through = pd.NaT
    fitted_row_count = 0

    for i in range(minimum_train, len(frame)):
        if beta is None or (i - minimum_train) % refit_every == 0:
            history = frame.iloc[:i].copy()
            if window is not None:
                history = history.iloc[-window:]

            x_train = np.column_stack(
                [np.ones(len(history)), history[["mom_5", "vol_20"]].to_numpy()]
            )
            y_train = history["target_next_return"].to_numpy()
            beta = np.linalg.lstsq(x_train, y_train, rcond=None)[0]
            version_number += 1
            fitted_row_count = len(history)
            fitted_through = history.index[-1]
            last_training_location = frame.index.get_loc(fitted_through)
            outcomes_known_through = frame.index[last_training_location + 1]

        x_now = np.r_[1.0, frame.iloc[i][["mom_5", "vol_20"]].to_numpy()]
        prediction.iloc[i] = x_now @ beta
        version.iloc[i] = version_number
        train_count.iloc[i] = fitted_row_count
        train_end.iloc[i] = fitted_through
        outcome_end.iloc[i] = outcomes_known_through

    return pd.concat(
        [prediction, version, train_count, train_end, outcome_end], axis=1
    )

expanding = walk_forward_predict(model_data, window=None)
rolling = walk_forward_predict(model_data, window=80)

print(expanding.dropna().head(8))
print(rolling.dropna().head(8))

assert (expanding.dropna()["last_training_feature_date"] < expanding.dropna().index).all()
assert (rolling.dropna()["last_training_feature_date"] < rolling.dropna().index).all()
assert (expanding.dropna()["last_training_outcome_date"] <= expanding.dropna().index).all()
assert (rolling.dropna()["last_training_outcome_date"] <= rolling.dropna().index).all()
assert expanding["prediction"].notna().sum() == 179
assert rolling["prediction"].notna().sum() == 179
assert expanding.dropna()["training_rows"].between(80, 255).all()
assert rolling.dropna()["training_rows"].eq(80).all()
```

Expected evidence: each design produces 179 predictions. The last training feature date must be earlier than the current prediction date, and the associated target for that last training row must have occurred no later than the current boundary. If a timing assertion fails, inspect the `frame.iloc[:i]` endpoint and the target horizon before looking at model performance.

Compare prediction errors on the same dates. For $n$ common observations, mean squared error is

$$
\mathrm{MSE}=\frac{1}{n}\sum_{i=1}^{n}(r_i-\widehat r_i)^2.
$$

It is measured in squared decimal-return units. Directional accuracy is the fraction of common observations for which $\mathrm{sign}(\widehat r_i)=\mathrm{sign}(r_i)$. The zero-prediction benchmark asks whether the fitted model reduces MSE relative to $\widehat r_i=0$ on exactly the same outcomes. These measures describe prediction, not net trading performance.

```python
forecast_rows = []
for name, output in {"expanding": expanding, "rolling_80": rolling}.items():
    common = output["prediction"].notna() & model_data["target_next_return"].notna()
    prediction = output.loc[common, "prediction"]
    outcome = model_data.loc[common, "target_next_return"]
    forecast_rows.append({
        "method": name,
        "observations": int(common.sum()),
        "mean_squared_error": float(np.mean((outcome - prediction) ** 2)),
        "directional_accuracy": float(np.mean(np.sign(prediction) == np.sign(outcome))),
        "zero_prediction_mse": float(np.mean(outcome ** 2)),
    })
forecast_comparison = pd.DataFrame(forecast_rows).set_index("method")
print(forecast_comparison.round(8))
assert forecast_comparison["observations"].nunique() == 1
assert np.isfinite(forecast_comparison.to_numpy()).all()
```

Both rows must use 179 common observations and contain finite values. A lower error on this artificial path describes only this exercise; it does not establish a profitable trading rule.

### Activity 2 — Compare window memory

Select the final prediction date. Report the number of training rows used by the expanding and rolling designs, compare their common-date errors with the zero-prediction benchmark, then explain one benefit and one risk of discarding older data. Do not choose a design from this one artificial path alone.

Expected evidence shows 255 training rows for the expanding model used on the final prediction date and 80 for the rolling model. Both error comparisons must use the same 179 outcomes. If an expanding count of 258 is reported, first check whether the model was actually refitted on the final prediction date or whether its most recent fitted version was reused.

## 4. Separate three update decisions

- **Model retraining** changes fitted parameters.
- **Signal recalculation** applies the current model to new features.
- **Portfolio rebalancing** changes the held position.

They need not occur at the same frequency.

```python
evaluation = model_data.join(
    expanding.add_prefix("expanding_")
).join(
    rolling.add_prefix("rolling_")
)

evaluation["daily_signal"] = np.where(
    evaluation["expanding_prediction"].isna(), np.nan,
    np.where(evaluation["expanding_prediction"] > 0, 1.0, 0.0),
)

rebalance_day = pd.Series(
    np.arange(len(evaluation)) % 5 == 0,
    index=evaluation.index,
)
evaluation["periodic_signal"] = (
    pd.Series(evaluation["daily_signal"], index=evaluation.index)
    .where(rebalance_day)
    .ffill()
    .fillna(0.0)
)

evaluation["daily_position"] = evaluation["daily_signal"].shift(1).fillna(0.0)
evaluation["periodic_position"] = evaluation["periodic_signal"].shift(1).fillna(0.0)

update_summary = pd.Series(
    {
        "model_refits": int(evaluation["expanding_model_version"].nunique()),
        "daily_signal_changes": int(evaluation["daily_signal"].diff().abs().fillna(0).sum()),
        "daily_position_changes": int(evaluation["daily_position"].diff().abs().fillna(0).sum()),
        "periodic_position_changes": int(evaluation["periodic_position"].diff().abs().fillna(0).sum()),
    }
)
print(update_summary)
assert evaluation.loc[
    evaluation["expanding_prediction"].isna(), "daily_signal"
].isna().all()
assert update_summary["model_refits"] == 36
assert update_summary["periodic_position_changes"] <= update_summary[
    "daily_position_changes"
]
```

### Activity 3 — Explain the counts

Expected evidence: 36 model versions, 21 daily signal changes, 21 daily position changes, and 9 periodic position changes for this fixed artificial path. If these values differ, first inspect the rebalance mask, forward fill, and one-row position shift. Missing predictions remain missing signals, while the position is explicitly initialized to cash until the previous signal is available. Explain why the number of model refits, signal changes, and position changes differ. Then identify which count is most directly connected to trading turnover.

## 5. Independently change the update schedule

Before coding, predict how the number of model versions changes when `refit_every=10` while `minimum_train=80` remains fixed. Generate a new expanding-window output without modifying the original `expanding` table. Then permit the portfolio signal to update only on every tenth row, carry the most recently permitted signal between those rows, shift once to form the position, and count position changes.

Expected evidence contains 179 predictions and 18 model versions. The position must remain in cash before the preceding permitted signal is available, and the periodic position must change no more often than the daily position on this fixed path. Save the prediction, new output columns, counts, passed assertions, and a revision of the initial prediction. If the model-version count differs, first list the refit indices beginning at 80; if the position changes on a non-update row, first inspect the update mask before the position shift.

## 6. Protect the final test period

If window length, refit frequency, threshold, and rebalance frequency are repeatedly chosen using the same final period, that period becomes part of model selection. It can no longer serve as untouched evidence.

Write a rule that separates:

```text
Training period:
Validation period and permitted choices:
Frozen test period:
What cannot change after the test is opened:
```

Expected evidence names the choices permitted during validation and freezes them before the final test is examined. If a setting was changed after viewing final-test results, first relabel that period as used evidence rather than describing it as untouched.

## 7. Required evidence and completion

The Week 5 timing records, common-date comparisons, and separated update schedules provide evidence about estimation and updating for the [Report 1 criteria](../10/week10_main.md). This lesson supplies evidence for those criteria; it does not change the report weights or add another criterion.

Submit the window diagram, modeling-table audit, expanding and rolling predictions, common-date forecast comparison, model-version evidence, the summary for updates permitted every fifth row, the independent comparison for updates permitted every tenth row, protected-period rule, and the design record in [Week 5 Support](week5_support.md#evaluation-design-record).

Your work is complete when another student can reproduce each historical prediction and verify that the model, signal, and position used no information from after their stated times.

## Exit evidence

```text
My model is retrained every ______.
My signal is recalculated every ______.
My portfolio is rebalanced every ______.
The last training outcome for prediction time t is ______.
My frozen test period is protected by ______.
```
