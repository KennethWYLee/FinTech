# Week 15 — Revise, Validate, and Freeze the Proposed Weighting Method

## Core financial question

After the Week 14 progress report, what evidence is required to correct, compare, and freeze a proposed portfolio-weighting method without using the final period to choose the method that is later evaluated on that period?

Week 15 is a development, validation, and submission class rather than another report session. Teams use only development evidence to revise the proposed method and settings, stop making choices before revealing final-period results, run one common chronological final evaluation, and preserve the exact report, code, and results for Weeks 16–17.

## Observable outcome

After completing Week 15, each team will have a reproducible weighting function, generic and method-specific tests, development-period baseline and sensitivity comparisons, a documented point at which method choices stopped, one common final-period evaluation with transaction costs, turnover, concentration, and drawdown, and an identifiable frozen submission.

The [final report criteria](../16/week16_main.md) define the assessed evidence. The common dataset and numerical settings for the course remain subject to instructor approval. This material defines the technical and reproducibility work required for the frozen submission without changing the grading weights.

## Teaching objectives

After completing this class, you should be able to:

- express a weighting method as a mapping from information available at a decision time to ordered portfolio weights;
- turn Week 14 feedback into one testable correction or comparison at a time;
- test inputs, labels, shape, finite values, full investment, and bounds before calculating performance;
- compare a proposed method and baselines under identical development conditions;
- examine costs, turnover, concentration, and selected sensitivity settings without inspecting final-period performance;
- stop revising the method and settings before final evaluation; and
- preserve a version that another team member can rerun from the first cell.

## Before you begin

Bring the Week 14 code, evidence, questions, and proposed changes. Use a clean notebook with `numpy` and `pandas`. All data and numerical results below are artificial. The 60-observation lookback, 21-business-day rebalance interval, 4-basis-point cost, 50% cap, artificial dates, and sensitivity settings are teaching assumptions rather than approved course-wide settings.

## 1. Convert feedback into one verifiable change at a time

Review the Week 14 questions. First address a correctness problem that can invalidate the experiment. Next address a design choice that needs a controlled comparison. Finally address wording, table, or figure problems. When attribution matters, do not change several assumptions at once.

### Activity 1 — record one change

```text
Feedback or observed limitation:
Why it threatens correctness, interpretation, or communication:
Affected formula, function, or assumption:
Previous behavior:
One change to be tested:
Evidence required:
Outputs that must be regenerated:
Result after the change:
Remaining limitation:
```

A lower final return is not itself a correctness problem. A violation of decision timing, a broken constraint, inconsistent costs, or a result that cannot be reproduced is.

## 2. Specify the weighting function before comparing performance

At rebalance date $t$, write the method as

$$
w_t=f(\mathcal{I}_t;\theta),
$$

where $\mathcal{I}_t$ contains information available no later than the stated decision time and $\theta$ contains settings fixed before final evaluation. The output must follow the asset order and satisfy the written constraints.

Complete this description before running the final period:

```text
Financial decision and purpose:
Inputs and financial interpretation:
Latest permissible observation at decision time:
Estimation-window rule:
Formula, objective, or deterministic allocation rule:
Fixed settings:
Full-investment, long-only, and asset-level constraints:
Behavior when inputs or optimization are invalid:
Rebalance and execution rules:
Expected output shape and asset order:
Baseline methods:
Evidence that would contradict the proposed claim:
```

If the prose, formula, and function implement different methods, stop and reconcile them before evaluating returns.

## 3. Create artificial data and test the weighting functions

Run every cell in order in a clean notebook.

```python
import numpy as np
import pandas as pd

rng = np.random.default_rng(151)
assets = ["A", "B", "C", "D", "E"]
n_assets = len(assets)
n_observations = 440

common_factor = rng.normal(0.0002, 0.009, n_observations)
idiosyncratic_scale = np.array([0.006, 0.009, 0.007, 0.012, 0.008])
return_matrix = (
    common_factor[:, None] * np.array([0.7, 1.0, 0.8, 1.2, 0.6])
    + rng.normal(
        0.0,
        idiosyncratic_scale,
        (n_observations, n_assets),
    )
)
returns = pd.DataFrame(
    return_matrix,
    columns=assets,
    index=pd.bdate_range("2024-01-02", periods=n_observations),
)
development = returns.iloc[:300].copy()
final_period = returns.iloc[300:].copy()
development_evaluation_start = development.index[100]

print("development/final shapes:", development.shape, final_period.shape)
print(
    "development dates:",
    development.index.min().date(),
    "through",
    development.index.max().date(),
)
print(
    "final dates:",
    final_period.index.min().date(),
    "through",
    final_period.index.max().date(),
)
print("development evaluation starts:", development_evaluation_start.date())

assert development.shape == (300, 5) and final_period.shape == (140, 5)
assert development.index.max() < final_period.index.min()
assert returns.index.is_monotonic_increasing
assert not returns.index.has_duplicates
assert np.isfinite(returns.to_numpy()).all()
assert returns.gt(-1).all().all()
```

Expected evidence: development dates from `2024-01-02` through `2025-02-24`, final dates from `2025-02-25` through `2025-09-08`, shapes `(300, 5)` and `(140, 5)`, and development evaluation beginning on `2024-05-21`. The artificial business-day index does not remove exchange holidays. The code applies only prespecified finite-value and return-range checks to final rows; it does not calculate a final-period performance statistic in this section.

```python
def validate_weights(weight, columns, lower=0.0, upper=0.50):
    columns = pd.Index(columns)
    if len(columns) == 0 or not columns.is_unique:
        raise ValueError("Asset columns must be nonempty and unique")
    if (
        not np.isfinite([lower, upper]).all()
        or lower < 0
        or lower > upper
        or lower * len(columns) > 1 + 1e-12
        or upper * len(columns) < 1 - 1e-12
    ):
        raise ValueError("Weight bounds are invalid or infeasible")

    if isinstance(weight, pd.Series):
        if not weight.index.is_unique or set(weight.index) != set(columns):
            raise ValueError("Weight labels do not match the asset columns")
        checked = weight.reindex(columns).astype(float)
    else:
        values = np.asarray(weight, dtype=float)
        if values.ndim != 1 or len(values) != len(columns):
            raise ValueError("Weight shape does not match the asset columns")
        checked = pd.Series(values, index=columns, dtype=float)

    if not np.isfinite(checked).all():
        raise ValueError("Weights must be finite")
    if not np.isclose(checked.sum(), 1.0, atol=1e-8):
        raise ValueError("Weights must sum to one")
    if (checked < lower - 1e-10).any() or (checked > upper + 1e-10).any():
        raise ValueError("A weight violates the stated bounds")
    return checked


def normalize_with_cap(raw, cap=0.50):
    raw = pd.Series(raw, dtype=float).copy()
    if (
        raw.empty
        or not np.isfinite(raw).all()
        or (raw < 0).any()
        or raw.sum() <= 0
    ):
        raise ValueError("Raw inputs must be finite, nonnegative, and nonzero")
    if (
        not np.isfinite(cap)
        or not 0 < cap <= 1
        or cap * len(raw) < 1 - 1e-12
    ):
        raise ValueError("Cap is invalid or infeasible")

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
                remaining_budget / len(active_raw),
                index=active_raw.index,
            )
        over_cap = proposed > cap + 1e-12
        if not over_cap.any():
            weight.loc[proposed.index] = proposed
            remaining_budget = 0.0
            break
        capped_assets = proposed.index[over_cap]
        weight.loc[capped_assets] = cap
        active.loc[capped_assets] = False
        remaining_budget -= cap * len(capped_assets)

    if (
        abs(remaining_budget) > 1e-10
        or not np.isclose(weight.sum(), 1.0)
        or (weight > cap + 1e-10).any()
    ):
        raise RuntimeError("Cap allocation failed")
    return weight


def equal_weight(history):
    return pd.Series(1 / history.shape[1], index=history.columns)


def inverse_volatility(history):
    volatility = history.std(ddof=1)
    if volatility.isna().any() or (volatility <= 0).any():
        raise ValueError("Volatility estimates must be positive and available")
    return normalize_with_cap(1 / volatility, cap=0.50)


def student_method(history):
    # Replace this placeholder with the team's documented weighting method.
    return inverse_volatility(history)


history = development.iloc[-60:]
methods = {
    "equal_weight": equal_weight,
    "inverse_volatility": inverse_volatility,
    "student_method": student_method,
}
checked_weights = {
    name: validate_weights(method(history), history.columns)
    for name, method in methods.items()
}
print(pd.DataFrame(checked_weights).round(6).to_string())

extreme_raw = normalize_with_cap([100, 1, 1, 1, 1], cap=0.30)
print("extreme raw-input check:", extreme_raw.round(6).to_list())
assert np.allclose(extreme_raw, [0.30, 0.175, 0.175, 0.175, 0.175])
assert checked_weights["student_method"].equals(
    checked_weights["inverse_volatility"]
)

try:
    validate_weights([0.7, 0.1, 0.1, 0.1, 0.0], assets)
except ValueError as error:
    print("expected failure:", error)
else:
    raise AssertionError("The intentional bound violation should fail")
```

Expected evidence: three finite weight columns in the stated asset order, each summing to one and respecting the 50% cap. The placeholder and inverse-volatility columns match exactly. The extreme input produces `(0.30, 0.175, 0.175, 0.175, 0.175)`, and the intentional invalid vector fails. Replace the placeholder body and description before treating it as a proposed method.

### Activity 2 — add method-specific tests

Add at least two tests implied by the proposed definition. Possible subjects include a risk-contribution tolerance, maximum selected-asset count, stated fallback, optimization residual, turnover bound, or response to infeasible inputs. State why each property follows from the method; do not add a test only because the current output happens to pass it.

## 4. Compare methods on development data only

The function below starts each comparison from cash on the common evaluation-start date. At every decision, its history ends on the preceding row. It rebalances immediately on the first evaluation date and every stated number of business-day observations afterward. Initial one-way turnover is 1; subsequent turnover is

$$
\frac12\sum_i|w_{i,t}^{target}-w_{i,t}^{hold}|.
$$

Four basis points per unit of turnover is deducted from return. Holdings then drift with asset returns. This simplified accounting does not model bid–ask dynamics, market impact, taxes, or financing.

```python
def run_backtest(
    data,
    method,
    evaluation_start,
    lookback=60,
    rebalance_every=21,
    cost_bps=4.0,
):
    if not isinstance(data, pd.DataFrame) or data.empty:
        raise ValueError("Data must be a nonempty DataFrame")
    if not isinstance(data.index, pd.DatetimeIndex):
        raise ValueError("Data must have a DatetimeIndex")
    if not data.index.is_monotonic_increasing or data.index.has_duplicates:
        raise ValueError("Dates must be ordered and unique")
    if not np.isfinite(data.to_numpy()).all() or not data.gt(-1).all().all():
        raise ValueError("Returns must be finite and greater than -1")
    if (
        not isinstance(lookback, int)
        or isinstance(lookback, bool)
        or lookback < 2
        or not isinstance(rebalance_every, int)
        or isinstance(rebalance_every, bool)
        or rebalance_every < 1
        or not np.isfinite(cost_bps)
        or cost_bps < 0
    ):
        raise ValueError("Lookback, rebalance interval, or cost is invalid")

    evaluation_start = pd.Timestamp(evaluation_start)
    if evaluation_start not in data.index:
        raise ValueError("Evaluation start must be an observed date")
    start_position = int(data.index.get_loc(evaluation_start))
    if start_position < lookback:
        raise ValueError("Insufficient earlier observations at evaluation start")

    held_weight = pd.Series(0.0, index=data.columns)
    rows = []
    for t in range(start_position, len(data)):
        rebalance = (t - start_position) % rebalance_every == 0
        turnover = 0.0
        information_end = pd.NaT
        if rebalance:
            history = data.iloc[t - lookback:t].copy()
            target = validate_weights(method(history), data.columns)
            turnover = (
                1.0
                if held_weight.sum() == 0
                else 0.5 * (target - held_weight).abs().sum()
            )
            held_weight = target.copy()
            information_end = history.index.max()

        gross_return = float(held_weight @ data.iloc[t])
        cost = turnover * cost_bps / 10_000
        net_return = gross_return - cost
        if 1 + gross_return <= 0 or 1 + net_return <= 0:
            raise ValueError("Gross or net portfolio wealth became nonpositive")

        rows.append(
            {
                "date": data.index[t],
                "information_end": information_end,
                "rebalance": rebalance,
                "gross_return": gross_return,
                "turnover": turnover,
                "cost": cost,
                "net_return": net_return,
                "concentration": float(np.square(held_weight).sum()),
            }
        )
        held_weight = (
            held_weight
            * (1 + data.iloc[t])
            / (1 + gross_return)
        )

    result = pd.DataFrame(rows).set_index("date")
    decisions = result.loc[result["rebalance"]]
    assert (decisions["information_end"] < decisions.index).all()
    return result


def summarize_backtest(result):
    required = ["net_return", "turnover", "cost", "concentration"]
    numeric = result[required].to_numpy(dtype=float)
    if (
        len(result) < 2
        or not np.isfinite(numeric).all()
        or (result["net_return"] <= -1).any()
    ):
        raise ValueError("Backtest result is invalid or insufficient")

    daily = result["net_return"]
    wealth = (1 + daily).cumprod()
    running_peak = np.maximum.accumulate(
        np.r_[1.0, wealth.to_numpy()]
    )[1:]
    drawdown = wealth.to_numpy() / running_peak - 1
    volatility = daily.std(ddof=1)
    return pd.Series(
        {
            "observations": len(result),
            "net_total_return": wealth.iloc[-1] - 1,
            "annualized_net_volatility": volatility * np.sqrt(252),
            "sharpe_zero_rf": (
                np.nan
                if volatility == 0
                else daily.mean() / volatility * np.sqrt(252)
            ),
            "maximum_drawdown": drawdown.min(),
            "one_way_turnover": result["turnover"].sum(),
            "total_cost": result["cost"].sum(),
            "mean_concentration": result["concentration"].mean(),
        }
    )


development_backtests = {
    name: run_backtest(
        development,
        method,
        evaluation_start=development_evaluation_start,
    )
    for name, method in methods.items()
}
development_index = development_backtests["equal_weight"].index
assert all(
    result.index.equals(development_index)
    for result in development_backtests.values()
)
development_comparison = pd.DataFrame(
    {
        name: summarize_backtest(result)
        for name, result in development_backtests.items()
    }
).T
print(development_comparison.round(6).to_string())
assert np.isfinite(development_comparison.to_numpy()).all()
```

Expected evidence: 200 common development observations for every method. Equal weighting has net total return `-0.013194`, annualized net volatility `0.141501`, maximum drawdown `-0.163046`, turnover `1.107582`, and mean concentration `0.200113`. The placeholder and inverse-volatility rows match at net total return `-0.011667`, annualized net volatility `0.135228`, maximum drawdown `-0.159127`, turnover `1.289818`, and mean concentration `0.210095`. All decision records use an `information_end` earlier than the return date, all methods begin with turnover 1, and each cost equals turnover times `4 / 10_000`. These artificial results are development evidence and may be used for revision; no final-period return has been calculated.

### Activity 3 — audit one rebalance

Choose one decision after the initial investment. Preserve the last permissible information date, target weights, drifted holdings immediately before trade, asset-by-asset absolute differences, one-way turnover, basis-point conversion, cost, gross return, and net return. Confirm the same accounting is used for every baseline.

## 5. Run prespecified sensitivity checks on common development dates

The next cell changes one setting at a time. All nine rows use the same 200 development dates and therefore remain available for method revision. The listed values must be declared before their results are compared.

```python
sensitivity_rows = []
for lookback in [40, 60, 100]:
    result = run_backtest(
        development,
        student_method,
        evaluation_start=development_evaluation_start,
        lookback=lookback,
    )
    sensitivity_rows.append(
        {
            "check": "lookback",
            "setting": lookback,
            **summarize_backtest(result).to_dict(),
        }
    )

for interval in [10, 21, 42]:
    result = run_backtest(
        development,
        student_method,
        evaluation_start=development_evaluation_start,
        rebalance_every=interval,
    )
    sensitivity_rows.append(
        {
            "check": "rebalance_every",
            "setting": interval,
            **summarize_backtest(result).to_dict(),
        }
    )

for cost_bps in [0, 4, 10]:
    result = run_backtest(
        development,
        student_method,
        evaluation_start=development_evaluation_start,
        cost_bps=cost_bps,
    )
    sensitivity_rows.append(
        {
            "check": "cost_bps",
            "setting": cost_bps,
            **summarize_backtest(result).to_dict(),
        }
    )

development_sensitivity = pd.DataFrame(sensitivity_rows).set_index(
    ["check", "setting"]
)
print(development_sensitivity.round(6).to_string())
assert (development_sensitivity["observations"] == 200).all()
assert (
    development_sensitivity.loc["cost_bps", "net_total_return"]
    .is_monotonic_decreasing
)
```

Expected evidence: nine finite rows with 200 identical dates. Net total returns for lookbacks 40, 60, and 100 are approximately `-0.008505`, `-0.011667`, and `-0.009484`; for rebalance intervals 10, 21, and 42 they are `-0.010519`, `-0.011667`, and `-0.012662`. Holding signals and turnover fixed, net total return decreases from `-0.011159` to `-0.011667` and `-0.012429` as cost rises from 0 to 4 and 10 basis points. Changes across lookbacks and rebalance intervals may reflect both new estimates and new trading paths; preserve turnover, cost, concentration, and drawdown with return and volatility.

### Activity 4 — revise or narrow the claim

For each sensitivity dimension, state what remained fixed, what changed, and which result weakens the proposed conclusion. Decide whether development evidence requires a correction, a justified revision, or a narrower claim. Do not select only the strongest row and discard the others.

## 6. Stop revising before revealing final-period results

After all development changes are complete, record the exact method and settings. From this point, final-period performance may not change the function, lookback, rebalancing, cap, cost, baselines, or measures. If a correctness error is found later, disclose the error, repair it, and treat the current final period as used development evidence; a fresh out-of-sample claim then requires another period.

```python
frozen_settings = {
    "method_function": "student_method",
    "lookback": 60,
    "rebalance_every": 21,
    "cost_bps": 4.0,
    "long_only": True,
    "full_investment": True,
    "asset_cap": 0.50,
    "development_end": str(development.index.max().date()),
    "final_start": str(final_period.index.min().date()),
    "final_end": str(final_period.index.max().date()),
}
print(pd.Series(frozen_settings).to_string())
```

This printed dictionary is not a complete version identifier. Add the exact code version, environment, data specification or checksum, report draft, random seeds, and the names of every submitted file to the [Week 15 submitted-version record](week15_support.md#record-the-submitted-version). A team member who did not make the final change should confirm the record before the next cell is run.

## 7. Run the common final-period evaluation once

Every method starts from cash on `2025-02-25`, estimates its first weights from the preceding 60 development observations, and then follows the frozen 21-observation schedule. After the first decision, realized earlier final-period returns may enter later rolling histories because they are available by then. No later return may enter an earlier decision.

```python
final_start = final_period.index.min()
final_backtests = {
    name: run_backtest(
        returns,
        method,
        evaluation_start=final_start,
        lookback=frozen_settings["lookback"],
        rebalance_every=frozen_settings["rebalance_every"],
        cost_bps=frozen_settings["cost_bps"],
    )
    for name, method in methods.items()
}
final_index = final_backtests["equal_weight"].index
assert len(final_index) == 140
assert all(result.index.equals(final_index) for result in final_backtests.values())
assert all(
    np.isclose(result["turnover"].iloc[0], 1.0)
    for result in final_backtests.values()
)
assert all(
    np.allclose(
        result["cost"],
        result["turnover"] * frozen_settings["cost_bps"] / 10_000,
    )
    for result in final_backtests.values()
)

final_comparison = pd.DataFrame(
    {
        name: summarize_backtest(result)
        for name, result in final_backtests.items()
    }
).T
print(final_comparison.round(6).to_string())
assert np.isfinite(final_comparison.to_numpy()).all()
assert final_comparison.loc["student_method"].equals(
    final_comparison.loc["inverse_volatility"]
)
```

Expected evidence: three rows with 140 identical final-period observations, first-date turnover 1, and a valid information boundary at every rebalance. Equal weighting has net total return `-0.075400`, annualized net volatility `0.127854`, maximum drawdown `-0.112437`, turnover `1.100781`, and mean concentration `0.200154`. The placeholder and inverse-volatility rows match at net total return `-0.070500`, annualized net volatility `0.123612`, maximum drawdown `-0.103379`, turnover `1.174898`, and mean concentration `0.206006`. Compare return, volatility, zero-risk-free-rate Sharpe ratio, drawdown, turnover, cost, and concentration together. The artificial final period may confirm, weaken, or contradict the development claim; it must not be hidden or used to create a replacement method that is evaluated on the same rows.

## 8. Freeze the final report, code, and results

Immediately preserve:

1. the exact code and package versions;
2. artificial generator or approved data source, fields, dates, adjustments, and access information;
3. method formula, settings, constraints, decision timing, and failure behavior;
4. baseline definitions and common evaluation conditions;
5. complete solver messages, failures, exclusions, and sensitivity rows;
6. final tables and figures linked to the code that produced them;
7. a Git commit, file hashes, or archive identifier for the submitted files; and
8. claims and limitations that match the displayed evidence.

If a correction is unavoidable after seeing final results, disclose that those rows no longer provide unused evidence for the revised method and remove or relabel the earlier final claim. Have another team member rerun from a clean environment. Differences must be resolved or disclosed before Week 16.

## 9. Required evidence and completion

Submit the change record, method description, generic and method-specific tests, development baseline table, rebalance audit, complete development sensitivity table, printed frozen settings, common final-period table, and completed [Week 15 submitted-version record](week15_support.md#record-the-submitted-version). The final package also contains the report, code, and results required by the course.

Week 15 is complete when another team member can reproduce the submitted outputs, every comparison uses stated common conditions, the final period was not used to select the submitted method or settings, and every claim points to visible evidence.

## Exit evidence

```text
The frozen version is identified by ______.
The most important corrected problem was ______.
Development evidence changed or narrowed the method because ______.
The final-period result supported or contradicted ______.
The narrowest conclusion supported by both periods is ______.
The principal unresolved limitation is ______.
```
