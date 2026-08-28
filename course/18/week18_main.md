# Week 18 — Advanced Issues and Research Questions

## Core financial question

How can a limitation observed in the completed portfolio project become a falsifiable financial research question, and which advanced method is justified by that limitation rather than added only because it is complex?

## Observable outcome

After completing Week 18, you will use an observed limitation from the semester to formulate a falsifiable financial research question, implement one advanced comparison involving time-varying weights, a trade threshold, and transaction costs, distinguish that comparison from online portfolio selection and formal distributionally robust optimization, and specify the baselines, chronology, sensitivity checks, and evidence needed for a defensible study.

Week 18 does not add a fourth report. It uses the completed projects to distinguish a useful research question from an unsupported claim or a method in search of a problem.

## Teaching objectives

After completing this class, you should be able to:

- distinguish an observed empirical limitation from a general claim about markets;
- explain how regime changes, estimation uncertainty, and trading costs can affect time-varying weights;
- compare trading on every scheduled date with skipping trades below a stated weight-difference threshold;
- identify research roles for online allocation, machine learning, and distributionally robust optimization without treating complexity as evidence of value; and
- design a chronological experiment whose conclusions remain within the data and assumptions tested.

## 1. Begin with observed evidence

A research question should begin with a limitation that is visible in the completed analysis. Examples include unstable weights across estimation windows, concentration after optimization, poor performance in one market period, high turnover, sensitivity to random seeds, or disagreement among near-performing methods.

Write the limitation without asserting a cause that has not been tested:

```text
Observed result:
Exact table, figure, or output:
Data, period, and assumptions under which it occurred:
Why it matters for a financial decision:
Explanations still consistent with the evidence:
Evidence that is currently missing:
```

“Weights changed when the lookback changed from 60 to 120 observations” is observable. “The market entered a new regime” requires a defined regime measure and supporting evidence.

## 2. Formulate a falsifiable question

A useful question specifies the decision, comparison, evidence, and limits of the conclusion. Use this structure without treating it as a new named method:

```text
For [assets, market, frequency, and period],
when weights are chosen at [decision time] using [available information],
does [proposed method or modification], compared with [baseline],
change [primary financial outcome after costs]
under [common constraints and evaluation design]?
```

Then state:

- one primary outcome chosen before testing;
- one failure condition that would count against the proposed explanation;
- at least two baselines, including a simple allocation;
- a final period not used for selection or a prespecified walk-forward evaluation;
- robustness checks tied to plausible failure mechanisms; and
- the narrowest claim the design could support.

### Activity 1 — Challenge the question

Exchange questions with another team. The reviewer must identify one ambiguous time boundary, one missing common condition, and one result that would falsify the proposed explanation. Revise the question without adding a new method merely to avoid a negative result.

## 3. Time-varying weights under changing return conditions

If weights are updated over time,

$$
w_t=f(\mathcal{I}_t;\theta),
$$

then both the information set $\mathcal{I}_t$ and fixed settings $\theta$ must be documented. A shorter lookback responds faster but uses fewer observations; a longer lookback is smoother but can retain outdated information. This is a tradeoff to test, not a universal rule.

The artificial sample below has two deliberately different covariance conditions. The labels are known because the data are simulated; a real application would need a detection rule that uses only information available at the time.

```python
import numpy as np
import pandas as pd

rng = np.random.default_rng(181)
assets = ["A", "B", "C", "D"]
n_assets = len(assets)
n_per_condition = 260

vol_1 = np.array([0.008, 0.012, 0.010, 0.015])
corr_1 = np.array([
    [1.00, 0.20, 0.10, 0.15], [0.20, 1.00, 0.25, 0.20],
    [0.10, 0.25, 1.00, 0.15], [0.15, 0.20, 0.15, 1.00],
])
vol_2 = np.array([0.018, 0.025, 0.015, 0.030])
corr_2 = np.array([
    [1.00, 0.75, 0.65, 0.70], [0.75, 1.00, 0.70, 0.80],
    [0.65, 0.70, 1.00, 0.68], [0.70, 0.80, 0.68, 1.00],
])
cov_1 = np.outer(vol_1, vol_1) * corr_1
cov_2 = np.outer(vol_2, vol_2) * corr_2
part_1 = rng.multivariate_normal(np.repeat(0.0002, 4), cov_1, n_per_condition)
part_2 = rng.multivariate_normal(np.repeat(0.0001, 4), cov_2, n_per_condition)
returns = pd.DataFrame(
    np.vstack([part_1, part_2]),
    columns=assets,
    index=pd.bdate_range("2024-01-02", periods=2 * n_per_condition),
)
condition = pd.Series(
    ["first"] * n_per_condition + ["second"] * n_per_condition,
    index=returns.index,
)
print(returns.groupby(condition).std().round(4))
print("Shapes:", returns.shape, condition.value_counts().to_dict())
print("Dates:", returns.index.min().date(), "through", returns.index.max().date())
assert returns.shape == (520, 4)
assert returns.index.is_unique and returns.index.is_monotonic_increasing
assert np.isfinite(returns.to_numpy()).all()
assert returns.gt(-1).all().all()
assert np.linalg.eigvalsh(cov_1).min() > 0
assert np.linalg.eigvalsh(cov_2).min() > 0
assert (returns.loc[condition == "second"].std() > returns.loc[
    condition == "first"
].std()).all()
```

Expected evidence: dates from `2024-01-02` through `2025-12-29`, 520 rows, 260 in each condition, a sorted unique index, finite returns above `-1`, positive-definite input covariance matrices, and higher sample standard deviation for every asset in the second condition. Sample standard deviations are approximately `(0.0084, 0.0122, 0.0097, 0.0160)` and `(0.0188, 0.0263, 0.0151, 0.0311)`. If a check fails, inspect the simulated covariance inputs and label alignment before testing a portfolio. Do not use the condition label inside a portfolio rule; it is available only for evaluation.

### Activity 2 — Predict before testing

Predict which assets receive more inverse-volatility weight in each condition and whether turnover will rise near the transition. State what observation would contradict the prediction.

## 4. Compare scheduled trading with a weight-difference threshold

At a scheduled decision date, let $w_t^*$ be the newly estimated target and $w_{t^-}$ the existing portfolio. A threshold $b\geq0$ can be used to decide whether the difference is large enough to trade:

$$
w_t=
\begin{cases}
w_{t^-}, & \max_i|w_{i,t}^*-w_{i,t^-}|<b,\\
w_t^*, & \text{otherwise}.
\end{cases}
$$

This rule can reduce turnover but can also delay useful adaptation. It is related to transaction costs because fewer trades can reduce the cost deduction, but the threshold is not derived from or optimized against the stated cost function. Select it without repeatedly searching final-period results. The implementation uses $\frac12\sum_i|w_{i,new}-w_{i,old}|$ for turnover between fully invested portfolios, records initial investment from cash as 1, and lets weights drift between trades. Costs are deducted from return but do not alter post-return weights in this simplified accounting.

```python
def inverse_volatility(history):
    if not isinstance(history, pd.DataFrame) or history.shape[1] == 0:
        raise ValueError("History must be a DataFrame with asset columns")
    if not np.isfinite(history.to_numpy()).all():
        raise ValueError("History must contain finite returns")
    volatility = history.std(ddof=1)
    if volatility.isna().any() or (volatility <= 0).any():
        raise ValueError("Volatility estimates must be positive and available")
    raw = 1 / volatility
    weight = raw / raw.sum()
    if (
        not np.isfinite(weight).all()
        or not np.isclose(weight.sum(), 1.0)
        or (weight < 0).any()
    ):
        raise RuntimeError("Weight constraints failed")
    return weight


def threshold_backtest(
    data,
    evaluation_start=None,
    lookback=60,
    rebalance_every=10,
    band=0.0,
    cost_bps=5.0,
):
    if not isinstance(data, pd.DataFrame) or data.empty:
        raise ValueError("Data must be a nonempty DataFrame")
    if data.shape[1] == 0 or not data.columns.is_unique:
        raise ValueError("Asset columns must be nonempty and unique")
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
        or not np.isfinite(band)
        or band < 0
        or band > 1
        or not np.isfinite(cost_bps)
        or cost_bps < 0
    ):
        raise ValueError("Lookback, schedule, threshold, or cost is invalid")

    if evaluation_start is None:
        if len(data) <= lookback:
            raise ValueError("Data are insufficient for the lookback")
        start_position = lookback
        evaluation_start = data.index[start_position]
    else:
        evaluation_start = pd.Timestamp(evaluation_start)
        if evaluation_start not in data.index:
            raise ValueError("Evaluation start must be an observed date")
        start_position = int(data.index.get_loc(evaluation_start))
        if start_position < lookback:
            raise ValueError("Insufficient history at the evaluation start")

    current = pd.Series(0.0, index=data.columns)
    rows = []
    for t in range(start_position, len(data)):
        scheduled = (t - start_position) % rebalance_every == 0
        traded = False
        turnover = 0.0
        max_target_change = np.nan
        information_end = pd.NaT
        pretrade_weight = current.copy()
        target_weight = pd.Series(np.nan, index=data.columns)
        if scheduled:
            history = data.iloc[t - lookback:t].copy()
            target = inverse_volatility(history)
            target_weight = target.copy()
            information_end = history.index.max()
            max_target_change = float((target - current).abs().max())
            if current.sum() == 0 or max_target_change >= band:
                turnover = float(
                    1.0 if current.sum() == 0
                    else 0.5 * (target - current).abs().sum()
                )
                current = target.copy()
                traded = True
        gross = float(current @ data.iloc[t])
        cost = turnover * cost_bps / 10_000
        net = gross - cost
        if 1 + gross <= 0 or 1 + net <= 0:
            raise ValueError("Gross or net portfolio wealth became nonpositive")
        rows.append(
            {
                "date": data.index[t],
                "information_end": information_end,
                "gross_return": gross,
                "net_return": net,
                "turnover": turnover,
                "cost": cost,
                "concentration": float(np.square(current).sum()),
                "scheduled": scheduled,
                "traded": traded,
                "max_target_change": max_target_change,
                **{
                    f"pretrade_{asset}": pretrade_weight[asset]
                    for asset in data.columns
                },
                **{
                    f"target_{asset}": target_weight[asset]
                    for asset in data.columns
                },
            }
        )
        current = current * (1 + data.iloc[t]) / (1 + gross)
    result = pd.DataFrame(rows).set_index("date")
    decisions = result.loc[result["scheduled"]]
    assert (decisions["information_end"] < decisions.index).all()
    return result


def summarize_threshold_backtest(result):
    daily = result["net_return"]
    numeric_columns = [
        "net_return",
        "turnover",
        "cost",
        "concentration",
    ]
    if (
        len(daily) < 2
        or not np.isfinite(result[numeric_columns].to_numpy()).all()
        or (daily <= -1).any()
    ):
        raise ValueError("Return series is invalid or insufficient")
    wealth = (1 + daily).cumprod()
    running_peak = np.maximum.accumulate(
        np.r_[1.0, wealth.to_numpy()]
    )[1:]
    drawdown = wealth.to_numpy() / running_peak - 1
    return pd.Series(
        {
            "observations": len(result),
            "net_total_return": wealth.iloc[-1] - 1,
            "annualized_net_volatility": daily.std(ddof=1) * np.sqrt(252),
            "maximum_drawdown": drawdown.min(),
            "one_way_turnover": result["turnover"].sum(),
            "total_cost": result["cost"].sum(),
            "mean_concentration": result["concentration"].mean(),
            "scheduled_decisions": result["scheduled"].sum(),
            "executed_rebalances": result["traded"].sum(),
        }
    )

primary_start = returns.index[60]
results = {
    "always_rebalance": threshold_backtest(
        returns, evaluation_start=primary_start, band=0.0
    ),
    "band_2_percent": threshold_backtest(
        returns, evaluation_start=primary_start, band=0.02
    ),
    "band_5_percent": threshold_backtest(
        returns, evaluation_start=primary_start, band=0.05
    ),
}
comparison = pd.DataFrame(
    {
        name: summarize_threshold_backtest(result)
        for name, result in results.items()
    }
).T
print(comparison.round(6).to_string())
assert np.isfinite(comparison.to_numpy()).all()
assert (comparison["observations"] == 460).all()
assert comparison.loc["always_rebalance", "scheduled_decisions"] == comparison.loc[
    "always_rebalance", "executed_rebalances"
]
assert comparison.loc["band_5_percent", "executed_rebalances"] <= comparison.loc[
    "always_rebalance", "executed_rebalances"
]
assert all(
    np.allclose(result["cost"], result["turnover"] * 5 / 10_000)
    for result in results.values()
)
skipped = results["band_5_percent"].query("scheduled and not traded")
assert len(skipped) > 0
audit_columns = [f"{prefix}_{asset}" for prefix in ["pretrade", "target"] for asset in assets]
assert skipped[audit_columns].notna().all().all()
```

Expected evidence: three finite rows with 460 common observations and 46 scheduled decisions. Executed rebalances are 46, 19, and 7 for thresholds 0%, 2%, and 5%; turnovers are approximately `1.959544`, `1.663070`, and `1.358525`; and net total returns are `-0.084263`, `-0.082548`, and `-0.083314`. The comparison does not guarantee that a positive threshold lowers risk or raises net return. If no skipped trade appears, confirm the threshold, schedule, and saved weights before changing the experiment.

### Activity 3 — Audit a skipped trade

Find one scheduled date on which the 5% rule did not trade. Use the saved `pretrade_*` and `target_*` columns to show the current weights, target weights, maximum absolute difference, threshold, avoided turnover, and avoided immediate cost. Then identify the returns that followed. The saved fields make the calculation auditable; the historical consequence is not proof that skipping was optimal.

## 5. Compare evidence across the two artificial conditions

The following table uses the known condition label only after portfolio returns have been produced. It does not provide the label to the weighting or trading rules. The first-condition rows include the initial investment from cash; the second-condition rows include only trades made after that condition begins.

```python
second_condition_start = returns.index[n_per_condition]
condition_rows = []
for method_name, result in results.items():
    condition_subsets = {
        "first": result.loc[result.index < second_condition_start],
        "second": result.loc[result.index >= second_condition_start],
    }
    for condition_name, subset in condition_subsets.items():
        condition_rows.append(
            {
                "method": method_name,
                "condition": condition_name,
                **summarize_threshold_backtest(subset).to_dict(),
            }
        )

condition_comparison = pd.DataFrame(condition_rows).set_index(
    ["method", "condition"]
)
print(condition_comparison.round(6).to_string())
assert (condition_comparison.xs("first", level="condition")["observations"] == 200).all()
assert (condition_comparison.xs("second", level="condition")["observations"] == 260).all()
```

Expected evidence: every method has 200 first-condition and 260 second-condition observations. Annualized net volatility is about `0.109–0.111` in the first condition and `0.295–0.298` in the second. The 5% threshold executes 4 and 3 trades in the two conditions, compared with 20 and 26 scheduled trades when every scheduled target is executed. These are results for known artificial periods, not evidence that a real-time rule could identify a market regime.

### Activity 4 — distinguish an observed condition from a detected regime

State what the artificial label reveals after simulation. Then specify an observable real-time variable, estimation window, threshold or model, decision delay, and false-detection measure that would be required to use a regime concept in a real portfolio. A label assigned with future knowledge cannot be used as a trading input.

## 6. Measure estimation and cost-assumption sensitivity

Lookback and threshold are part of the method specification. The first table compares a small prespecified set on a common 400-observation interval beginning at the first date with 120 earlier observations available. The second table keeps lookback at 60 and changes the assumed linear cost. Neither table is a fresh final test.

```python
common_sensitivity_start = returns.index[120]
sensitivity_rows = []
for lookback in [20, 60, 120]:
    for band in [0.00, 0.02, 0.05]:
        result = threshold_backtest(
            returns,
            evaluation_start=common_sensitivity_start,
            lookback=lookback,
            band=band,
            cost_bps=5.0,
        )
        sensitivity_rows.append(
            {
                "lookback": lookback,
                "band": band,
                **summarize_threshold_backtest(result).to_dict(),
            }
        )

lookback_sensitivity = pd.DataFrame(sensitivity_rows).set_index(
    ["lookback", "band"]
)
print("lookback and threshold sensitivity")
print(lookback_sensitivity.round(6).to_string())
assert (lookback_sensitivity["observations"] == 400).all()

cost_rows = []
for cost_bps in [0, 5, 15]:
    for band in [0.00, 0.02, 0.05]:
        result = threshold_backtest(
            returns,
            evaluation_start=primary_start,
            lookback=60,
            band=band,
            cost_bps=cost_bps,
        )
        cost_rows.append(
            {
                "cost_bps": cost_bps,
                "band": band,
                **summarize_threshold_backtest(result).to_dict(),
            }
        )

cost_sensitivity = pd.DataFrame(cost_rows).set_index(["cost_bps", "band"])
print("\ncost and threshold sensitivity")
print(cost_sensitivity.round(6).to_string())
for band in [0.00, 0.02, 0.05]:
    band_rows = cost_sensitivity.xs(band, level="band")
    assert band_rows["one_way_turnover"].nunique() == 1
    assert band_rows["net_total_return"].is_monotonic_decreasing
```

Expected evidence: nine lookback rows with 400 common dates and nine cost rows. In the cost table, turnover stays fixed within each threshold and net total return decreases as cost rises. At 5 basis points, the 2% threshold has a higher artificial full-period net return than the other two thresholds, but the size and ordering depend on the tested data and settings. Report estimation and specification sensitivity rather than selecting one row after observing the grid and presenting it as untouched evidence.

## 7. Connect advanced methods to the observed limitation

Several established research directions could extend a portfolio decision, but they answer different questions:

- **Time-varying estimation** updates means, covariances, scenarios, or model parameters as information arrives. This is already present in the rolling inverse-volatility example.
- **Market-regime modeling** defines latent or observable conditions and a rule for estimating the current condition. The artificial condition label is an evaluation aid, not a real-time detector.
- **Online portfolio selection** studies sequential allocation decisions and cumulative performance with a stated information protocol. A loop that updates weights is not, by itself, evidence that a recognized online algorithm or its guarantees have been implemented.
- **Machine-learning allocation** may estimate conditional return, risk, state, or weights. It still requires a financial target, chronological training, simple baselines, constraints, costs, and comparison of prediction quality with portfolio outcomes.
- **Distributionally robust optimization** optimizes over a specified set of plausible probability distributions. The distance, reference distribution, and size of that set are assumptions. A stress row or sensitivity grid alone is not distributionally robust optimization.
- **Estimation uncertainty** asks how finite observations, resampling, windows, models, and seeds change inputs or decisions. Showing sensitivity does not identify the true parameter.

For the limitation selected in Section 1, choose at most one advanced extension and complete:

```text
Observed limitation and evidence:
Simplest credible comparison:
Advanced extension, if needed:
New assumption introduced by the extension:
Why the extension targets the stated limitation:
Common data, constraints, costs, and outcome:
Result that would count against the explanation:
Reason to reject the advanced extension if the simple comparison is sufficient:
```

### Activity 5 — reject unnecessary complexity

Defend the simplest credible design first. Choose the advanced alternative only when it tests a specific explanation that the simple comparison cannot test. Complexity, novelty, and a higher in-sample result are not financial evidence by themselves.

## 8. Plan and defend the proposed study

Use [the Week 18 planning template](week18_support.md#plan-the-proposed-study). It must contain one primary question and outcome, justified methods, common comparison conditions, decision chronology, sensitivity checks tied to failure mechanisms, a result that would count against the explanation, and explicit limits on any conclusion. Exchange plans and revise one ambiguity without promising a positive result.

## Required evidence and completion criteria

Submit or preserve:

1. one limitation traced to final-report evidence;
2. the revised falsifiable question and peer challenge;
3. the artificial-condition audit and pre-analysis predictions;
4. the rebalancing comparison, skipped-trade audit, and accounting checks;
5. the condition-specific, lookback, threshold, and cost-sensitivity tables;
6. the simple-versus-advanced design comparison; and
7. the completed plan for the proposed study.

Your work is complete when another student can identify the observation motivating the question, reproduce the advanced comparison, state what would count against the explanation, and see the exact boundary of any eventual claim.

## Exit evidence

```text
The observed limitation is ______ under ______.
The research question asks whether ______ compared with ______ changes ______.
The result that would count against my explanation is ______.
The most important information boundary is ______.
The narrowest claim this design could support is ______.
```
