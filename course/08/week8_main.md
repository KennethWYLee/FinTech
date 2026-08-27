# Week 8 — Basic Portfolio-Weighting Methods

## Core financial question

How do equal, market-capitalization, signal-based, and inverse-volatility rules convert information available before a return interval into feasible portfolio weights, and how can the four rules be compared under the same rebalancing, cost, and evaluation conditions?

Week 7 established that performance measures answer different questions and that repeated selection can overstate evidence. This week applies those limits to portfolio weights. The resulting calculations provide the initial weighting approach required in the Week 10 report and the benchmarks for the optimization methods introduced in Week 11.

## Observable outcome

By the end of this 150-minute class, you will calculate four weight vectors for the same artificial assets, distinguish target weights from weights that drift after returns, and evaluate rules fixed before a separate chronological period. You will preserve constraint checks, timing, turnover, costs, concentration, and limited conclusions rather than select a universal winner.

## Teaching objectives

After completing this class, you should be able to:

- distinguish a trading signal, a target portfolio weight, and an actual holding weight;
- normalize raw inputs under full-investment, long-only, no-leverage, and position-cap constraints;
- calculate equal, market-capitalization, signal-based, and inverse-volatility weights and explain their required inputs;
- apply target weights with a one-period information lag, monthly rebalancing, weight drift, and a common transaction-cost rule; and
- compare concentration, turnover, and net results on identical evaluation dates while limiting conclusions to the artificial experiment.

## Before you begin

Bring the Week 6 transaction-cost convention and the Week 7 limits on empirical conclusions. Use a clean notebook with `numpy` and `pandas`. Every asset, price, return, share count, and result in this class is artificial. The 20-business-day lookback, 60% cap, monthly schedule, and 4-basis-point cost are teaching assumptions, not recommended settings.

## 150-minute learning sequence

| Time | Focus | Required evidence |
|---:|---|---|
| 0–10 minutes | From trading decisions to capital allocation | Signal, target-weight, and holding-weight distinction |
| 10–25 minutes | Budget, long-only, leverage, and position caps | Feasibility statement |
| 25–45 minutes | Worked calculation of four weighting rules | Four hand-calculated vectors and cap check |
| 45–60 minutes | Independent normalization exercise | Prediction, calculation, and corrected explanation |
| 60–75 minutes | Artificial data and chronological boundary | Data audit and fixed-rule statement |
| 75–100 minutes | Weight functions, fallbacks, and failures | Common-date weight table and passed tests |
| 100–120 minutes | Target weights, drift, rebalancing, and costs | Hand calculation and audited backtest |
| 120–135 minutes | Separate chronological evaluation | Four comparable result rows |
| 135–145 minutes | Concentration, turnover, and limitations | Evidence-based comparison |
| 145–150 minutes | Exit evidence | Conditional conclusion and next question |

## 1. A signal is not a portfolio weight

A signal expresses direction or relative preference. It may be negative, unbounded, or measured in units that cannot be invested. A target weight states the desired fraction of current portfolio value allocated to an asset at a decision time. After asset returns occur, the actual holding weights normally drift away from those targets. Rebalancing trades from the drifted holdings to new targets.

For $N$ assets, today's fully invested, long-only, no-leverage portfolio must satisfy

$$
\sum_{i=1}^{N}w_{i,t}=1,
\qquad 0\leq w_{i,t}\leq w_{\max}.
$$

The gross exposure is therefore $\sum_i|w_{i,t}|=1$. A common cap is feasible only if $Nw_{\max}\geq1$. For four assets, a 20% cap is infeasible because the largest possible sum is only 80%.

Before a cap is imposed, the four calculations are

$$
w_i^{EW}=\frac{1}{N},
\qquad
w_i^{MC}=\frac{MC_i}{\sum_j MC_j},
$$

$$
w_i^{SIG}=\frac{\max(s_i,0)}{\sum_j\max(s_j,0)},
\qquad
w_i^{IV}=\frac{1/\widehat\sigma_i}{\sum_j1/\widehat\sigma_j}.
$$

Here, $MC_i$ is the artificial price multiplied by the artificial share count, $s_i$ is an available signal, and $\widehat\sigma_i$ is a positive volatility estimate. Real equity indices often use float-adjusted market capitalization; today's artificial share counts do not reproduce an index provider's float adjustment. Equal and market-capitalization weights do not use a return forecast. Inverse-volatility weights use volatility estimates but ignore expected returns and correlations. They are not generally equal-risk-contribution weights, which are studied in Week 12.

The signal-based denominator is zero when all signals are nonpositive. This class then uses equal weights and records that the fallback occurred. Missing or infinite inputs, nonpositive volatility, negative market capitalization, and an infeasible cap cause an explicit failure.

### Worked example — raw inputs, normalization, and a cap

Use artificial market capitalizations `(40, 30, 20, 10)`, signals `(0.4, -0.1, 0.2, 0.0)`, volatilities `(0.10, 0.20, 0.15, 0.25)`, and a 60% cap.

Equal weights are `(0.25, 0.25, 0.25, 0.25)`. Market-capitalization weights are `(0.40, 0.30, 0.20, 0.10)`. Neither vector violates the cap.

Clipping negative signals gives `(0.4, 0, 0.2, 0)`. Dividing by `0.6` gives uncapped weights `(0.6667, 0, 0.3333, 0)`. The first weight exceeds 60% by `0.0667`, so that excess is assigned in proportion to the other positive weight. The capped signal-based weights are therefore `(0.60, 0, 0.40, 0)`.

The inverse volatilities are `(10, 5, 6.6667, 4)` and sum to `25.6667`. Their normalized weights are approximately `(0.3896, 0.1948, 0.2597, 0.1558)`, so the cap does not bind. On these inputs, the signal-based rule is the most concentrated by the squared-weight measure introduced later. That statement concerns this calculation only.

### Activity 1 — calculate before running code

Use market capitalizations `(25, 25, 30, 20)`, signals `(0.3, 0.0, -0.2, 0.1)`, volatilities `(0.16, 0.12, 0.20, 0.24)`, and a 50% cap.

Before calculating, predict which rule will have the largest weight and whether the cap will bind. Then calculate all four vectors, show the normalization denominator, apply the cap, and check finiteness, nonnegativity, sum-to-one, and the 50% maximum. Preserve your prediction and revised explanation. If a result fails, first check whether negative signals were clipped before normalization and whether the denominator is positive.

## 2. Build artificial data and fix the evaluation rules

The first 260 business-day rows form a development period. The four formulas, 20-day lookback, 60% cap, monthly rebalancing, and 4-basis-point cost are fixed before the later 260 rows are evaluated. Within this artificial experiment, the later rows provide out-of-sample evidence only for rules fixed before that boundary. Rolling inputs during the later period may use information through the preceding business day, but no later-period result may be used to change the fixed rules.

```python
import numpy as np
import pandas as pd

rng = np.random.default_rng(2030)
dates = pd.bdate_range("2022-01-03", periods=520)
assets = ["A", "B", "C", "D"]

annual_vol = np.array([0.12, 0.18, 0.15, 0.22])
correlation = np.array(
    [
        [1.00, 0.45, 0.25, 0.15],
        [0.45, 1.00, 0.35, 0.20],
        [0.25, 0.35, 1.00, 0.30],
        [0.15, 0.20, 0.30, 1.00],
    ]
)
daily_cov = np.outer(annual_vol, annual_vol) * correlation / 252
daily_mean = np.array([0.05, 0.06, 0.055, 0.065]) / 252
simulated_returns = rng.multivariate_normal(daily_mean, daily_cov, len(dates))

asset_returns = pd.DataFrame(simulated_returns, index=dates, columns=assets)
asset_prices = 100 * (1 + asset_returns).cumprod()
shares = pd.Series([4.0, 3.0, 2.0, 1.0], index=assets)
market_cap = asset_prices.mul(shares, axis=1)
momentum_20 = asset_prices / asset_prices.shift(20) - 1
volatility_20 = asset_returns.rolling(20).std(ddof=1) * np.sqrt(252)

development_end = dates[259]
evaluation_start = dates[260]
evaluation_end = dates[-1]

print("shape:", asset_returns.shape)
print("development through:", development_end.date())
print("evaluation:", evaluation_start.date(), "through", evaluation_end.date())

assert asset_returns.shape == (520, 4)
assert asset_returns.index.is_monotonic_increasing
assert not asset_returns.index.has_duplicates
assert np.linalg.eigvalsh(daily_cov).min() > 0
assert asset_returns.gt(-1).all().all()
assert asset_prices.gt(0).all().all()
```

Expected evidence: shape `(520, 4)`, a development end of `2022-12-30`, and an evaluation interval from `2023-01-02` through `2023-12-29`. If dates differ, first inspect the start date, number of rows, and use of `pd.bdate_range`; this artificial calendar does not remove exchange holidays.

### Activity 2 — audit the boundary

Write the last date whose return can enter the 20-day volatility estimate used for the first evaluation-day allocation. State whether the return on `2023-01-02` is available at that allocation decision. Preserve the answer before and after inspecting the code in Section 4. If the answer changes, identify the exact index or lag that changed it.

## 3. Calculate constrained target weights

The function below accepts only a nonempty vector of finite, nonnegative raw values. It returns both the capped weight vector and a flag showing whether equal weights replaced an all-zero vector. Excess above the cap is redistributed among entries still below the cap. The function stops on invalid inputs instead of silently changing them.

```python
def normalize_with_cap(raw, cap=0.60, fallback_to_equal=False):
    raw = pd.Series(raw, dtype=float).copy()
    if raw.empty:
        raise ValueError("At least one raw input is required")
    if not np.isfinite(raw).all():
        raise ValueError("Raw inputs must be finite")
    if raw.lt(0).any():
        raise ValueError("Raw inputs must be nonnegative before normalization")
    if not np.isfinite(cap) or not 0 < cap <= 1:
        raise ValueError("Cap must be finite and in (0, 1]")
    if cap * len(raw) < 1 - 1e-12:
        raise ValueError("Cap is infeasible for a fully invested long-only portfolio")

    used_fallback = False
    if raw.sum() <= 0:
        if not fallback_to_equal:
            raise ValueError("Raw inputs sum to zero and no fallback was allowed")
        raw[:] = 1.0
        used_fallback = True

    weight = pd.Series(0.0, index=raw.index)
    active = pd.Series(True, index=raw.index)
    remaining_budget = 1.0

    for _ in range(len(raw) + 1):
        active_raw = raw[active]
        if active_raw.empty:
            break
        if active_raw.sum() > 0:
            proposed = remaining_budget * active_raw / active_raw.sum()
        else:
            proposed = pd.Series(
                remaining_budget / len(active_raw), index=active_raw.index
            )

        over = proposed > cap + 1e-12
        if not over.any():
            weight.loc[proposed.index] = proposed
            remaining_budget = 0.0
            break

        capped_index = proposed.index[over]
        weight.loc[capped_index] = cap
        active.loc[capped_index] = False
        remaining_budget -= cap * len(capped_index)

    if abs(remaining_budget) > 1e-10 or not np.isclose(weight.sum(), 1.0):
        raise RuntimeError("Cap redistribution did not produce a full allocation")
    if weight.gt(cap + 1e-10).any():
        raise RuntimeError("Cap redistribution exceeded the position limit")
    return weight, used_fallback


def weights_at(input_date, cap=0.60):
    equal, _ = normalize_with_cap(pd.Series(1.0, index=assets), cap)

    cap_input = market_cap.loc[input_date]
    if cap_input.le(0).any():
        raise ValueError("Market capitalizations must be positive")
    cap_weight, _ = normalize_with_cap(cap_input, cap)

    positive_signal = momentum_20.loc[input_date].clip(lower=0)
    signal_weight, signal_fallback = normalize_with_cap(
        positive_signal, cap, fallback_to_equal=True
    )

    vol_input = volatility_20.loc[input_date]
    if vol_input.le(0).any():
        raise ValueError("Volatility estimates must be positive")
    inverse_volatility, _ = normalize_with_cap(1 / vol_input, cap)

    table = pd.DataFrame(
        {
            "equal": equal,
            "market_cap": cap_weight,
            "positive_signal": signal_weight,
            "inverse_volatility": inverse_volatility,
        }
    )
    table.attrs["positive_signal_fallback"] = signal_fallback
    table.attrs["input_date"] = input_date
    return table


example_weights = weights_at(development_end)
print(example_weights.round(4))
print("positive-signal fallback:", example_weights.attrs["positive_signal_fallback"])

assert np.isfinite(example_weights.to_numpy()).all()
assert np.allclose(example_weights.sum(axis=0), 1.0)
assert example_weights.ge(0).all().all()
assert example_weights.le(0.60 + 1e-10).all().all()

zero_weight, zero_fallback = normalize_with_cap(
    [0, 0, 0, 0], cap=0.60, fallback_to_equal=True
)
print("all-zero fallback:", zero_weight.to_list(), zero_fallback)
assert zero_fallback and np.allclose(zero_weight, 0.25)

for invalid_raw, invalid_cap in [([1, 1, 1, 1], 0.20), ([1, np.nan, 1, 1], 0.60)]:
    try:
        normalize_with_cap(invalid_raw, invalid_cap)
    except ValueError as error:
        print("expected failure:", error)
    else:
        raise AssertionError("Invalid inputs should have failed")
```

Expected evidence: four finite columns that sum to one and respect the 60% cap. In the printed method order, the development-date columns are approximately `equal = (0.25, 0.25, 0.25, 0.25)`, `market_cap = (0.3765, 0.2952, 0.2278, 0.1005)`, `positive_signal = (0.1790, 0.1691, 0.4341, 0.2179)`, and `inverse_volatility = (0.2881, 0.1866, 0.2982, 0.2270)`. The development-date fallback flag is `False`; the all-zero test prints four 0.25 weights and `True`; and the invalid cases print one infeasible-cap message and one nonfinite-input message. If the table contains a missing value, first inspect the 20-day lookback at `development_end`. If a cap check fails, inspect feasibility and the redistribution loop before calculating any return.

## 4. Distinguish target weights from drifted holdings

Suppose the target weights before one interval are `(0.50, 0.30, 0.20)` and the asset returns are `(10%, -5%, 0%)`. The portfolio return before cost is

$$
r_p=0.50(0.10)+0.30(-0.05)+0.20(0)=0.035.
$$

Without cash flows or trading during the interval, the end-of-interval holding weight is

$$
w_{i,t}^{hold}=\frac{w_{i,t}^{target}(1+r_{i,t})}{1+r_{p,t}}.
$$

The drifted holdings are approximately `(0.5314, 0.2754, 0.1932)`. Trading back to the same target at the next decision requires one-way turnover

$$
\frac12\sum_i\left|w_{i,t+1}^{target}-w_{i,t}^{hold}\right|\approx0.0314.
$$

This hand calculation shows why turnover must compare a new target with the holdings immediately before the trade, not with the previous target.

The following backtest calculates every target from information through the previous business date. The first observation of each new month is a rebalance date. Between rebalances, holdings drift with returns. Initial movement from cash is recorded as turnover of 1; later one-way turnover is half the sum of absolute asset-weight changes. Cost equals turnover multiplied by `4 / 10_000`. For simplicity, the cost is deducted from portfolio return but is not assigned to a particular asset when end-of-day holding proportions are updated.

```python
def backtest_method(method, cost_bp=4.0, cap=0.60):
    portfolio_return = pd.Series(np.nan, index=asset_returns.index)
    turnover = pd.Series(0.0, index=asset_returns.index)
    cost = pd.Series(0.0, index=asset_returns.index)
    fallback_used = pd.Series(False, index=asset_returns.index)
    target_concentration = pd.Series(np.nan, index=asset_returns.index)
    held = pd.Series(0.0, index=assets)
    first_investment = True

    for i in range(21, len(asset_returns)):
        date = asset_returns.index[i]
        previous_date = asset_returns.index[i - 1]
        new_month = date.month != previous_date.month

        if new_month or held.sum() == 0:
            proposed_table = weights_at(previous_date, cap)
            proposed = proposed_table[method]
            fallback_used.loc[date] = (
                method == "positive_signal"
                and proposed_table.attrs["positive_signal_fallback"]
            )
            turnover.loc[date] = (
                1.0 if first_investment else 0.5 * (proposed - held).abs().sum()
            )
            held = proposed.copy()
            target_concentration.loc[date] = (proposed**2).sum()
            first_investment = False

        day_return = asset_returns.loc[date]
        portfolio_return.loc[date] = held @ day_return
        cost.loc[date] = turnover.loc[date] * cost_bp / 10_000
        if 1 + portfolio_return.loc[date] <= 0:
            raise ValueError("Gross portfolio wealth became nonpositive")
        if portfolio_return.loc[date] - cost.loc[date] <= -1:
            raise ValueError("Net portfolio wealth became nonpositive")
        held = held * (1 + day_return) / (1 + portfolio_return.loc[date])

    result = pd.DataFrame(
        {
            "gross_return": portfolio_return,
            "turnover": turnover,
            "cost": cost,
            "net_return": portfolio_return - cost,
            "fallback_used": fallback_used,
            "target_concentration": target_concentration,
        }
    )
    return result.loc[result["net_return"].notna()]


methods = ["equal", "market_cap", "positive_signal", "inverse_volatility"]
full_backtests = {method: backtest_method(method) for method in methods}
evaluation = {
    method: result.loc[evaluation_start:evaluation_end].copy()
    for method, result in full_backtests.items()
}

common_index = evaluation["equal"].index
assert len(common_index) == 260
assert all(result.index.equals(common_index) for result in evaluation.values())
assert all(np.allclose(result["cost"], result["turnover"] * 4 / 10_000)
           for result in evaluation.values())

comparison = []
for method, result in evaluation.items():
    comparison.append(
        {
            "method": method,
            "observations": len(result),
            "final_net_wealth": (1 + result["net_return"]).prod(),
            "annualized_net_volatility": result["net_return"].std(ddof=1) * np.sqrt(252),
            "total_one_way_turnover": result["turnover"].sum(),
            "mean_target_concentration": result["target_concentration"].dropna().mean(),
            "fallback_count": int(result["fallback_used"].sum()),
        }
    )

comparison = pd.DataFrame(comparison).set_index("method")
print(comparison.round(4).to_string())
assert np.isfinite(comparison.to_numpy()).all()
```

Expected evidence: four rows with 260 identical evaluation dates. In the verified environment, final net wealth is `1.5030`, `1.4505`, `1.4366`, and `1.4686` in the printed method order; annualized net volatility is `0.1131`, `0.1105`, `0.1297`, and `0.1081`; total one-way turnover is `0.1412`, `0.0000`, `5.4451`, and `0.7450`; and every evaluation-period fallback count is zero. The exact last digits may vary with an approved package change. Market-capitalization turnover is zero here because fixed shares and a nonbinding cap cause the target market-capitalization weights to move with the drifted holdings. Signal-based turnover is the largest on this artificial path, but the separately tested all-zero case—not this evaluation period—demonstrates its fallback. If these relationships fail, first print the common index, rebalance dates, previous-date inputs, and each method's separately calculated turnover. Do not alter a rule after seeing this evaluation output and continue to call the same period unused.

### Activity 3 — trace one evaluation-day decision

For `2023-01-02`, preserve the input date, four target vectors, fallback flag, pre-trade holdings, turnover, cost, asset returns, gross portfolio return, and net portfolio return. Before tracing the code, predict which date supplies the weights. In a temporary copy of `backtest_method`, save `held.copy()` immediately before the rebalance calculation and print the requested values only when `date == evaluation_start`; do not change the submitted function. Confirm that no `2023-01-02` return enters its own weight. If your trace disagrees, first inspect `previous_date` and the placement of the holdings update.

## 5. Compare concentration without declaring a universal winner

For a target weight vector, the squared-weight concentration measure is

$$
H_t=\sum_iw_{i,t}^2.
$$

It equals $1/N=0.25$ for four equal weights. Under long-only full investment and a 60% cap, its maximum is $0.60^2+0.40^2=0.52$. The code records this measure only when a new target is set; holding-weight concentration may differ after returns.

Use the comparison table to write five sentences: one about inputs, one about target concentration, one about turnover, one about net wealth and volatility, and one about the limits of the evidence. A valid sentence names the criterion and the artificial evaluation period. It does not claim that the method with the largest final wealth is generally best.

### Activity 4 — change one declared assumption

Before rerunning, predict how replacing the 60% cap with 40% will affect the signal-based rule's target concentration and turnover. Run all four methods with `cap=0.40` while keeping the seed, dates, lookback, rebalance schedule, and cost unchanged. Check feasibility, compare results with the original table, and revise your prediction. Preserve both tables and identify the first diagnostic check if any method produces missing or unequal dates.

## 6. Required evidence and completion

Submit the worked-example weights, Activity 1 calculation, artificial-data audit, fixed-rule statement, constrained development-date weight table, fallback and failure outputs, drift hand calculation, common evaluation table, traced evaluation-day decision, cap comparison, five-sentence interpretation, and completed [Week 8 weighting-method record](week8_support.md#weighting-method-record).

This evidence supports the [Report 1 criteria](../10/week10_main.md), including the initial explanation of how a trading signal becomes a portfolio weight. It also supplies the common basic benchmarks needed in Week 11. This lesson does not change the report weights or add another criterion.

Your work is complete when every method has a formula, input, availability time, fallback or failure rule, constraint, rebalance rule, lag, turnover convention, transaction cost, common evaluation period, and appropriately limited interpretation.

## Exit evidence

```text
A signal differs from a target weight because ______.
A target weight differs from a holding weight after returns because ______.
My budget and cap checks show ______.
Under the stated artificial evaluation, the clearest difference among methods is ______.
That difference does not establish a universal ranking because ______.
The first question I will carry into mean–variance weighting is ______.
```
