# Week 7 — Performance Evaluation and Backtest Auditing

## Observable outcome

By the end of this 150-minute class, you will verify performance measures by hand, produce a performance table, drawdown record, block-bootstrap interval, and backtest audit for an artificial net-return series, and demonstrate why selecting the best of many trials can create an optimistic result.

## Teaching Objectives

After completing this class, you should be able to:

- calculate annualized return, volatility, Sharpe ratio, Sortino ratio, maximum drawdown, and drawdown duration;
- interpret return, risk, path, and trading measures as different evidence;
- use a block bootstrap to show sampling uncertainty without claiming independence;
- explain repeated-selection and data-snooping risk; and
- audit timing, costs, benchmarks, periods, and claims before accepting a backtest.

## Before you begin

Bring the Week 6 net-return convention. This class assumes 252 artificial observations per year and a zero target or cash return for its full-length example. Those assumptions must appear beside the metrics. Week 8 will use the same cost and evidence limits when comparing portfolio weights.

## 150-minute learning sequence

| Time | Focus | Evidence produced |
|---:|---|---|
| 0–10 minutes | Which metric answers which question? | Metric classification |
| 10–35 minutes | Worked return, variability, ratio, and drawdown calculations | Verified four-return example |
| 35–50 minutes | Independent four-return calculation | Prediction, calculation, and correction |
| 50–70 minutes | Build net strategy returns | Audited series |
| 70–90 minutes | Performance function | Common metric table |
| 90–110 minutes | Block-bootstrap uncertainty | Interval and assumptions |
| 110–125 minutes | Multiple trials | Selection-bias demonstration |
| 125–140 minutes | Backtest audit | Pass/fail/unclear table |
| 140–145 minutes | Evidence synthesis | Report 1 evidence statement |
| 145–150 minutes | Exit evidence | Defensible conclusion |

## 1. Metrics answer different questions

For periodic returns \(r_t\), \(N\) observations per year, and zero target return:

\[
\widehat\mu_{ann}=\left(\prod_t(1+r_t)\right)^{N/T}-1,
\]

\[
\widehat\sigma_{ann}=\operatorname{sd}(r_t)\sqrt{N},
\qquad
\widehat{SR}=\frac{\overline r}{\operatorname{sd}(r)}\sqrt{N}.
\]

For the Sortino ratio, define downside deviations relative to target \(q=0\):

\[
d_t=\min(r_t-q,0), \qquad
\widehat{Sortino}=\frac{\overline r-q}{\sqrt{\frac{1}{T}\sum_t d_t^2}}\sqrt{N}.
\]

Define initial wealth as \(W_0=1\), subsequent wealth as \(W_t=\prod_{j\leq t}(1+r_j)\), the running peak as \(M_t=\max(W_0,W_1,\ldots,W_t)\), and drawdown

\[
D_t=\frac{W_t}{M_t}-1.
\]

Maximum drawdown is \(\min_t D_t\). Drawdown duration counts consecutive observations below the prior peak. It is not measured in money or percentage.

### Worked example — four artificial returns

Use returns \((10\%,-10\%,-5\%,8\%)\), initial wealth 1, target 0, and \(N=4\) observations per year so the four observations form one artificial annualization period. Wealth and drawdown are:

| Observation | Return | Wealth | Running peak including initial wealth | Drawdown |
|---:|---:|---:|---:|---:|
| 1 | 10.0% | 1.100000 | 1.100000 | 0.00% |
| 2 | -10.0% | 0.990000 | 1.100000 | -10.00% |
| 3 | -5.0% | 0.940500 | 1.100000 | -14.50% |
| 4 | 8.0% | 1.015740 | 1.100000 | -7.66% |

Because \(N/T=4/4=1\), annualized return is \(1.015740-1=1.574\%\). The arithmetic mean return is 0.75% per observation, sample standard deviation is approximately 9.777%, annualized volatility is approximately 19.553%, and the zero-target Sharpe ratio is approximately 0.1534. The downside root mean square over all four observations is approximately 5.590%; annualizing it gives 11.180%, so the zero-target Sortino ratio is approximately 0.2683. Maximum drawdown is -14.50%, and the final three observations remain below the prior peak, so maximum drawdown duration is three observations. If duration is reported as 14.50%, first separate the depth of the loss from the number of observations spent below a peak.

### Activity 1 — Match evidence to questions

Match annualized return, volatility, Sharpe, Sortino, maximum drawdown, drawdown duration, and turnover to these questions: growth, overall dispersion, return per total variability, return per downside variability, deepest loss from a prior peak, time spent below a peak, and trading intensity.

Expected evidence uses each measure once and preserves the distinction between drawdown depth and duration. If Sharpe and Sortino are matched to the same question without qualification, first identify whether their denominators use total or downside variability.

### Activity 2 — Independently verify another path

Use returns \((4\%,-2\%,-3\%,5\%)\), initial wealth 1, target 0, and \(N=4\). Before calculating, predict whether final wealth is above 1 and which observation has the deepest drawdown. Then calculate every wealth, running peak, drawdown, annualized return, annualized volatility, Sharpe ratio, Sortino ratio, maximum drawdown, and maximum duration. Run the `performance_metrics` function later on this four-return series and revise the prediction or calculation from the output.

Expected evidence includes four wealth factors, a running peak that never falls below 1, nonpositive drawdowns, and agreement between the hand calculations and the function within a stated rounding tolerance. If maximum drawdown misses the first loss after a peak, first prepend the initial wealth of 1 conceptually before taking the running maximum. Save the prediction, full calculation, function output, correction, and units.

## 2. Build an artificial net-return series

```python
import numpy as np
import pandas as pd

rng = np.random.default_rng(2029)
dates = pd.bdate_range("2022-01-03", periods=520)
asset_return = pd.Series(
    rng.normal(0.0002, 0.012, len(dates)), index=dates, name="asset_return"
)
price = 100 * (1 + asset_return).cumprod()
momentum = price / price.shift(20) - 1
signal = pd.Series(
    np.where(momentum.isna(), np.nan, np.where(momentum > 0, 1.0, 0.0)),
    index=dates,
)
position = signal.shift(1).fillna(0.0)
turnover = position.diff().abs().fillna(position.abs())
one_way_cost = 4 / 10_000

strategy_return = position * asset_return - one_way_cost * turnover
buy_hold_turnover = pd.Series(0.0, index=dates)
buy_hold_turnover.iloc[0] = 1.0
buy_hold_return = asset_return - one_way_cost * buy_hold_turnover

returns = pd.DataFrame(
    {"strategy_net": strategy_return, "buy_and_hold_net": buy_hold_return}
)
assert returns.gt(-1).all().all()
print(returns.head())
```

Each generated `asset_return` is the artificial interval return ending on its row date; the boundary before the first row is not displayed. The strategy and buy-and-hold series are both net of the same four-basis-point one-way rate. Buy-and-hold is charged one initial investment from cash and no terminal liquidation; the timing strategy is charged on each position change. If the first buy-and-hold cost is absent, inspect `buy_hold_turnover` before calculating metrics.

## 3. Calculate common metrics

```python
def performance_metrics(r, periods_per_year=252, target=0.0):
    r = pd.Series(r).dropna()
    if len(r) < 2 or not np.isfinite(r).all():
        raise ValueError("at least two finite return observations are required")
    if (r <= -1).any():
        raise ValueError("a return at or below -100% makes compounded wealth nonpositive")
    wealth = (1 + r).cumprod()
    annual_return = wealth.iloc[-1] ** (periods_per_year / len(r)) - 1
    annual_volatility = r.std(ddof=1) * np.sqrt(periods_per_year)
    sharpe = np.nan
    if r.std(ddof=1) > 0:
        sharpe = (r.mean() - target) / r.std(ddof=1) * np.sqrt(periods_per_year)

    downside = np.minimum(r - target, 0.0)
    downside_deviation = np.sqrt(np.mean(downside**2)) * np.sqrt(periods_per_year)
    sortino = np.nan
    if downside_deviation > 0:
        sortino = (r.mean() - target) * periods_per_year / downside_deviation

    running_peak = wealth.cummax().clip(lower=1.0)
    drawdown = wealth / running_peak - 1
    underwater = drawdown < 0
    episode = (~underwater).cumsum()
    max_duration = int(underwater.groupby(episode).sum().max())

    return pd.Series(
        {
            "annual_return": annual_return,
            "annual_volatility": annual_volatility,
            "sharpe_zero_target": sharpe,
            "sortino_zero_target": sortino,
            "maximum_drawdown": drawdown.min(),
            "maximum_drawdown_duration_observations": max_duration,
            "final_wealth": wealth.iloc[-1],
        }
    )

metric_table = returns.apply(performance_metrics).T
metric_table["one_way_turnover"] = [turnover.sum(), 1.0]
print(metric_table.round(4))

assert metric_table["maximum_drawdown"].le(0).all()
assert metric_table["annual_volatility"].ge(0).all()
assert np.isfinite(metric_table.to_numpy()).all()
```

Expected evidence: strategy net final wealth is approximately 1.153661, annualized return 0.071727, annualized volatility 0.146087, Sharpe 0.547013, Sortino 0.824471, maximum drawdown -0.159642, maximum duration 416 observations, and one-way turnover 48. Buy-and-hold net final wealth is approximately 1.463705, with one-way turnover 1. Both methods have finite metrics, nonnegative volatility, and nonpositive maximum drawdown. If a ratio is nonfinite, inspect its denominator and report it as undefined rather than forcing a numerical value.

### Activity 3 — Write a four-part interpretation

For each method, write one sentence about growth, variability, drawdown, and trading. Do not call a method superior unless you state the decision criterion.

Expected evidence distinguishes the higher final wealth from the shallower drawdown and lower turnover rather than compressing them into one label. If the conclusion says only “better,” first name the metric and whether costs are included.

## 4. Show sampling uncertainty

Daily returns are not guaranteed independent. The following circular block bootstrap resamples five-observation blocks so some short local dependence is preserved. The block length is an assumption, not a universally correct value.

```python
def circular_block_bootstrap_sharpe(r, block=5, repetitions=500, seed=77):
    x = pd.Series(r).dropna().to_numpy()
    n = len(x)
    if n < 2 or block < 1 or repetitions < 1:
        raise ValueError("returns, block length, and repetitions are insufficient")
    generator = np.random.default_rng(seed)
    output = []

    for _ in range(repetitions):
        pieces = []
        while sum(len(piece) for piece in pieces) < n:
            start = generator.integers(0, n)
            index = (start + np.arange(block)) % n
            pieces.append(x[index])
        sample = np.concatenate(pieces)[:n]
        sample_std = sample.std(ddof=1)
        output.append(
            np.nan if sample_std == 0
            else sample.mean() / sample_std * np.sqrt(252)
        )

    return np.asarray(output)

boot = circular_block_bootstrap_sharpe(returns["strategy_net"])
interval = np.quantile(boot, [0.025, 0.5, 0.975])
print(pd.Series(interval, index=["2.5%", "median", "97.5%"]).round(4))
assert np.isfinite(boot).all()
```

All 500 bootstrap values should be finite for the artificial series. With block length 5 and seed 77, the 2.5%, median, and 97.5% sample quantiles are approximately -0.9458, 0.5219, and 1.8139. If a value is nonfinite, inspect the resampled standard deviations and the input series before calculating quantiles.

### Activity 4 — Change one assumption

Repeat with block lengths 1 and 20. Report how the interval changes and explain why the comparison is a sensitivity analysis, not proof that one interval has correct coverage for real markets.

Expected evidence keeps 500 repetitions and seed 77 fixed while changing only block length. The endpoints need not move monotonically with block length. If multiple settings change together, first restore the common return series, repetitions, and seed.

## 5. Demonstrate repeated selection

The next cell creates a separate zero-mean artificial return series and 30 random timing rules with no designed predictive relation to it. It selects the highest first-half net Sharpe and then reports that same rule in the second half.

```python
demo_asset_return = pd.Series(
    np.random.default_rng(6).normal(0.0, 0.012, len(dates)), index=dates
)
trial_rng = np.random.default_rng(88)
trial_positions = pd.DataFrame(
    trial_rng.integers(0, 2, size=(len(asset_return), 30)),
    index=dates,
    columns=[f"trial_{i:02d}" for i in range(30)],
).shift(1).fillna(0.0)

trial_turnover = trial_positions.diff().abs().fillna(trial_positions.abs())
trial_returns = (
    trial_positions.mul(demo_asset_return, axis=0)
    - one_way_cost * trial_turnover
)
split = len(trial_returns) // 2

def annualized_sharpe(frame):
    return frame.mean() / frame.std(ddof=1) * np.sqrt(252)

selection_sharpe = annualized_sharpe(trial_returns.iloc[:split])
selected_name = selection_sharpe.idxmax()
test_sharpe = annualized_sharpe(trial_returns.iloc[split:])[selected_name]

print("Selected trial:", selected_name)
print("Selection-period Sharpe:", round(selection_sharpe[selected_name], 4))
print("Later-period Sharpe:", round(test_sharpe, 4))
print("Median selection-period Sharpe:", round(selection_sharpe.median(), 4))
```

Expected evidence: `trial_28` is selected in the current environment; its first-half Sharpe is approximately 1.8709, the median first-half Sharpe is approximately 0.1428, and its later-period Sharpe is approximately 0.2490. All trials use the same cost rate, but their turnover can differ. This one deliberately reproducible realization illustrates selection optimism; it cannot estimate the probability of overfitting or show that every selected model must deteriorate. If the selected name changes in another approved package version, first check the stored versions and then verify the relative selection-versus-later-period pattern rather than silently replacing the recorded environment.

## 6. Complete the backtest audit

For example, if a backtest shifts the signal before return calculation but reports no spread or slippage source, position timing can be marked `pass`, the cost convention must be marked `unclear`, and any claim about implementable net performance must remain `unclear`. The next check is to obtain or justify the missing cost inputs; a strong gross return cannot change that audit result.

Use `pass`, `fail`, or `unclear` for timing, feature availability, position lag, cost convention, benchmark alignment, frozen test period, number of tried variants, uncertainty analysis, and whether each conclusion stays within the tested evidence. Every `unclear` item requires a next check.

Expected evidence supplies a reason and next action for every `fail` or `unclear` judgment. If all items are marked `pass` but no experiment log or cost source exists, first change the unsupported rows to `unclear`.

## 7. Required evidence and completion

The Week 7 metric definitions, uncertainty checks, selection demonstration, and audit provide the performance-evidence boundary for Report 1. The detailed report rubric remains pending; this lesson does not add a new grading rule.

Submit both four-return calculations, the metric table, four-part interpretation, bootstrap intervals, repeated-selection result, completed audit, and [Week 7 evidence record](week7_support.md#performance-and-audit-record).

Your work is complete when all assumptions appear beside the metrics and a reader can distinguish measured performance from uncertainty and selection risk.

## Exit evidence

```text
The strongest measured result is ______.
Its main path risk is ______.
Its uncertainty is visible in ______.
The number of tried alternatives is ______.
The evidence is conditional on ______.
```
