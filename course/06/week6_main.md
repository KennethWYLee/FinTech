# Week 6 — Trading Costs, Execution, and Benchmarks

## Observable outcome

By the end of this 150-minute class, you will convert a gross backtest into a net backtest with explicit commissions, bid–ask spread, slippage, turnover, and execution delay. You will compare the same strategy with cash, buy-and-hold, and a simple rule under identical dates, construct a small equal-weighted comparison under a common asset universe, and report the tested cost level at which a comparison changes.

## Teaching Objectives

After completing this class, you should be able to:

- express trading costs in basis points and map them to position changes;
- distinguish one-way turnover from round-trip language;
- calculate gross and net strategy returns under a stated execution assumption;
- perform cost and execution-delay sensitivity analysis; and
- select benchmarks that answer specific comparison questions.

## Before you begin

Bring the Week 4 ledger and Week 5 update rules. Week 4 supplies the gross-return ledger, and Week 5 supplies the update and rebalancing schedule. This class uses artificial data and assumes zero cash return. Week 7 will evaluate the resulting net returns and their uncertainty. See [Week 6 Support](week6_support.md) for the cost record and troubleshooting.

## 150-minute learning sequence

| Time | Focus | Evidence produced |
|---:|---|---|
| 0–10 minutes | Gross or net? | Missing-cost diagnosis |
| 10–30 minutes | Commission, spread, slippage, and a worked trade | Cost convention and verified calculation |
| 30–45 minutes | Turnover | Independent hand cost calculation |
| 45–60 minutes | Rebuild the strategy ledger | Gross ledger |
| 60–80 minutes | Add net costs | Net ledger and identity checks |
| 80–100 minutes | Cost sensitivity | Cost grid and change in the comparison |
| 100–115 minutes | Execution delay | Delay comparison |
| 115–140 minutes | Cash, buy-and-hold, simple-rule, and equal-weighted comparisons | Common-condition evidence |
| 140–145 minutes | Assumption audit | Missing real-market inputs |
| 145–150 minutes | Exit evidence | Conditional conclusion |

## 1. Define the cost convention

One basis point is $0.0001$ in decimal-return units. For a one-way position change

$$
\tau_t=|p_t-p_{t-1}|,
$$

define the cost rate

$$
c=\frac{c_{\text{commission}}+c_{\text{half-spread}}+c_{\text{slippage}}}{10{,}000}.
$$

Each component in the numerator is stated in basis points; $c$ is therefore in decimal-return units.

The net return is

$$
r^{\text{net}}_t=r^{\text{gross}}_t-c\tau_t.
$$

This convention charges one-way cost on each change. Entering and later exiting a full position creates two one-way changes. If a source quotes a full spread or round-trip cost, convert it before using this formula.

### Worked example — cost on a partial position change

Assume a commission of 1 bp, a quoted full spread of 6 bp, and slippage of 2 bp. The half-spread used for one execution is 3 bp, so the stated one-way rate is

$$
c=\frac{1+3+2}{10{,}000}=0.0006.
$$

If a position changes from 0.25 to 0.75, then $\tau_t=|0.75-0.25|=0.50$. The cost deducted from the portfolio return is $0.0006\times0.50=0.0003$, or 0.03%. If gross return for the period is 0.80%, net return is $0.0080-0.0003=0.0077$, or 0.77%. If the cost appears as 3.0%, first check whether basis points were divided by 10,000 exactly once.

### Activity 1 — Hand-calculate costs

A position changes from 0 to 1. Commission is 1 bp, half-spread is 2 bp, and slippage is 1 bp. Calculate the one-way cost. Then calculate the total cost of entering and later exiting if the same assumptions apply. State whether your answer is decimal or percentage.

Before calculating, predict whether the round-trip amount is one or two one-way charges. Expected evidence shows the component sum in basis points, the conversion to decimal-return units, the number of position changes, and the revised prediction. If decimal and percentage values differ by a factor of 100, first write both units beside the number.

## 2. Rebuild the gross strategy

```python
import numpy as np
import pandas as pd

rng = np.random.default_rng(2028)
dates = pd.bdate_range("2023-01-03", periods=260)
price = 100 * np.exp(np.cumsum(rng.normal(0.0002, 0.012, len(dates))))

cost_data = pd.DataFrame({"Adj_Close": price}, index=dates)
cost_data["asset_return"] = cost_data["Adj_Close"].pct_change(fill_method=None)
cost_data["mom_5"] = cost_data["Adj_Close"] / cost_data["Adj_Close"].shift(5) - 1
cost_data["signal"] = np.where(
    cost_data["mom_5"].isna(), np.nan,
    np.where(cost_data["mom_5"] > 0, 1.0, 0.0),
)
cost_data["position"] = cost_data["signal"].shift(1).fillna(0.0)
cost_data["turnover_one_way"] = cost_data["position"].diff().abs().fillna(
    cost_data["position"].abs()
)
cost_data["gross_return"] = (
    cost_data["position"] * cost_data["asset_return"]
).fillna(0.0)

assert cost_data["position"].between(0, 1).all()
assert cost_data["turnover_one_way"].isin([0.0, 1.0]).all()
assert cost_data.loc[cost_data["signal"].shift(1).isna(), "position"].eq(0.0).all()
print(cost_data.head(10).round(6))
```

Expected evidence: the signal is missing until momentum exists, the position remains in cash until the previous signal is available, and turnover is either zero or one. If a position appears earlier, inspect the signal initialization and shift before calculating costs.

## 3. Add costs and verify the identity

```python
commission_bp = 1.0
half_spread_bp = 2.0
slippage_bp = 1.0
one_way_cost = (commission_bp + half_spread_bp + slippage_bp) / 10_000

cost_data["trading_cost"] = one_way_cost * cost_data["turnover_one_way"]
cost_data["net_return"] = cost_data["gross_return"] - cost_data["trading_cost"]
cost_data["gross_wealth"] = (1 + cost_data["gross_return"]).cumprod()
cost_data["net_wealth"] = (1 + cost_data["net_return"]).cumprod()

assert np.allclose(
    cost_data["net_return"],
    cost_data["gross_return"] - cost_data["trading_cost"],
)
assert (cost_data["gross_wealth"] + 1e-12 >= cost_data["net_wealth"]).all()

summary = pd.Series(
    {
        "position_changes": int((cost_data["turnover_one_way"] > 0).sum()),
        "total_one_way_turnover": cost_data["turnover_one_way"].sum(),
        "total_cost_return_units": cost_data["trading_cost"].sum(),
        "gross_final_wealth": cost_data["gross_wealth"].iloc[-1],
        "net_final_wealth": cost_data["net_wealth"].iloc[-1],
    }
)
print(summary.round(6))
assert summary["position_changes"] == 51
assert np.isclose(summary["total_one_way_turnover"], 51.0)
assert np.isclose(summary["total_cost_return_units"], 0.0204)
```

Expected evidence: 51 position changes, total one-way turnover of 51, total cost deductions of 0.0204 return units, gross final wealth of approximately 0.860369, and net final wealth of approximately 0.843004. Every trading cost is nonnegative, and net return equals gross return minus cost on every row. If the wealth assertion fails, inspect cost signs and verify that all one-period wealth factors remain positive.

### Activity 2 — Explain a cost row

Select one entry or exit row. Substitute the position change and cost components into the formula, then verify the recorded net return.

Expected evidence identifies the preceding and current positions, a turnover of 1, a cost deduction of 0.0004, and a net return exactly 0.0004 below gross return. If the chosen row has no cost, first confirm that its position actually changed.

## 4. Cost sensitivity

```python
cost_data["buy_hold_turnover"] = 0.0
cost_data.loc[cost_data.index[0], "buy_hold_turnover"] = 1.0
cost_data["buy_hold_gross_return"] = cost_data["asset_return"].fillna(0.0)

def final_wealth_at_cost(total_cost_bp):
    rate = total_cost_bp / 10_000
    strategy_net = cost_data["gross_return"] - rate * cost_data["turnover_one_way"]
    buy_hold_net = (
        cost_data["buy_hold_gross_return"]
        - rate * cost_data["buy_hold_turnover"]
    )
    return pd.Series({
        "strategy_final_wealth": (1 + strategy_net).prod(),
        "buy_hold_final_wealth": (1 + buy_hold_net).prod(),
    })

cost_grid_bp = [0, 2, 4, 8, 12, 20, 40]
cost_sensitivity = pd.DataFrame({
    bp: final_wealth_at_cost(bp) for bp in cost_grid_bp
}).T
cost_sensitivity.index.name = "one_way_cost_bp"
print(cost_sensitivity.round(6))

assert cost_sensitivity["strategy_final_wealth"].is_monotonic_decreasing
assert cost_sensitivity["buy_hold_final_wealth"].is_monotonic_decreasing
```

Both final-wealth columns must be nonincreasing as the cost rate rises while positions remain fixed. Each row compares the strategy and buy-and-hold under the same cost rate. If either column rises, confirm that its gross returns and turnover remain fixed across the grid.

### Activity 3 — State when the comparison changes

Calculate `strategy_final_wealth - buy_hold_final_wealth` for every row. Identify the first tested cost at which the difference is nonpositive. If no tested cost crosses zero, report the tested range rather than inventing a break-even value.

Expected evidence shows a positive difference at 2 bp and a nonpositive difference first at the tested 4 bp row. This identifies a change within the tested grid, not the exact break-even cost. If 0 bp is reported as the first change, first verify that both methods use their own turnover and the same cost rate.

## 5. Execution delay

```python
cost_data["position_delay_1"] = cost_data["signal"].shift(2).fillna(0.0)
cost_data["gross_return_delay_1"] = (
    cost_data["position_delay_1"] * cost_data["asset_return"]
).fillna(0.0)
cost_data["turnover_delay_1"] = cost_data["position_delay_1"].diff().abs().fillna(
    cost_data["position_delay_1"].abs()
)
cost_data["net_return_delay_1"] = (
    cost_data["gross_return_delay_1"]
    - one_way_cost * cost_data["turnover_delay_1"]
)

delay_comparison = pd.Series(
    {
        "next_period_net_final_wealth": (1 + cost_data["net_return"]).prod(),
        "one_extra_period_delay_net_final_wealth": (
            1 + cost_data["net_return_delay_1"]
        ).prod(),
        "rows_with_different_positions": int(
            (cost_data["position"] != cost_data["position_delay_1"]).sum()
        ),
    }
)
print(delay_comparison.round(6))
assert np.isfinite(delay_comparison.to_numpy()).all()
assert delay_comparison["rows_with_different_positions"] > 0
```

Expected evidence: 51 rows have different positions; next-period net final wealth is approximately 0.843004 and one-extra-period-delay net final wealth is approximately 0.840783. Both calculations use the same one-way cost components, recalculated from their own positions. The delayed result can move in either direction on another path; the purpose is to reveal sensitivity to the execution assumption. If the positions never differ, inspect the two shifts and confirm that the signal changes during the sample.

## 6. Compare common benchmarks

Cash tests whether taking risk added value under the zero cash-return assumption. Buy-and-hold tests whether timing improved on continuous exposure to the same asset. A simple rule benchmark tests whether added complexity improves on a transparent alternative.

The five-session artificial signal below is itself a transparent rule, not an AI result. The same accounting can accept an AI-generated signal, but a Report 1 comparison must identify the actual candidate and keep its data, dates, costs, and execution assumptions aligned with the benchmarks.

```python
cost_data["buy_hold_net_return"] = (
    cost_data["buy_hold_gross_return"]
    - one_way_cost * cost_data["buy_hold_turnover"]
)
cost_data["slow_mom"] = cost_data["Adj_Close"] / cost_data["Adj_Close"].shift(20) - 1
cost_data["slow_signal"] = np.where(
    cost_data["slow_mom"].isna(), np.nan,
    np.where(cost_data["slow_mom"] > 0, 1.0, 0.0),
)
cost_data["slow_position"] = cost_data["slow_signal"].shift(1).fillna(0.0)
cost_data["slow_turnover"] = cost_data["slow_position"].diff().abs().fillna(
    cost_data["slow_position"].abs()
)
cost_data["slow_net_return"] = (
    cost_data["slow_position"] * cost_data["asset_return"].fillna(0.0)
    - one_way_cost * cost_data["slow_turnover"]
)

benchmark_table = pd.Series(
    {
        "cash": 1.0,
        "buy_and_hold_net": (1 + cost_data["buy_hold_net_return"]).prod(),
        "five_session_momentum_net": (1 + cost_data["net_return"]).prod(),
        "twenty_session_momentum_net": (1 + cost_data["slow_net_return"]).prod(),
    },
    name="final_wealth",
)
print(benchmark_table.round(6))
assert np.isfinite(benchmark_table.to_numpy()).all()
```

Expected evidence: final wealth is 1.000000 for cash, approximately 0.846184 for net buy-and-hold, 0.843004 for net five-session momentum, and 0.893627 for net twenty-session momentum. All four values use the same artificial dates. Buy-and-hold and both timing rules are charged the same stated one-way cost per unit of turnover; their turnover differs because their decisions differ. No terminal liquidation is imposed. This table does not control statistical uncertainty, which is addressed in Week 7. If a value is missing or infinite, inspect the common return rows, initial entry treatment, and each method's turnover before comparing results.

### Worked example — equal-weighted benchmark under common conditions

An equal-weighted benchmark is meaningful only for a multi-asset candidate using the same asset universe and period. Consider one artificial return interval with assets A and B. The candidate begins the interval with weights $(0.75,0.25)$, the benchmark with $(0.50,0.50)$, and both move from cash so that initial one-way turnover is 1. Asset returns are 2.0% and -1.0%, and the common one-way cost is 4 bp.

The candidate gross return is $0.75(0.02)+0.25(-0.01)=0.0125$; its net return is $0.0125-0.0004=0.0121$, or 1.21%. The equal-weighted benchmark gross return is $0.50(0.02)+0.50(-0.01)=0.0050$; its net return is $0.0050-0.0004=0.0046$, or 0.46%. This one interval demonstrates common-condition accounting only. It cannot show that the candidate weighting is generally better, and it does not address weight drift after the return. Week 8 develops multi-period weighting and rebalancing.

### Activity 4 — Recalculate a common-condition comparison

Use candidate weights $(0.20,0.80)$, equal weights $(0.50,0.50)$, asset returns -1.0% and 1.5%, and a common one-way entry cost of 6 bp. Before calculating, predict which net return is higher. Verify that both weight vectors are long-only and sum to one, calculate both gross and net returns, revise the prediction, and state why using a different universe or cost rate would invalidate the comparison.

Expected evidence contains two feasible weight vectors, two gross returns, two cost deductions using the same rate and turnover, and two net returns in the same units. If the difference is driven by unequal costs, first restore the common 6 bp rate before interpreting weights. Save the prediction, calculation, revision, and limitation.

### Activity 5 — Record missing real-market inputs

List the source still needed for commission, quoted spread, expected slippage, execution price, and market impact for a real Report 1 strategy. Mark each value as measured, quoted, assumed, or pending. Expected evidence does not replace a missing value with the Week 6 artificial 4 bp convention. If all items are marked measured without a source and retrieval date, first change their status to pending.

## 7. Required evidence and completion

The Week 6 cost record, sensitivity results, delay comparison, and benchmark conditions provide implementation evidence for the [Report 1 criteria](../10/week10_main.md). This lesson supplies evidence for those criteria; it does not change the report weights or add another criterion.

Submit the cost convention, hand calculation, gross and net ledgers, passed identities, cost grid, delay comparison, benchmark table, equal-weighted comparison, assumption audit, and [Week 6 cost record](week6_support.md#cost-and-execution-record).

Your work is complete when another student can reproduce each charge, identify every benchmark question, and state the cost and execution assumptions on which the conclusion depends.

## Exit evidence

```text
My one-way cost is ______ bp and includes ______.
My total one-way turnover is ______.
The comparison changes when ______.
Execution delay changes ______.
This evidence still does not address ______.
```
