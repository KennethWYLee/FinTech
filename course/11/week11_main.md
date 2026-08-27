# Week 11 — Mean–Variance Portfolio Weighting

## Core financial question

How do estimated expected returns and covariances change the portfolio weights selected by global-minimum-variance, mean–variance, maximum-Sharpe, and efficient-frontier calculations, and what evidence is needed before those weights can be trusted?

Week 8 supplied equal, market-capitalization, signal-based, and inverse-volatility benchmarks. This week introduces optimization and solver auditing. Week 12 will examine alternative covariance estimates and risk-based allocations, so this week keeps the sample covariance fixed except for one required estimation-window comparison. The resulting evidence begins the method comparison required for the Week 14 progress report.

## Observable outcome

By the end of this 150-minute class, you will estimate annual expected returns and a covariance matrix from an artificial training period, solve three constrained portfolio problems, trace an efficient frontier, and apply frozen target weights to a later test period under one common monthly rebalancing and cost rule. You will preserve input units, solver status, constraint residuals, concentration, and weight sensitivity.

## Teaching objectives

After completing this class, you should be able to:

- explain how expected returns, variances, covariances, and a risk-free rate enter different portfolio objectives;
- distinguish global-minimum-variance, mean–variance, maximum-Sharpe, and efficient-frontier portfolios;
- solve and audit a constrained portfolio problem, including an infeasible target-return case;
- compare in-sample estimated risk with common later-period financial evidence; and
- show how a changed estimation window can alter optimized weights and appropriately limit the conclusion.

## Before you begin

Use a clean notebook with `numpy`, `pandas`, and `scipy`. Bring Week 8's distinction among target weights, drifted holdings, turnover, and net returns. All returns and results below are artificial. The 60% cap, annual risk-free rate of 2%, variance penalty of 10, monthly rebalance, and 4-basis-point cost are teaching assumptions, not recommended settings.

## 150-minute class plan

| Time | Focus | Required evidence |
|---:|---|---|
| 0–10 minutes | From basic weights to optimization | Input-and-objective comparison |
| 10–30 minutes | Portfolio variance and covariance | Worked calculation and independent exercise |
| 30–45 minutes | Three objectives, frontier, and units | Complete mathematical specification |
| 45–65 minutes | Artificial data, split, and estimates | Data and matrix audit |
| 65–95 minutes | Solve and audit four allocations | Weights, estimates, solver messages, and residuals |
| 95–115 minutes | Efficient frontier and infeasible target | Frontier table and explicit failure |
| 115–135 minutes | Common later-period evaluation | Net evidence under common conditions |
| 135–145 minutes | Estimation-window sensitivity | Weight-change comparison and interpretation |
| 145–150 minutes | Exit evidence | Supported claim and unresolved limitation |

## 1. Covariance changes portfolio risk

Let $r_t\in\mathbb{R}^N$ be the asset-return vector in period $t$, and let $w\in\mathbb{R}^N$ be target weights selected before that return. Then

$$
r_{p,t}=w^\top r_t,
\qquad
\mu_p=w^\top\mu,
\qquad
\sigma_p^2=w^\top\Sigma w,
$$

where $\mu=\mathbb{E}[r_t]$ and $\Sigma=\operatorname{Cov}(r_t)$. Covariance records how two asset returns vary together. A covariance estimated from a finite sample is uncertain; it is an input to a stated decision, not a fixed law of the market.

### Worked example — two artificial assets

Suppose $w=(0.6,0.4)^\top$, annual volatilities are 20% and 30%, and their correlation is 0.25. Annual portfolio variance is

$$
\begin{aligned}
\sigma_p^2
&=0.6^2(0.20)^2+0.4^2(0.30)^2
+2(0.6)(0.4)(0.25)(0.20)(0.30)\\
&=0.0144+0.0144+0.0072=0.0360.
\end{aligned}
$$

Portfolio volatility is $\sqrt{0.0360}\approx18.97\%$. If correlation rises to 0.80 while every other input stays fixed, the covariance term rises, variance becomes `0.05184`, and volatility becomes approximately `22.77%`. This is a conditional calculation, not a forecast that correlation will rise.

### Activity 1 — predict, calculate, and revise

Use weights `(0.40, 0.60)`, annual volatilities `(0.15, 0.25)`, and correlation `0.10`. Predict the direction of the volatility change if correlation becomes `0.70`, calculate both portfolio volatilities, and revise your explanation after checking the arithmetic. Preserve each variance term and its unit. If the result moves in the wrong direction, first inspect the factor of 2 and confirm that volatility is the square root of variance.

## 2. Define the portfolio problems and units

Every calculation below imposes full investment, long-only weights, no leverage, and a 60% position cap:

$$
\mathbf{1}^\top w=1,
\qquad 0\leq w_i\leq0.60.
$$

The global-minimum-variance portfolio solves

$$
\min_w\;w^\top\Sigma w.
$$

It uses the covariance estimate but not the expected-return estimate. The mean–variance portfolio solves

$$
\max_w\;w^\top\mu-\lambda w^\top\Sigma w,
$$

where $\lambda>0$ controls the variance penalty. Both $\mu$ and $\Sigma$ are annualized in the code, and $\lambda=10$ is fixed before the test period. Changing from decimal returns to percentage returns without adjusting the objective would change the solution.

With annual risk-free rate $r_f=0.02$, the maximum-Sharpe portfolio in the stated feasible set solves

$$
\max_w\;\frac{w^\top\mu-r_f}{\sqrt{w^\top\Sigma w}}.
$$

This is an in-sample ratio of estimated excess return to estimated volatility. It does not guarantee the largest realized Sharpe ratio.

Finally, an efficient-frontier point minimizes $w^\top\Sigma w$ subject to the same constraints and an additional requirement $w^\top\mu\geq\mu^*$. A target above the greatest feasible estimated return is not a portfolio; it is an infeasible constraint.

## 3. Create and audit an artificial training/test sample

Run all code cells in order in a clean notebook.

```python
import numpy as np
import pandas as pd
from scipy.optimize import minimize

rng = np.random.default_rng(111)
assets = ["A", "B", "C", "D", "E"]
annual_mean = np.array([0.06, 0.08, 0.05, 0.10, 0.04])
annual_vol = np.array([0.14, 0.20, 0.11, 0.26, 0.09])
correlation = np.array(
    [
        [1.00, 0.55, 0.35, 0.40, 0.20],
        [0.55, 1.00, 0.25, 0.60, 0.15],
        [0.35, 0.25, 1.00, 0.20, 0.45],
        [0.40, 0.60, 0.20, 1.00, 0.10],
        [0.20, 0.15, 0.45, 0.10, 1.00],
    ]
)
annual_cov = np.outer(annual_vol, annual_vol) * correlation
returns = pd.DataFrame(
    rng.multivariate_normal(annual_mean / 252, annual_cov / 252, size=500),
    columns=assets,
    index=pd.bdate_range("2024-01-02", periods=500),
)
train = returns.iloc[:350].copy()
test = returns.iloc[350:].copy()

print("full/train/test shapes:", returns.shape, train.shape, test.shape)
print("training dates:", train.index.min().date(), "through", train.index.max().date())
print("test dates:", test.index.min().date(), "through", test.index.max().date())
print("missing values:", int(returns.isna().sum().sum()))

assert returns.shape == (500, 5)
assert train.shape == (350, 5) and test.shape == (150, 5)
assert train.index.max() < test.index.min()
assert not returns.index.has_duplicates
assert returns.index.is_monotonic_increasing
assert returns.gt(-1).all().all()
```

Expected evidence: shapes `(500, 5)`, `(350, 5)`, and `(150, 5)`; training dates from `2024-01-02` through `2025-05-05`; test dates from `2025-05-06` through `2025-12-01`; and zero missing values. If a result differs, first inspect the seed, date construction, row slicing, and column order. This artificial business-day index does not remove exchange holidays.

```python
mu_hat = train.mean() * 252
cov_hat = train.cov() * 252
eigenvalues = np.linalg.eigvalsh(cov_hat)

print("annualized mean estimate")
print(mu_hat.round(4))
print("smallest eigenvalue:", round(float(eigenvalues.min()), 8))
print("condition number:", round(float(np.linalg.cond(cov_hat)), 2))

assert np.isfinite(mu_hat).all()
assert np.isfinite(cov_hat).all().all()
assert np.allclose(cov_hat, cov_hat.T)
assert eigenvalues.min() >= -1e-10
```

Expected evidence: annualized mean estimates `A=0.2000`, `B=0.2033`, `C=0.0138`, `D=0.0334`, and `E=-0.0027`, a positive smallest eigenvalue, and condition number about `17.40`. These estimates differ from the artificial population inputs because they come from one finite sample. A condition number is a numerical diagnostic; no single cutoff proves that a financial estimate is valid or invalid. If a check fails, inspect missing values, annualization, symmetry, and asset order before optimization.

## 4. Solve and audit the allocations

The first helper finds the greatest estimated return permitted by the cap. The second solves one stated objective and returns the weights, the complete SciPy result, and explicit constraint residuals. Solver success is necessary but does not validate the financial inputs.

```python
def maximum_return_with_cap(mu, cap=0.60):
    mu = np.asarray(mu, dtype=float)
    if mu.ndim != 1 or mu.size == 0 or not np.isfinite(mu).all():
        raise ValueError("Expected returns must be a nonempty finite vector")
    if not np.isfinite(cap) or not 0 < cap <= 1 or cap * len(mu) < 1 - 1e-12:
        raise ValueError("Cap is invalid or infeasible")

    weight = np.zeros(len(mu))
    remaining = 1.0
    for index in np.argsort(mu)[::-1]:
        weight[index] = min(cap, remaining)
        remaining -= weight[index]
        if remaining <= 1e-12:
            break
    if remaining > 1e-10:
        raise RuntimeError("Maximum-return allocation is incomplete")
    return float(weight @ mu), weight


def solve_weights(
    mu,
    cov,
    method="gmv",
    risk_penalty=10.0,
    risk_free_rate=0.02,
    target=None,
    cap=0.60,
):
    mu = np.asarray(mu, dtype=float)
    cov = np.asarray(cov, dtype=float)
    n = len(mu)
    if mu.ndim != 1 or n == 0 or cov.shape != (n, n):
        raise ValueError("Expected returns and covariance dimensions do not match")
    if not np.isfinite(mu).all() or not np.isfinite(cov).all():
        raise ValueError("Expected returns and covariance must be finite")
    if not np.allclose(cov, cov.T, atol=1e-10):
        raise ValueError("Covariance must be symmetric")
    if np.linalg.eigvalsh(cov).min() < -1e-10:
        raise ValueError("Covariance must be positive semidefinite")
    if not np.isfinite(cap) or not 0 < cap <= 1 or cap * n < 1 - 1e-12:
        raise ValueError("Cap is invalid or infeasible")
    if method == "mean_variance" and (not np.isfinite(risk_penalty) or risk_penalty <= 0):
        raise ValueError("Risk penalty must be positive and finite")
    if not np.isfinite(risk_free_rate):
        raise ValueError("Risk-free rate must be finite")
    if target is not None and not np.isfinite(target):
        raise ValueError("Target return must be finite")
    if method == "max_sharpe" and np.linalg.eigvalsh(cov).max() <= 0:
        raise ValueError("Maximum-Sharpe volatility must be positive")

    maximum_return, _ = maximum_return_with_cap(mu, cap)
    if target is not None and target > maximum_return + 1e-10:
        raise ValueError(
            f"Target exceeds maximum feasible return {maximum_return:.6f}"
        )

    constraints = [{"type": "eq", "fun": lambda w: w.sum() - 1.0}]
    if target is not None:
        constraints.append({"type": "ineq", "fun": lambda w: w @ mu - target})

    if method == "gmv":
        objective = lambda w: w @ cov @ w
    elif method == "mean_variance":
        objective = lambda w: -(w @ mu - risk_penalty * (w @ cov @ w))
    elif method == "max_sharpe":
        def objective(w):
            variance = w @ cov @ w
            if variance <= 0:
                return 1e12
            return -(w @ mu - risk_free_rate) / np.sqrt(variance)
    else:
        raise ValueError("Method must be 'gmv', 'mean_variance', or 'max_sharpe'")

    result = minimize(
        objective,
        np.repeat(1 / n, n),
        method="SLSQP",
        bounds=[(0.0, cap)] * n,
        constraints=constraints,
        options={"ftol": 1e-12, "maxiter": 2000},
    )
    if not result.success:
        raise RuntimeError(result.message)

    weight = result.x
    equality_residual = abs(weight.sum() - 1.0)
    bound_violation = max(
        float(np.maximum(-weight, 0).max()),
        float(np.maximum(weight - cap, 0).max()),
    )
    target_violation = (
        0.0 if target is None else max(0.0, float(target - weight @ mu))
    )
    if max(equality_residual, bound_violation, target_violation) > 1e-7:
        raise RuntimeError("A portfolio constraint failed its tolerance check")

    audit = {
        "success": bool(result.success),
        "status": int(result.status),
        "objective": float(result.fun),
        "equality_residual": equality_residual,
        "bound_violation": bound_violation,
        "target_violation": target_violation,
    }
    return weight, result, audit


mu_array = mu_hat.to_numpy()
cov_array = cov_hat.to_numpy()
gmv_w, gmv_result, gmv_audit = solve_weights(mu_array, cov_array, "gmv")
mv_w, mv_result, mv_audit = solve_weights(mu_array, cov_array, "mean_variance")
ms_w, ms_result, ms_audit = solve_weights(mu_array, cov_array, "max_sharpe")

weights = pd.DataFrame(
    {
        "equal_weight": 0.20,
        "gmv": gmv_w,
        "mean_variance": mv_w,
        "max_sharpe": ms_w,
    },
    index=assets,
)

estimated_rows = []
for method, weight in weights.items():
    estimated_return = float(weight @ mu_array)
    estimated_volatility = float(np.sqrt(weight @ cov_array @ weight))
    estimated_rows.append(
        {
            "method": method,
            "estimated_return": estimated_return,
            "estimated_volatility": estimated_volatility,
            "estimated_sharpe": (estimated_return - 0.02) / estimated_volatility,
            "concentration": float(np.square(weight).sum()),
        }
    )
estimated = pd.DataFrame(estimated_rows).set_index("method")

audits = pd.DataFrame(
    {"gmv": gmv_audit, "mean_variance": mv_audit, "max_sharpe": ms_audit}
).T
print("weights")
print(weights.round(4).to_string())
print("\nin-sample estimates")
print(estimated.round(4).to_string())
print("\nsolver messages")
print(gmv_result.message, "|", mv_result.message, "|", ms_result.message)
print("\nconstraint audit")
print(audits.round(10).to_string())

assert np.isfinite(weights.to_numpy()).all()
assert np.allclose(weights.sum(axis=0), 1.0)
assert weights.ge(-1e-10).all().all()
assert weights.le(0.60 + 1e-10).all().all()
```

Expected evidence: the global-minimum-variance weights are approximately `(0.1559, 0, 0.1987, 0.0454, 0.6000)`; mean–variance weights are `(0.6000, 0.1408, 0, 0, 0.2592)`; and maximum-Sharpe weights are `(0.6000, 0.4000, 0, 0, 0)`. All columns sum to one and all optimized solutions report `success=True`, status `0`, and successful termination. Equality and bound violations are no larger than `1e-7`. The estimated volatilities are approximately `0.1129`, `0.0793`, `0.1082`, and `0.1409` in printed method order. A successful local numerical solution is not proof of a global optimum or valid financial inputs. If a number differs materially, first inspect the annualization, asset order, objective, risk-free rate, penalty, cap, solver message, and unrounded residuals.

### Activity 2 — explain bounds and objective differences

Identify every binding 60% upper bound and every zero weight. Explain each using only the stated objective, estimates, and constraints. Compare the mean–variance and maximum-Sharpe columns and explain why their common emphasis on estimated return does not make their objectives identical. Preserve the estimated return, volatility, Sharpe ratio, and concentration for both columns.

## 5. Trace an efficient frontier and reject an infeasible target

```python
maximum_feasible_return, maximum_return_weight = maximum_return_with_cap(
    mu_array, cap=0.60
)
gmv_estimated_return = float(gmv_w @ mu_array)
targets = np.linspace(gmv_estimated_return, maximum_feasible_return * 0.995, 8)

frontier_rows = []
for target in targets:
    weight, result, audit = solve_weights(
        mu_array, cov_array, method="gmv", target=float(target)
    )
    frontier_rows.append(
        {
            "target": target,
            "estimated_return": weight @ mu_array,
            "estimated_volatility": np.sqrt(weight @ cov_array @ weight),
            "concentration": np.square(weight).sum(),
            "equality_residual": audit["equality_residual"],
            "target_violation": audit["target_violation"],
        }
    )

frontier = pd.DataFrame(frontier_rows)
print(frontier.round(6).to_string(index=False))
print("maximum feasible estimated return:", round(maximum_feasible_return, 6))

try:
    solve_weights(
        mu_array,
        cov_array,
        method="gmv",
        target=maximum_feasible_return + 0.001,
    )
except ValueError as error:
    print("expected infeasible target:", error)
else:
    raise AssertionError("An infeasible target should not produce a portfolio")

gmv_volatility = float(np.sqrt(gmv_w @ cov_array @ gmv_w))
assert frontier["estimated_volatility"].ge(gmv_volatility - 1e-7).all()
assert frontier["estimated_return"].ge(frontier["target"] - 1e-7).all()
```

Expected evidence: eight feasible rows beginning near estimated return `0.033785` and volatility `0.079349`, with estimated volatility never below the global minimum under the same constraints. The maximum feasible estimated return is approximately `0.201961`. A target of approximately `0.202961` prints an explicit infeasibility message. If a frontier row fails, first compare the target with the cap-constrained maximum and inspect residuals before changing solver tolerances.

### Activity 3 — interpret two frontier points

Choose two feasible rows, predict which will have higher estimated volatility, and then compare estimated return, volatility, and concentration. State that both are training-period calculations. Preserve your prediction and revision. Do not use the later test period to select the target and continue to describe that period as unused.

## 6. Apply common later-period rebalancing and costs

All four target vectors are fixed before the test results are inspected; three use training estimates and equal weights do not. On the first test date and the first business-day observation of each new month, holdings are traded to their fixed target. Between those dates, holdings drift. Initial investment from cash is recorded as turnover of 1; later one-way turnover is half the absolute asset-weight change. Every method pays 4 basis points times its own turnover. Terminal liquidation is omitted for all methods.

```python
def evaluate_fixed_target(sample, target_weight, cost_bp=4.0):
    target_weight = np.asarray(target_weight, dtype=float)
    if target_weight.shape != (sample.shape[1],) or not np.isfinite(target_weight).all():
        raise ValueError("Target weight is not aligned with the sample")
    if (
        not np.isclose(target_weight.sum(), 1.0)
        or target_weight.min() < -1e-10
        or target_weight.max() > 0.60 + 1e-10
    ):
        raise ValueError("Target weight violates the portfolio constraints")

    held = np.zeros(len(target_weight))
    previous_date = None
    rows = []
    for date, asset_return in sample.iterrows():
        rebalance = previous_date is None or date.month != previous_date.month
        turnover = 0.0
        if rebalance:
            turnover = (
                1.0
                if previous_date is None
                else 0.5 * np.abs(target_weight - held).sum()
            )
            held = target_weight.copy()

        gross_return = float(held @ asset_return.to_numpy())
        cost = turnover * cost_bp / 10_000
        net_return = gross_return - cost
        if 1 + gross_return <= 0 or 1 + net_return <= 0:
            raise ValueError("Portfolio wealth became nonpositive")
        rows.append(
            {
                "net_return": net_return,
                "turnover": turnover,
                "pre_return_concentration": float(np.square(held).sum()),
            }
        )
        held = held * (1 + asset_return.to_numpy()) / (1 + gross_return)
        previous_date = date

    return pd.DataFrame(rows, index=sample.index)


evaluations = {
    method: evaluate_fixed_target(test, weight.to_numpy())
    for method, weight in weights.items()
}
common_index = evaluations["equal_weight"].index
assert all(result.index.equals(common_index) for result in evaluations.values())

test_rows = []
for method, result in evaluations.items():
    test_rows.append(
        {
            "method": method,
            "observations": len(result),
            "annualized_net_return": (1 + result["net_return"]).prod()
            ** (252 / len(result))
            - 1,
            "annualized_net_volatility": result["net_return"].std(ddof=1)
            * np.sqrt(252),
            "total_one_way_turnover": result["turnover"].sum(),
            "mean_concentration": result["pre_return_concentration"].mean(),
        }
    )

test_summary = pd.DataFrame(test_rows).set_index("method")
print(test_summary.round(4).to_string())
assert np.isfinite(test_summary.to_numpy()).all()
```

Expected evidence: four rows with 150 identical test observations. In printed order, annualized net returns are approximately `-0.0772`, `-0.0252`, `-0.0598`, and `-0.1085`; annualized net volatilities are `0.1097`, `0.0754`, `0.1058`, and `0.1418`; and total one-way turnover is `1.0865`, `1.0625`, `1.0612`, and `1.0421`. These results support statements only about this seed, artificial path, test interval, targets, monthly rule, and 4-basis-point cost. If dates or counts differ, inspect the common index before comparing values. If costs differ unexpectedly, inspect each method's separately calculated turnover.

## 7. Show estimation-window sensitivity

The following comparison changes only the training observations. It does not use the test returns to re-estimate a portfolio.

```python
short_train = train.iloc[-175:]
short_mu = short_train.mean().to_numpy() * 252
short_cov = short_train.cov().to_numpy() * 252

full_weights = {"gmv": gmv_w, "mean_variance": mv_w, "max_sharpe": ms_w}
short_weights = {
    method: solve_weights(short_mu, short_cov, method=method)[0]
    for method in full_weights
}

stability_rows = []
for method in full_weights:
    absolute_change = np.abs(full_weights[method] - short_weights[method])
    stability_rows.append(
        {
            "method": method,
            "total_absolute_change": absolute_change.sum(),
            "largest_asset_change": absolute_change.max(),
            "asset_with_largest_change": assets[int(absolute_change.argmax())],
        }
    )

stability = pd.DataFrame(stability_rows).set_index("method")
print(stability.round(4).to_string())
assert np.isfinite(stability[["total_absolute_change", "largest_asset_change"]]).all().all()
```

Expected evidence: total absolute changes of approximately `0.0269`, `1.0242`, and `0.8144` for global minimum variance, mean–variance, and maximum Sharpe. The largest individual change is in asset `A` for global minimum variance, asset `D` for mean–variance, and asset `A` for maximum Sharpe. This comparison shows sensitivity to these two training windows; it does not identify the cause, estimate a population error, or prove one method is generally more stable.

### Activity 4 — change the penalty without using test results

Before solving, predict how changing the mean–variance penalty from 10 to 20 will affect estimated volatility and the allocation to the highest estimated-return assets. Use only `train`, preserve the new weights and constraint audit, and revise your prediction. Do not choose between penalties using `test`; that would reuse the stated test period for method selection.

## 8. Required evidence and completion

Submit the two-asset worked calculation and Activity 1, the data and covariance audit, all four weight columns, in-sample estimates, solver messages and residuals, frontier and infeasible-target output, common net test-period comparison, estimation-window sensitivity table, penalty activity, and completed [Week 11 portfolio-method record](week11_support.md#portfolio-method-record).

This evidence supports the Week 14 progress report's mathematical definition, baseline comparison, implementation checks, and initial evidence. Detailed report grading criteria remain pending and are not created here.

Your work is complete when another student can reproduce each portfolio, distinguish its inputs and objective, verify every constraint, separate training from test evidence, and identify one observed source of weight sensitivity.

## Exit evidence

```text
The objective changes the selected weights because ______.
The solver audit establishes ______ but does not establish ______.
Under the stated artificial test conditions, ______.
Changing the estimation window showed ______.
The next covariance or risk-allocation question I would test is ______.
```
