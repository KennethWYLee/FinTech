# Week 13 — Downside Risk and Minimum-CVaR Portfolios

## Core financial question

How does a portfolio change when the decision focuses on tail loss, and what can be concluded when minimum-CVaR weights are estimated from finite scenarios and then modified by portfolio constraints, turnover limits, transaction costs, regularization, and stress assumptions?

Week 12 compared allocations defined through volatility and covariance. Week 13 changes the loss measure to Conditional Value at Risk (CVaR), constructs the finite-scenario linear program, and tests how implementation choices affect the decision. The evidence supports the Week 14 progress report and prepares for the Week 15 comparison of scenario-generation methods.

## Observable outcome

By the end of this 150-minute class, you will calculate empirical Value at Risk (VaR) and CVaR, solve and audit a long-only minimum-CVaR portfolio, add an explicit turnover limit, linear transaction-cost term, and absolute-deviation regularization term, and compare sensitivity to scenario count, random seed, and a stated stress scenario.

## Teaching objectives

After completing this class, you should be able to:

- translate returns into losses and state the empirical quantile and tail-membership conventions;
- formulate the finite-scenario minimum-CVaR problem as a linear program;
- verify solver status, full investment, bounds, excess-loss inequalities, and objective components;
- distinguish a hard turnover limit from a transaction-cost term and a regularization term;
- compare decisions across prespecified scenario counts, seeds, and stress assumptions; and
- separate sensitivity evidence from the guarantees of a formally specified robust optimization problem.

## Before you begin

Use a clean notebook with `numpy`, `pandas`, and `scipy`. Bring Week 7's drawdown calculation, Week 11's constraint audits, and Week 12's rule that test results must not be used to redesign a method while still being described as unused. All returns and results below are artificial. The 95% confidence level, scenario counts, seeds, 60% cap, 12% turnover limit, 4-basis-point cost, regularization coefficient, and stress returns are teaching assumptions.

## 150-minute class plan

| Time | Focus | Required evidence |
|---:|---|---|
| 0–15 minutes | Returns, losses, VaR, and CVaR | Hand-sorted tail calculation |
| 15–30 minutes | Finite scenarios and limits of inference | Scenario specification |
| 30–50 minutes | Artificial data and empirical estimates | Chronological split and training estimate |
| 50–75 minutes | Minimum-CVaR linear program | Weights, objective, and constraint audit |
| 75–100 minutes | Turnover, costs, and regularization | Formulation and comparison |
| 100–120 minutes | Scenario-count and seed sensitivity | Repeated-solution table |
| 120–135 minutes | Stress assumptions and meaning of robustness | Changed weights and bounded conclusion |
| 135–145 minutes | Common later-period evaluation | Net performance and tail-risk table |
| 145–150 minutes | Exit evidence | Supported conclusion and next question |

## 1. Define downside risk using losses

For one-period portfolio return $r_{p,t}$, define loss as

$$
L_t=-r_{p,t}.
$$

At confidence level $\alpha$, Value at Risk is an $\alpha$-quantile of loss:

$$
\mathrm{VaR}_{\alpha}(L)
=\inf\{\ell:\Pr(L\leq\ell)\geq\alpha\}.
$$

For a continuous loss distribution, CVaR can be interpreted as the expected loss in the tail beyond VaR. With finite observations or probability mass at the threshold, the mean selected by a rule such as `loss >= VaR` need not equal the optimization expression used later. Every numerical result must therefore state its quantile and tail-membership conventions.

### Activity 1 — sort the tail by hand

For losses `[-0.02, 0.00, 0.01, 0.03, 0.08]` and $\alpha=0.80$, calculate NumPy's default linear empirical quantile and identify the losses selected by `loss >= VaR`. The quantile is `0.04`, only `0.08` is selected, and the selected-loss mean is `0.08`. Explain why the loss `-0.02` represents a positive return.

## 2. Specify finite scenarios before optimization

A scenario is one simultaneous vector of asset returns. With $S$ scenarios $r_s$, portfolio loss for weights $w$ is $L_s=-r_s^\top w$. This class initially draws complete rows with replacement from artificial training returns. Complete rows preserve cross-sectional relationships within each selected observation, but ordinary row bootstrap does not reproduce time ordering or guarantee that the training sample describes future tails.

Before inspecting optimized weights, record the training dates, observation count, return frequency, asset order, sampling method, replacement rule, scenario count, random seed, confidence level, constraints, previous weights, and whether test results have been inspected. A larger scenario count may reduce simulation variation from a fixed generator. It cannot repair a generator that omits relevant market behavior.

## 3. Create artificial data and calculate empirical risk

Run every code cell in order in a clean notebook.

```python
import numpy as np
import pandas as pd
from scipy.optimize import linprog

rng = np.random.default_rng(131)
assets = ["A", "B", "C", "D"]
n_assets = len(assets)
annual_mean = np.array([0.05, 0.07, 0.06, 0.09])
annual_volatility = np.array([0.10, 0.15, 0.12, 0.22])
correlation = np.array(
    [
        [1.00, 0.45, 0.30, 0.25],
        [0.45, 1.00, 0.40, 0.55],
        [0.30, 0.40, 1.00, 0.35],
        [0.25, 0.55, 0.35, 1.00],
    ]
)
annual_covariance = np.outer(annual_volatility, annual_volatility) * correlation
return_matrix = rng.multivariate_normal(
    annual_mean / 252, annual_covariance / 252, size=520
)

# Insert several adverse artificial training observations.
shock_rows = np.array([40, 115, 190, 265, 330])
return_matrix[shock_rows] += np.array([-0.025, -0.040, -0.020, -0.055])
returns = pd.DataFrame(
    return_matrix,
    columns=assets,
    index=pd.bdate_range("2024-01-02", periods=520),
)
train = returns.iloc[:380].copy()
test = returns.iloc[380:].copy()

print("train/test shapes:", train.shape, test.shape)
print("training dates:", train.index.min().date(), "through", train.index.max().date())
print("test dates:", test.index.min().date(), "through", test.index.max().date())
print("missing values:", int(returns.isna().sum().sum()))

assert train.shape == (380, 4) and test.shape == (140, 4)
assert train.index.max() < test.index.min()
assert returns.index.is_monotonic_increasing
assert not returns.index.has_duplicates
assert returns.gt(-1).all().all()
```

Expected evidence: training dates from `2024-01-02` through `2025-06-16`, test dates from `2025-06-17` through `2025-12-29`, shapes `(380, 4)` and `(140, 4)`, and zero missing values. The artificial business-day index does not remove exchange holidays.

```python
def empirical_var_cvar(portfolio_returns, alpha=0.95):
    losses = -np.asarray(portfolio_returns, dtype=float)
    if losses.ndim != 1 or len(losses) == 0 or not np.isfinite(losses).all():
        raise ValueError("Portfolio returns must be a finite nonempty vector")
    if not np.isfinite(alpha) or not 0 < alpha < 1:
        raise ValueError("Alpha must be finite and strictly between zero and one")
    var = float(np.quantile(losses, alpha, method="linear"))
    tail = losses[losses >= var]
    if len(tail) == 0:
        raise RuntimeError("The tail-membership rule selected no observations")
    return var, float(tail.mean()), len(tail)


equal_weight = np.repeat(1 / n_assets, n_assets)
train_var, train_cvar, train_tail_count = empirical_var_cvar(
    train.to_numpy() @ equal_weight
)
training_baseline = pd.Series(
    {
        "VaR": train_var,
        "selected_loss_mean": train_cvar,
        "selected_observations": train_tail_count,
    },
    name="equal_weight_training",
)
print(training_baseline.round(6).to_string())
```

Expected evidence: training VaR `0.012402`, selected-loss mean `0.021092`, and 19 selected observations. These are one-period target-weight returns, not a buy-and-hold path with drifting weights. The test returns remain unexamined until all four target vectors and settings are fixed in Section 8.

## 4. Formulate and audit the minimum-CVaR linear program

Rockafellar and Uryasev express finite-scenario CVaR using a threshold $\zeta$ and nonnegative excess-loss variables $u_s$:

$$
\min_{w,\zeta,u}\quad
\zeta+\frac{1}{(1-\alpha)S}\sum_{s=1}^{S}u_s
$$

subject to

$$
u_s\geq-r_s^\top w-\zeta,\qquad
u_s\geq0,\qquad
\mathbf{1}^\top w=1,\qquad
0\leq w_i\leq0.60.
$$

The formulation is a linear program because its scenarios are fixed. The implementation also creates variables for absolute changes from previous weights and absolute deviations from a reference. Their coefficients are initially zero. Section 5 activates them without changing the CVaR scenario constraints.

```python
def solve_cvar_portfolio(
    scenarios,
    alpha=0.95,
    cap=0.60,
    previous_weight=None,
    turnover_limit=None,
    cost_bp=0.0,
    regularization=0.0,
    regularization_target=None,
):
    scenarios = np.asarray(scenarios, dtype=float)
    if (
        scenarios.ndim != 2
        or scenarios.shape[0] == 0
        or scenarios.shape[1] == 0
        or not np.isfinite(scenarios).all()
    ):
        raise ValueError("Scenarios must be a finite nonempty matrix")
    if not np.isfinite(alpha) or not 0 < alpha < 1:
        raise ValueError("Alpha must be finite and strictly between zero and one")

    n_scenarios, n_problem_assets = scenarios.shape
    if (
        not np.isfinite(cap)
        or not 0 < cap <= 1
        or cap * n_problem_assets < 1 - 1e-12
    ):
        raise ValueError("Cap is invalid or infeasible")
    if not np.isfinite(cost_bp) or cost_bp < 0:
        raise ValueError("Cost must be finite and nonnegative")
    if not np.isfinite(regularization) or regularization < 0:
        raise ValueError("Regularization must be finite and nonnegative")

    if previous_weight is None:
        previous_weight = np.repeat(1 / n_problem_assets, n_problem_assets)
    previous_weight = np.asarray(previous_weight, dtype=float)
    if (
        previous_weight.shape != (n_problem_assets,)
        or not np.isfinite(previous_weight).all()
        or (previous_weight < 0).any()
        or not np.isclose(previous_weight.sum(), 1.0)
    ):
        raise ValueError("Previous weights must be long-only and sum to one")

    if regularization_target is None:
        regularization_target = np.repeat(1 / n_problem_assets, n_problem_assets)
    regularization_target = np.asarray(regularization_target, dtype=float)
    if (
        regularization_target.shape != (n_problem_assets,)
        or not np.isfinite(regularization_target).all()
        or (regularization_target < 0).any()
        or not np.isclose(regularization_target.sum(), 1.0)
    ):
        raise ValueError("The regularization target must be long-only and sum to one")
    if turnover_limit is not None and (
        not np.isfinite(turnover_limit) or not 0 <= turnover_limit <= 1
    ):
        raise ValueError("Turnover limit must be between zero and one")

    zeta_position = n_problem_assets
    excess_start = zeta_position + 1
    turnover_start = excess_start + n_scenarios
    regularization_start = turnover_start + n_problem_assets
    n_variables = regularization_start + n_problem_assets

    objective = np.zeros(n_variables)
    objective[zeta_position] = 1.0
    objective[excess_start:turnover_start] = 1 / (
        (1 - alpha) * n_scenarios
    )
    objective[turnover_start:regularization_start] = cost_bp / 10_000 / 2
    objective[regularization_start:] = regularization

    matrices = []
    limits = []
    scenario_matrix = np.zeros((n_scenarios, n_variables))
    scenario_matrix[:, :n_problem_assets] = -scenarios
    scenario_matrix[:, zeta_position] = -1.0
    scenario_matrix[:, excess_start:turnover_start] = -np.eye(n_scenarios)
    matrices.append(scenario_matrix)
    limits.append(np.zeros(n_scenarios))

    turnover_matrix = np.zeros((2 * n_problem_assets, n_variables))
    turnover_bound = np.empty(2 * n_problem_assets)
    regularization_matrix = np.zeros((2 * n_problem_assets, n_variables))
    regularization_bound = np.empty(2 * n_problem_assets)
    for i in range(n_problem_assets):
        turnover_matrix[2 * i, i] = 1.0
        turnover_matrix[2 * i, turnover_start + i] = -1.0
        turnover_bound[2 * i] = previous_weight[i]
        turnover_matrix[2 * i + 1, i] = -1.0
        turnover_matrix[2 * i + 1, turnover_start + i] = -1.0
        turnover_bound[2 * i + 1] = -previous_weight[i]

        regularization_matrix[2 * i, i] = 1.0
        regularization_matrix[2 * i, regularization_start + i] = -1.0
        regularization_bound[2 * i] = regularization_target[i]
        regularization_matrix[2 * i + 1, i] = -1.0
        regularization_matrix[2 * i + 1, regularization_start + i] = -1.0
        regularization_bound[2 * i + 1] = -regularization_target[i]
    matrices.extend([turnover_matrix, regularization_matrix])
    limits.extend([turnover_bound, regularization_bound])

    if turnover_limit is not None:
        limit_matrix = np.zeros((1, n_variables))
        limit_matrix[0, turnover_start:regularization_start] = 0.5
        matrices.append(limit_matrix)
        limits.append(np.array([turnover_limit]))

    equality_matrix = np.zeros((1, n_variables))
    equality_matrix[0, :n_problem_assets] = 1.0
    bounds = (
        [(0.0, cap)] * n_problem_assets
        + [(None, None)]
        + [(0.0, None)] * n_scenarios
        + [(0.0, None)] * (2 * n_problem_assets)
    )
    result = linprog(
        objective,
        A_ub=np.vstack(matrices),
        b_ub=np.concatenate(limits),
        A_eq=equality_matrix,
        b_eq=np.array([1.0]),
        bounds=bounds,
        method="highs",
    )
    if not result.success:
        raise RuntimeError(result.message)
    if not np.isfinite(result.fun) or not np.isfinite(result.x).all():
        raise RuntimeError("The solver returned a nonfinite result")

    weight = result.x[:n_problem_assets]
    zeta = float(result.x[zeta_position])
    excess = result.x[excess_start:turnover_start]
    scenario_loss = -scenarios @ weight
    turnover = float(0.5 * np.abs(weight - previous_weight).sum())
    deviation = float(np.abs(weight - regularization_target).sum())
    cvar_component = float(
        zeta + excess.sum() / ((1 - alpha) * n_scenarios)
    )
    estimated_cost = turnover * cost_bp / 10_000
    regularization_penalty = regularization * deviation
    audit = {
        "success": bool(result.success),
        "status": int(result.status),
        "objective": float(result.fun),
        "cvar_component": cvar_component,
        "turnover": turnover,
        "estimated_cost": estimated_cost,
        "regularization_deviation": deviation,
        "regularization_penalty": regularization_penalty,
        "equality_residual": float(abs(weight.sum() - 1.0)),
        "bound_violation": float(
            max(
                np.maximum(-weight, 0).max(),
                np.maximum(weight - cap, 0).max(),
            )
        ),
        "scenario_residual": float(
            np.maximum(scenario_loss - zeta - excess, 0).max()
        ),
        "objective_residual": float(
            abs(result.fun - cvar_component - estimated_cost - regularization_penalty)
        ),
    }
    if turnover_limit is not None:
        audit["turnover_limit_residual"] = float(
            max(turnover - turnover_limit, 0)
        )

    residuals = [
        audit["equality_residual"],
        audit["bound_violation"],
        audit["scenario_residual"],
        audit["objective_residual"],
    ]
    if turnover_limit is not None:
        residuals.append(audit["turnover_limit_residual"])
    if excess.min() < -1e-9 or max(residuals) > 1e-7:
        raise RuntimeError("The solution failed a required audit")
    return weight, zeta, excess, result, audit


scenario_count = 500
scenario_seed = 9001
scenario_rng = np.random.default_rng(scenario_seed)
scenario_indices = scenario_rng.integers(0, len(train), size=scenario_count)
scenarios = train.to_numpy()[scenario_indices]
minimum_cvar_weight, zeta, excess, minimum_cvar_result, minimum_cvar_audit = (
    solve_cvar_portfolio(scenarios)
)
scenario_var, selected_loss_mean, selected_count = empirical_var_cvar(
    scenarios @ minimum_cvar_weight
)

print(pd.Series(minimum_cvar_weight, index=assets, name="weight").round(6))
print("solver message:", minimum_cvar_result.message)
print("zeta and objective:", round(zeta, 6), round(minimum_cvar_audit["objective"], 6))
print(
    "separate empirical diagnostic:",
    round(scenario_var, 6),
    round(selected_loss_mean, 6),
    selected_count,
)
print(pd.Series(minimum_cvar_audit).round(10).to_string())
```

Expected evidence: weights approximately `(0.496398, 0, 0.503602, 0)`, threshold `0.008546`, and objective `0.017503`. The separate empirical diagnostic reports VaR `0.008546`, selected-loss mean `0.016839`, and 27 selected scenarios. HiGHS reports an optimal solution, and every residual is within `1e-7`. The objective and selected-loss mean differ because observations at the threshold and the empirical selection rule do not assign tail probability in the same way.

### Activity 2 — audit one scenario inequality

Find the scenario with the largest loss under `minimum_cvar_weight`. Substitute its returns, weight, $\zeta$, and corresponding value in `excess` into $u_s\geq-r_s^\top w-\zeta$. Preserve the scenario index, both sides, and residual. Do not rely only on the solver's success flag.

## 5. Add turnover, cost, and regularization explicitly

Let $w^0$ be previous weights and $w^R$ be a stated reference. Add nonnegative variables satisfying $d_i\geq|w_i-w_i^0|$ and $q_i\geq|w_i-w_i^R|$. One-way turnover is $\frac12\sum_i d_i$. The next decision imposes

$$
\frac12\sum_i d_i\leq0.12
$$

and minimizes

$$
\text{CVaR expression}
+\frac{4}{10{,}000}\left(\frac12\sum_i d_i\right)
+0.005\sum_iq_i.
$$

The second term estimates the initial trade's cost under a linear 4-basis-point assumption. The third pulls weights toward equal weights; its coefficient is a teaching choice expressed on the same numerical scale as one-period loss, not an estimated market quantity. This objective treats each scenario as one holding-period return and the initial cost as paid at the start of that holding period. A hard limit restricts feasible weights, a cost term changes the objective in financial units, and regularization expresses a preference toward a reference. They are not interchangeable.

```python
practical_weight, _, _, practical_result, practical_audit = (
    solve_cvar_portfolio(
        scenarios,
        previous_weight=equal_weight,
        turnover_limit=0.12,
        cost_bp=4.0,
        regularization=0.005,
        regularization_target=equal_weight,
    )
)
weight_comparison = pd.DataFrame(
    {
        "equal_weight_reference": equal_weight,
        "minimum_cvar": minimum_cvar_weight,
        "with_limit_cost_regularization": practical_weight,
    },
    index=assets,
)
audit_comparison = pd.DataFrame(
    {
        "minimum_cvar": minimum_cvar_audit,
        "with_limit_cost_regularization": practical_audit,
    }
).T
print(weight_comparison.round(6).to_string())
print(
    audit_comparison[
        [
            "objective",
            "cvar_component",
            "turnover",
            "estimated_cost",
            "regularization_deviation",
            "regularization_penalty",
            "turnover_limit_residual",
        ]
    ].round(8).to_string()
)
print("solver message:", practical_result.message)
```

Expected evidence: the modified weights are approximately `(0.25, 0.25, 0.37, 0.13)`. One-way turnover is `0.12`, estimated initial cost is `0.000048`, absolute deviation from equal weights is `0.24`, the regularization term is `0.001200`, the CVaR component is `0.022349`, and the complete objective is `0.023597`. This comparison alone does not identify which term changed the weights; Activity 3 changes one condition at a time.

### Activity 3 — distinguish three changes

Run three additional versions using the same 500 scenarios: only the 12% turnover limit, only the 4-basis-point cost term, and only the `0.005` regularization term. Preserve weights, turnover, CVaR component, estimated cost, regularization deviation, and complete objective. A term may be present without changing the optimizer under the stated coefficient and constraints. Explain why complete objectives from different formulations are not directly comparable performance measures.

## 6. Measure sensitivity to scenario count and seed

The following combinations are declared before their results are examined. Each row draws training observations with replacement and solves the unmodified minimum-CVaR problem.

```python
sensitivity_rows = []
for budget in [100, 300, 1000]:
    for seed in [11, 22, 33, 44, 55]:
        draw_rng = np.random.default_rng(seed)
        draw = train.to_numpy()[
            draw_rng.integers(0, len(train), size=budget)
        ]
        weight, _, _, result, audit = solve_cvar_portfolio(draw)
        sensitivity_rows.append(
            {
                "scenario_count": budget,
                "seed": seed,
                "estimated_cvar": audit["cvar_component"],
                "largest_weight": weight.max(),
                "concentration": np.square(weight).sum(),
                **{
                    f"w_{asset}": weight[i]
                    for i, asset in enumerate(assets)
                },
            }
        )

sensitivity = pd.DataFrame(sensitivity_rows)
weight_columns = [f"w_{asset}" for asset in assets]
dispersion = sensitivity.groupby("scenario_count")[
    ["estimated_cvar", "largest_weight", "concentration"]
].agg(["mean", "std"])
weight_ranges = sensitivity.groupby("scenario_count")[weight_columns].agg(
    lambda values: values.max() - values.min()
)
weight_ranges["sum_of_asset_weight_ranges"] = weight_ranges.sum(axis=1)
print(sensitivity.round(6).to_string(index=False))
print("\ndispersion across five seeds")
print(dispersion.round(6).to_string())
print("\nweight ranges across five seeds")
print(weight_ranges.round(6).to_string())
assert len(sensitivity) == 15
assert np.isfinite(sensitivity.select_dtypes(include="number").to_numpy()).all()
assert np.isfinite(weight_ranges.to_numpy()).all()
```

Expected evidence: 15 finite solutions, five for each scenario count. The standard deviation of estimated CVaR is approximately `0.001490`, `0.001645`, and `0.001855` for 100, 300, and 1,000 scenarios. These five seeds do not show monotonically lower dispersion as count increases. This result is limited to the artificial observations, counts, and seeds.

### Activity 4 — identify a claim the table cannot support

For each scenario count, report the standard deviation of estimated CVaR and the range of every asset weight. Write one sentence the 15 rows support and one they cannot support. A claim about every larger scenario count or future market is outside the evidence.

## 7. Add an explicit stress assumption

The artificial stress vector `(-4%, -7%, -5%, -12%)` is declared before test evaluation and repeated ten times in the 510-row scenario matrix. Repetition gives it more influence than appending it once; it does not establish its real probability. All other settings remain fixed.

```python
stress_return = np.array([[-0.04, -0.07, -0.05, -0.12]])
stressed_scenarios = np.vstack(
    [scenarios, np.repeat(stress_return, 10, axis=0)]
)
stressed_weight, _, _, stressed_result, stressed_audit = (
    solve_cvar_portfolio(
        stressed_scenarios,
        previous_weight=equal_weight,
        turnover_limit=0.12,
        cost_bp=4.0,
        regularization=0.005,
        regularization_target=equal_weight,
    )
)
stress_comparison = pd.DataFrame(
    {
        "without_added_stress": practical_weight,
        "with_added_stress": stressed_weight,
        "change": stressed_weight - practical_weight,
    },
    index=assets,
)
print(stress_comparison.round(6).to_string())
print("stressed solver message:", stressed_result.message)
print(pd.Series(stressed_audit).round(10).to_string())
```

Expected evidence: stressed weights approximately `(0.37, 0.25, 0.25, 0.13)`, compared with `(0.25, 0.25, 0.37, 0.13)` without the added observations. Both use the full 12% turnover allowance. The change is conditional on the invented vector and its ten repetitions.

This is a sensitivity and stress exercise. Formal robust optimization specifies uncertainty about inputs or distributions and optimizes over an explicit set of admissible alternatives, often through a worst-case objective or constraint. Appending one stress vector does not provide that guarantee. State only what the calculation shows about this stress; do not call the portfolio robust in general.

## 8. Compare fixed decisions on one later period

The four targets are fixed before evaluation. Every method begins from the same equal-weight portfolio used as the optimization's previous weights. On the first test date and first observation of each new month, holdings trade to target. One-way turnover is half the absolute change from the current holdings. Each method pays 4 basis points times its own turnover. Terminal liquidation is omitted.

```python
def evaluate_monthly_target(
    sample,
    target_weight,
    previous_weight,
    alpha=0.95,
    cost_bp=4.0,
):
    if not isinstance(sample, pd.DataFrame) or sample.empty:
        raise ValueError("Sample must be a nonempty DataFrame")
    if not isinstance(sample.index, pd.DatetimeIndex):
        raise ValueError("Sample must have a DatetimeIndex")
    if not sample.index.is_monotonic_increasing or sample.index.has_duplicates:
        raise ValueError("Sample dates must be ordered and unique")
    if not np.isfinite(sample.to_numpy()).all() or not sample.gt(-1).all().all():
        raise ValueError("Sample returns must be finite and greater than -1")
    if not np.isfinite(cost_bp) or cost_bp < 0:
        raise ValueError("Cost must be finite and nonnegative")

    target_weight = np.asarray(target_weight, dtype=float)
    previous_weight = np.asarray(previous_weight, dtype=float)
    if (
        target_weight.shape != (sample.shape[1],)
        or not np.isfinite(target_weight).all()
        or not np.isclose(target_weight.sum(), 1.0)
        or target_weight.min() < -1e-10
        or target_weight.max() > 0.60 + 1e-10
    ):
        raise ValueError("Target weights violate alignment or constraints")
    if (
        previous_weight.shape != target_weight.shape
        or not np.isfinite(previous_weight).all()
        or not np.isclose(previous_weight.sum(), 1.0)
        or previous_weight.min() < -1e-10
    ):
        raise ValueError("Previous weights violate alignment or constraints")

    held_weight = previous_weight.copy()
    previous_date = None
    rows = []
    for date, asset_return in sample.iterrows():
        rebalance = previous_date is None or date.month != previous_date.month
        turnover = 0.0
        if rebalance:
            turnover = 0.5 * np.abs(target_weight - held_weight).sum()
            held_weight = target_weight.copy()
        gross_return = float(held_weight @ asset_return.to_numpy())
        cost = turnover * cost_bp / 10_000
        net_return = gross_return - cost
        if 1 + gross_return <= 0 or 1 + net_return <= 0:
            raise ValueError("Portfolio wealth became nonpositive")
        rows.append({"net_return": net_return, "turnover": turnover})
        held_weight = held_weight * (1 + asset_return.to_numpy()) / (1 + gross_return)
        previous_date = date

    path = pd.DataFrame(rows, index=sample.index)
    net_return = path["net_return"].to_numpy()
    var, cvar, tail_count = empirical_var_cvar(net_return, alpha)
    wealth = np.cumprod(1 + net_return)
    running_peak = np.maximum.accumulate(np.r_[1.0, wealth])[1:]
    drawdown = wealth / running_peak - 1
    summary = {
        "observations": len(path),
        "cumulative_net_return": wealth[-1] - 1,
        "annualized_net_volatility": net_return.std(ddof=1) * np.sqrt(252),
        "net_var": var,
        "net_cvar": cvar,
        "tail_count": tail_count,
        "maximum_drawdown": drawdown.min(),
        "total_one_way_turnover": path["turnover"].sum(),
    }
    return path, summary


comparison_weights = {
    "equal_weight": equal_weight,
    "minimum_cvar": minimum_cvar_weight,
    "limit_cost_regularization": practical_weight,
    "added_stress": stressed_weight,
}
evaluation_paths = {}
evaluation_rows = {}
for name, weight in comparison_weights.items():
    path, summary = evaluate_monthly_target(test, weight, equal_weight)
    evaluation_paths[name] = path
    evaluation_rows[name] = summary

common_index = evaluation_paths["equal_weight"].index
assert all(path.index.equals(common_index) for path in evaluation_paths.values())
test_summary = pd.DataFrame(evaluation_rows).T
print(test_summary.round(6).to_string())
```

Expected evidence: 140 identical test observations per method. In printed order, cumulative net returns are approximately `0.055968`, `-0.017350`, `0.016804`, and `0.024037`; annualized net volatilities are `0.108953`, `0.087298`, `0.098834`, and `0.097184`; and net CVaR estimates are `0.013073`, `0.010751`, `0.011739`, and `0.011867`. Total one-way turnovers are approximately `0.072750`, `0.522429`, `0.182100`, and `0.180540`. The optimization uses one-day scenarios, whereas this implementation holds and restores target weights monthly. That horizon difference and the simplified cost deduction, which omits market impact, taxes, financing, and bid–ask dynamics, are limitations. One artificial period does not establish future superiority.

## 9. Required evidence and completion

Submit the hand-sorted loss calculation, artificial-data audit, empirical training estimate, scenario specification, minimum-CVaR weights and complete audit, one hand-checked scenario inequality, three separate implementation-change comparisons, scenario-count and seed table, stress comparison, common net test table, and completed [Week 13 scenario and optimization record](week13_support.md#scenario-and-optimization-record).

The Week 14 report should use this evidence to state the financial decision, loss convention, scenario generator, scenario count, seed, constraints, objective terms, solver checks, sensitivity results, test conditions, and limits of each claim. These records support the [Report 2 criteria](../14/week14_main.md); this lesson does not change the report weights or add another criterion.

Your work is complete when another student can reconstruct every scenario draw, objective term, constraint, and test-period calculation and can distinguish what was observed from what was assumed.

## Exit evidence

```text
Within the stated scenarios, minimum CVaR changed ______.
The turnover limit, cost term, and regularization term affected ______ in different ways.
Changing the scenario count or seed changed ______.
Adding the stated stress observations changed ______.
The common test period showed ______ under the stated accounting.
The evidence does not establish ______.
```
