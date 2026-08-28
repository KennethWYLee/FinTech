# Week 4 — Basic Backtesting

## Observable outcome

After completing Week 4, you will produce a complete long-or-cash backtest ledger that connects price, return, signal, position, trade, strategy return, and wealth. You will compare correctly lagged and invalid same-period implementations, verify a miniature ledger by hand, and repair a separate alignment error. Costs are deliberately postponed to Week 6; every Week 4 strategy result is gross of costs.

## Teaching Objectives

After completing this class, you should be able to:

- define entry, exit, holding, cash, permitted positions, and execution assumptions;
- convert an after-close signal into the next eligible position;
- distinguish a signal, position, trade, asset return, and strategy return;
- verify a backtest with timing and accounting identities; and
- describe the result without claiming future profitability.

## Before you begin

Complete the Week 3 timing record. Use a notebook with `numpy` and `pandas`. Week 3 defined when the signal becomes available; this lesson applies that time to a position and a return. Week 5 will preserve the same timing while changing model, signal, and rebalancing update schedules. Detailed ledger fields and troubleshooting are in [Week 4 Support](week4_support.md).

## 1. Write the strategy specification first

Use the following Week 4 assumptions:

- Data frequency: one artificial observation per business day.
- Feature: five-session momentum calculated after close $t$.
- Signal: 1 when momentum is positive and 0 when it is nonpositive; it is missing before momentum is available.
- Decision: recorded after the closing information for period $t$ becomes available.
- Execution: position changes at the boundary before the next artificial return period. This discrete-time assumption does not establish that the same price would be fillable in real trading.
- Holding return: close-to-close return for the period after the signal.
- Position: 1 means fully invested in the artificial asset; 0 means cash.
- Cash return: 0 for this exercise.
- Short selling and leverage: not permitted.
- Initialization: remain in zero-return cash until a signal is available.
- Transaction costs: 0 in Week 4 and introduced in Week 6.
- Final boundary: report the position shown in the final row; do not force a liquidation trade in Week 4.

The return at date $t$ is the change from the artificial price at boundary $t-1$ to the price at boundary $t$. Under the stated discrete-time assumption, a signal formed at boundary $t-1$ determines the position for that next interval. Therefore,

$$
p_t=s_{t-1}, \qquad r^{\text{strategy}}_t=p_t r^{\text{asset}}_t.
$$

Here, $s_{t-1}$ is the signal available after the preceding boundary, $p_t$ is the exposure held during the return interval ending at $t$, and each return is expressed as a decimal. A change from position 0 to 1 or from 1 to 0 has absolute size 1. Week 6 will attach a cost to that change.

### Worked example — four dated rows

The initial position is cash because no preceding signal is shown. Wealth begins at 1. The strategy earns a return only when the position for that interval is 1.

| Date | Asset return | Signal after close | Position for current return | Absolute position change | Gross strategy return | Gross wealth |
|---|---:|---:|---:|---:|---:|---:|
| A | missing | 1 | 0 | 0 | 0.0% | 1.0000 |
| B | 2.0% | 0 | 1 | 1 | 2.0% | 1.0200 |
| C | -1.0% | 1 | 0 | 1 | 0.0% | 1.0200 |
| D | 3.0% | 1 | 1 | 1 | 3.0% | 1.0506 |

For Date B, the preceding signal is 1, so the position is 1 and the strategy earns $1\times0.02=0.02$. Wealth becomes $1.0000(1+0.02)=1.0200$. For Date C, the preceding signal is 0, so the strategy remains in cash despite the asset return of -1.0%. The Date C signal affects Date D, not Date C. If the Date B position is recorded as 0, first compare it with the signal in Date A rather than the signal in Date B.

### Activity 1 — Hand-complete the ledger

For this abbreviated five-date ledger only, assume the feature history was observed before Date 1, so its displayed signals are available. The Python example below separately shows the initial missing-feature period.

| Date | Asset return | Signal after close | Position for current return | Strategy return |
|---|---:|---:|---:|---:|
| 1 | missing | 0 | 0 | 0.0% under the stated initial-cash rule |
| 2 | 2.0% | 1 | 0 |  |
| 3 | -1.0% | 1 | 1 |  |
| 4 | 3.0% | 0 | 1 |  |
| 5 | -2.0% | 0 | 0 |  |

Explain why the signal shown on Date 2 cannot earn the Date 2 return.

Expected evidence includes positions equal to the preceding row's signals and strategy returns equal to position times asset return. Before calculating, predict which of Dates 2–5 have nonzero strategy returns. If a position instead equals the signal on the same row, first draw an arrow from each signal to the next return interval.

## 2. Build the artificial strategy input

```python
import numpy as np
import pandas as pd

rng = np.random.default_rng(2026)
dates = pd.bdate_range("2024-01-02", periods=180)
log_return = rng.normal(0.0002, 0.012, len(dates))
price = 100 * np.exp(np.cumsum(log_return))

bt = pd.DataFrame({"Adj_Close": price}, index=dates)
bt.index.name = "Date"
bt["asset_return"] = bt["Adj_Close"].pct_change(fill_method=None)
bt["mom_5"] = bt["Adj_Close"] / bt["Adj_Close"].shift(5) - 1
bt["signal_at_close"] = np.where(
    bt["mom_5"].isna(), np.nan,
    np.where(bt["mom_5"] > 0, 1.0, 0.0),
)

assert bt.index.is_unique and bt.index.is_monotonic_increasing
assert set(bt["signal_at_close"].dropna().unique()).issubset({0.0, 1.0})
print(bt.head(8).round(6))
```

Expected evidence: the first five momentum values and signals are missing, and the first available signal appears only after five price changes can be measured. If a zero appears instead of a missing early signal, inspect the nested `np.where`. The signal is known at its row's decision boundary but is not yet aligned with the return it may earn.

## 3. Create the valid backtest ledger

```python
bt["position"] = bt["signal_at_close"].shift(1).fillna(0.0)
bt["trade"] = bt["position"].diff().fillna(bt["position"]).abs()
bt["strategy_return_gross"] = bt["position"] * bt["asset_return"]
bt["strategy_return_gross"] = bt["strategy_return_gross"].fillna(0.0)
bt["strategy_wealth_gross"] = (1 + bt["strategy_return_gross"]).cumprod()
bt["buy_hold_wealth"] = (1 + bt["asset_return"].fillna(0.0)).cumprod()

ledger_columns = [
    "Adj_Close", "asset_return", "mom_5", "signal_at_close",
    "position", "trade", "strategy_return_gross", "strategy_wealth_gross"
]
print(bt[ledger_columns].head(12).round(6))

previous_signal = bt["signal_at_close"].shift(1)
assert bt.loc[previous_signal.notna(), "position"].equals(
    previous_signal.loc[previous_signal.notna()]
)
assert bt.loc[previous_signal.isna(), "position"].eq(0.0).all()
assert bt["position"].between(0, 1).all()
assert bt["trade"].isin([0.0, 1.0]).all()
assert bt["strategy_wealth_gross"].gt(0).all()
```

Expected evidence: the position remains in cash while the previous signal is unavailable; afterward, the current position equals the previous row's signal. Trades occur only when the position changes, and wealth remains positive. These checks verify the stated mechanics, not economic value.

### Activity 2 — Explain one entry and one exit

Find the first row where `trade == 1`. Report the previous signal, current position, current asset return, and current strategy return. Repeat for the next exit. Explain the rows in words.

Expected evidence shows an entry with current position 1 following a preceding signal of 1 and an exit with current position 0 following a preceding signal of 0. If the first selected row does not change position, first confirm that `trade` is the absolute difference of consecutive positions.

## 4. Detect an invalid same-period backtest

This flawed implementation uses a signal formed at boundary $t$ to earn the return that ended at that boundary:

```python
bt["invalid_same_period_return"] = (
    bt["signal_at_close"] * bt["asset_return"]
).fillna(0.0)
bt["invalid_same_period_wealth"] = (
    1 + bt["invalid_same_period_return"]
).cumprod()

comparison = pd.Series(
    {
        "valid_final_wealth": bt["strategy_wealth_gross"].iloc[-1],
        "invalid_final_wealth": bt["invalid_same_period_wealth"].iloc[-1],
        "rows_with_different_returns": int(
            (~np.isclose(
                bt["strategy_return_gross"],
                bt["invalid_same_period_return"],
            )).sum()
        ),
    }
)
print(comparison.round(6))
assert comparison["rows_with_different_returns"] > 0
```

Expected evidence: 29 rows differ; gross final wealth is approximately 1.201040 under the valid timing and 1.639531 under the invalid timing for this fixed artificial path. If no row differs, inspect whether the signal ever changes and whether both formulas accidentally use the same position. The invalid result may be higher or lower on another path. Its direction is irrelevant: the evidence is invalid because the timing rule is impossible.

### Activity 3 — State the defect

Write the earliest time at which `signal_at_close[t]` is known and the return interval represented by `asset_return[t]`. Use those two facts to reject the same-period implementation. For a real strategy, also identify an execution price that could actually be obtained after the signal is known.

Expected evidence states that the signal is available only after the close at $t$, whereas `asset_return[t]` ended at that close. If the explanation relies only on the difference in final wealth, first replace it with the two relevant time boundaries.

## 5. Verify accounting and benchmark meaning

The number of position changes is not the number of invested days. The trade field records changes in exposure; the position field records exposure held during a return interval.

```python
summary = pd.Series(
    {
        "observations": len(bt),
        "invested_periods": int(bt["position"].sum()),
        "position_changes": int((bt["trade"] > 0).sum()),
        "gross_final_wealth": bt["strategy_wealth_gross"].iloc[-1],
        "buy_hold_final_wealth": bt["buy_hold_wealth"].iloc[-1],
    }
)
print(summary.round(6))

assert summary["invested_periods"] == 99
assert summary["position_changes"] == 28
assert np.isclose(
    bt["strategy_wealth_gross"].iloc[-1],
    np.prod(1 + bt["strategy_return_gross"]),
)
```

Expected evidence: 180 observations, 99 invested periods, 28 position changes, gross final strategy wealth of approximately 1.201040, and buy-and-hold wealth of approximately 1.249013. Stored final strategy wealth must equal directly compounded strategy returns. If the identity fails, locate the first row where the wealth recursion differs. Buy-and-hold answers what happened to continuous exposure to the same artificial asset. It does not by itself make the strategy valid, and a difference on one artificial path is not a market finding.

### Activity 4 — Audit the transformation chain

For one selected date, show:

```text
price history -> feature -> signal time -> next position -> asset return interval
-> strategy return -> wealth
```

Each arrow must name the formula or shift used.

Expected evidence identifies the exact price rows used by the feature, the time when the signal is available, the one-row position shift, the return interval, and the wealth update. If an arrow skips from signal directly to same-row return, first insert the position and execution boundary.

## 6. Independent alignment repair

Use this different six-row artificial input:

| Row | Asset return | Signal after close |
|---:|---:|---:|
| 0 | missing | missing |
| 1 | 1.0% | 1 |
| 2 | -2.0% | 0 |
| 3 | 3.0% | 1 |
| 4 | 1.0% | 1 |
| 5 | -1.0% | 0 |

Before coding, predict the first row on which a position of 1 may earn a return. Create the input as a separate `DataFrame`, then calculate an invalid position from the same-row signal and a valid position from the preceding signal. Initialize unavailable preceding signals to cash. For both versions, calculate gross strategy return and gross wealth from 1.

Required checks are that the valid position equals the preceding signal wherever that signal exists, the initial valid position is 0, at least one valid return differs from its same-row counterpart, and the valid final wealth equals the direct product of one plus the valid returns. Save the prediction, both ledgers, passed assertions, and a sentence that identifies the first differing row without claiming which alignment has better investment performance. If no row differs, first inspect whether both position columns used the same shift.

## 7. Required evidence and completion

The Week 4 specification, ledger, timing comparison, and limitations are direct evidence for the [Report 1 criteria](../10/week10_main.md). This lesson supplies evidence for those criteria; it does not change the report weights or add another criterion.

Submit:

1. the written strategy specification;
2. the hand-completed ledger;
3. the complete Python ledger and passed assertions;
4. one entry and one exit explanation;
5. the valid-versus-invalid alignment comparison;
6. the benchmark summary;
7. the independently repaired six-row ledger; and
8. the reproducibility record in [Week 4 Support](week4_support.md#backtest-record).

Your work is complete when another student can reconstruct every transformation and confirm that no signal earns a return that ended before the signal existed.

## Exit evidence

```text
My signal is known at ______.
My position earns the return from ______ to ______.
The required lag is ______.
One accounting identity that passed is ______.
Costs are still missing, so I cannot yet claim ______.
```
