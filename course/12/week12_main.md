# Week 12 — Covariance Estimation and Risk-Based Weighting

## Core financial question

How do covariance estimates and definitions of risk allocation change portfolio weights, and how can sample covariance, shrinkage, a one-factor estimate, a trailing-window estimate, equal risk contribution, unequal risk budgets, and hierarchical risk parity be compared without confusing different objectives?

Week 11 used one sample covariance in mean–variance calculations and found that weights changed when the estimation window changed. This week studies that dependence directly. Week 13 changes the risk measure from volatility to downside tail loss, so Value at Risk and Conditional Value at Risk are not introduced here. The evidence developed today supports the covariance choice and risk-based benchmarks required for the Week 14 progress report.

## Observable outcome

After completing Week 12, you will construct and diagnose four covariance estimates, verify the decomposition of portfolio volatility, solve equal and unequal risk-budget allocations, construct a hierarchical risk-parity allocation, and compare fixed target weights on one later period under common monthly rebalancing and transaction costs.

## Teaching objectives

After completing this class, you should be able to:

- distinguish sample, fixed diagonal-shrinkage, one-factor, and trailing-window covariance estimates;
- calculate marginal, total, and proportional contributions to portfolio volatility in one asset order;
- construct and audit equal-risk-contribution and unequal-risk-budget portfolios;
- reproduce a hierarchical risk-parity calculation and preserve its clustered asset order; and
- compare covariance and allocation choices under common constraints, test dates, rebalancing, costs, and appropriately limited conclusions.

## Before you begin

Use a clean notebook with `numpy`, `pandas`, and `scipy`. Bring Week 11's sample covariance, solver checks, target-weight drift, monthly rebalancing, and 4-basis-point cost convention. All returns, factors, weights, and results below are artificial. The 35% shrinkage intensity, 126-observation window, single-linkage clustering, risk budgets, 60% cap, and other numerical settings are teaching assumptions.

## 1. Four covariance estimates answer different questions

For weights $w$ and annual covariance matrix $\Sigma$, portfolio volatility is

$$
\sigma_p=\sqrt{w^\top\Sigma w}.
$$

The sample covariance $S$ uses every observation in the stated training period. It is easy to reproduce but changes with the sample. This class also calculates the fixed diagonal-shrinkage matrix

$$
\Sigma_\delta=(1-\delta)S+\delta\mathrm{diag}(S),
\qquad \delta=0.35.
$$

This teaching calculation reduces off-diagonal covariances while keeping sample variances. The fixed intensity and diagonal target do not reproduce the statistically estimated intensity or target in Ledoit and Wolf.

The artificial one-factor estimate is

$$
\Sigma_F=\beta\beta^\top\sigma_f^2+D,
$$

where $f$ is the equally weighted cross-sectional return in the training data, $\beta$ is estimated by ordinary least squares with an intercept, and $D$ contains residual variances on its diagonal. Setting residual covariances to zero is a model assumption, not an observed fact.

Finally, a trailing-window covariance uses only the most recent $L$ observations available at a decision date. Repeating that calculation at later decisions creates a rolling sequence of estimates. The window length changes both information and sampling variation; a shorter window is not automatically more current and better.

## 2. Create artificial returns and diagnose covariance estimates

Run every code cell in order in a clean notebook.

```python
import numpy as np
import pandas as pd
from scipy.cluster.hierarchy import leaves_list, linkage
from scipy.optimize import minimize
from scipy.spatial.distance import squareform

rng = np.random.default_rng(121)
assets = ["A", "B", "C", "D", "E", "F"]
n_assets = len(assets)
n_observations = 420

market = rng.normal(0.00025, 0.010, n_observations)
sector_1 = rng.normal(0.0, 0.006, n_observations)
sector_2 = rng.normal(0.0, 0.007, n_observations)
idiosyncratic = rng.normal(
    0.0,
    [0.006, 0.008, 0.007, 0.009, 0.006, 0.010],
    (n_observations, n_assets),
)
return_matrix = (
    market[:, None] * np.array([0.7, 1.0, 0.8, 1.1, 0.6, 1.2])
    + sector_1[:, None] * np.array([0.8, 0.7, 0.5, 0.0, 0.0, 0.0])
    + sector_2[:, None] * np.array([0.0, 0.0, 0.0, 0.7, 0.9, 0.6])
    + idiosyncratic
)
returns = pd.DataFrame(
    return_matrix,
    columns=assets,
    index=pd.bdate_range("2024-01-02", periods=n_observations),
)
train = returns.iloc[:300].copy()
test = returns.iloc[300:].copy()

print("train/test shapes:", train.shape, test.shape)
print("training dates:", train.index.min().date(), "through", train.index.max().date())
print("test dates:", test.index.min().date(), "through", test.index.max().date())
print("missing values:", int(returns.isna().sum().sum()))

assert train.shape == (300, 6) and test.shape == (120, 6)
assert train.index.max() < test.index.min()
assert not returns.index.has_duplicates
assert returns.index.is_monotonic_increasing
assert returns.gt(-1).all().all()
```

Expected evidence: training dates from `2024-01-02` through `2025-02-24`, test dates from `2025-02-25` through `2025-08-11`, shapes `(300, 6)` and `(120, 6)`, and zero missing values. If a result differs, inspect the seed, row split, artificial factor shapes, and column order. The artificial business-day index does not remove exchange holidays.

```python
sample_cov = train.cov().to_numpy() * 252

delta = 0.35
shrink_cov = (1 - delta) * sample_cov + delta * np.diag(np.diag(sample_cov))

factor = train.mean(axis=1).to_numpy()
factor_design = np.column_stack([np.ones(len(factor)), factor])
factor_coefficients = np.linalg.lstsq(
    factor_design, train.to_numpy(), rcond=None
)[0]
factor_residuals = train.to_numpy() - factor_design @ factor_coefficients
factor_cov = (
    np.outer(factor_coefficients[1], factor_coefficients[1])
    * np.var(factor, ddof=1)
    * 252
    + np.diag(np.var(factor_residuals, axis=0, ddof=1) * 252)
)

trailing_window = 126
trailing_cov = train.iloc[-trailing_window:].cov().to_numpy() * 252

covariances = {
    "sample": sample_cov,
    "diagonal_shrinkage": shrink_cov,
    "one_factor": factor_cov,
    "trailing_126": trailing_cov,
}
covariance_diagnostics = pd.DataFrame(
    {
        name: {
            "smallest_eigenvalue": np.linalg.eigvalsh(cov).min(),
            "condition_number": np.linalg.cond(cov),
            "mean_variance": np.diag(cov).mean(),
        }
        for name, cov in covariances.items()
    }
).T
print(covariance_diagnostics.round(6).to_string())

for name, cov in covariances.items():
    assert cov.shape == (n_assets, n_assets), name
    assert np.isfinite(cov).all(), name
    assert np.allclose(cov, cov.T), name
    assert np.linalg.eigvalsh(cov).min() > 0, name
assert np.allclose(np.diag(shrink_cov), np.diag(sample_cov))

rolling_rows = []
for end_position in [199, 249, 299]:
    window = train.iloc[end_position - trailing_window + 1:end_position + 1]
    rolling_cov = window.cov().to_numpy() * 252
    rolling_rows.append(
        {
            "decision_date": train.index[end_position],
            "window_start": window.index.min(),
            "observations": len(window),
            "condition_number": np.linalg.cond(rolling_cov),
            "smallest_eigenvalue": np.linalg.eigvalsh(rolling_cov).min(),
        }
    )
rolling_diagnostics = pd.DataFrame(rolling_rows)
print("\nrolling 126-observation diagnostics")
print(rolling_diagnostics.round(6).to_string(index=False))
```

Expected evidence: four finite, symmetric, positive-definite covariance matrices. Their condition numbers are approximately `12.9079`, `6.3549`, `11.8221`, and `12.3771` in printed order. The three rolling decisions use 126 observations and end on `2024-10-07`, `2024-12-16`, and `2025-02-24`; their condition numbers differ. A lower condition number is not sufficient evidence of a better financial estimate. If a check fails, inspect annualization, missing values, factor dimensions, window endpoints, and asset order before calculating weights.

### Activity 1 — change shrinkage without claiming improvement

Before calculation, predict how changing `delta` from `0.35` to `0.70` affects off-diagonal covariances, the diagonal, and the condition number. Recalculate the matrix, verify the prediction, and preserve both diagnostics. Explain which dependence information was reduced. Do not call the changed estimate better using only its condition number.

## 3. Decompose portfolio volatility

The marginal contribution of asset $i$ to portfolio volatility and its total contribution are

$$
MC_i=\frac{(\Sigma w)_i}{\sigma_p},
\qquad
RC_i=w_iMC_i.
$$

They satisfy $\sum_iRC_i=\sigma_p$. The proportional contribution is $RC_i/\sigma_p$, and all proportional contributions sum to one.

### Worked example — two unequal risk shares

Use target weights `(0.50, 0.50)` and annual covariance

$$
\Sigma=\begin{bmatrix}0.040&0.006\\0.006&0.090\end{bmatrix}.
$$

Then $\Sigma w=(0.023,0.048)^\top$, portfolio variance is `0.0355`, and volatility is approximately `0.1884`. Marginal contributions are approximately `(0.1221, 0.2548)`, total contributions are `(0.0610, 0.1274)`, and proportional contributions are `(0.3239, 0.6761)`. Equal capital therefore does not imply equal contributions to volatility.

```python
def risk_contributions(weight, cov):
    weight = np.asarray(weight, dtype=float)
    cov = np.asarray(cov, dtype=float)
    if weight.ndim != 1 or weight.size == 0 or cov.shape != (len(weight), len(weight)):
        raise ValueError("Weight and covariance dimensions do not match")
    if not np.isfinite(weight).all() or not np.isfinite(cov).all():
        raise ValueError("Weight and covariance inputs must be finite")
    if not np.allclose(cov, cov.T, atol=1e-10):
        raise ValueError("Covariance must be symmetric")
    if np.linalg.eigvalsh(cov).min() < -1e-10:
        raise ValueError("Covariance must be positive semidefinite")
    variance = float(weight @ cov @ weight)
    if variance <= 0:
        raise ValueError("Portfolio variance must be positive")

    volatility = np.sqrt(variance)
    marginal = cov @ weight / volatility
    contribution = weight * marginal
    proportion = contribution / volatility
    return volatility, marginal, contribution, proportion


equal_weight = np.repeat(1 / n_assets, n_assets)
equal_vol, equal_marginal, equal_contribution, equal_proportion = (
    risk_contributions(equal_weight, sample_cov)
)
print("equal-weight annualized volatility:", round(equal_vol, 6))
print(pd.Series(equal_proportion, index=assets, name="risk_share").round(6))

assert np.isclose(equal_contribution.sum(), equal_vol)
assert np.isclose(equal_proportion.sum(), 1.0)
```

Expected evidence: equal-weight annualized volatility about `0.152547` and risk shares `(0.122307, 0.182668, 0.162026, 0.204349, 0.123826, 0.204824)`. The shares sum to one but are unequal. If an identity fails, first inspect the asset order, covariance units, matrix multiplication, and distinction between an absolute contribution and its proportion.

### Activity 2 — independently verify another weight vector

Use the two-asset covariance in the worked example with weights `(0.60, 0.40)`. Predict whether the two proportional contributions move closer together, calculate all four stages, and revise your explanation. Preserve the identity checks. If the shares do not sum to one, first recalculate portfolio variance and $\Sigma w$.

## 4. Solve equal and unequal risk budgets

A risk-budget portfolio chooses positive weights so that proportional contribution $RC_i/\sigma_p$ matches a prespecified positive budget $b_i$, where $\sum_i b_i=1$. Equal risk contribution uses $b_i=1/N$. It is a special case of risk budgeting, not a different risk measure.

The teaching solver minimizes squared differences between calculated shares and budgets under full investment, positive weights, and a 60% cap. It records solver and constraint checks. A successful result only establishes agreement with the stated sample covariance and tolerance.

```python
def solve_risk_budget(cov, budget, cap=0.60):
    cov = np.asarray(cov, dtype=float)
    budget = np.asarray(budget, dtype=float)
    if cov.ndim != 2 or cov.shape[0] != cov.shape[1]:
        raise ValueError("Covariance must be square")
    n = cov.shape[0]
    if budget.shape != (n,) or not np.isfinite(budget).all() or (budget <= 0).any():
        raise ValueError("Budgets must be a positive finite vector in asset order")
    if not np.isclose(budget.sum(), 1.0):
        raise ValueError("Budgets must sum to one")
    if not np.isfinite(cov).all() or not np.allclose(cov, cov.T, atol=1e-10):
        raise ValueError("Covariance must be finite and symmetric")
    if np.linalg.eigvalsh(cov).min() <= 0:
        raise ValueError("Covariance must be positive definite for this solver")
    if not np.isfinite(cap) or not 0 < cap <= 1 or cap * n < 1 - 1e-12:
        raise ValueError("Cap is invalid or infeasible")

    def objective(weight):
        proportion = risk_contributions(weight, cov)[3]
        return np.square(proportion - budget).sum()

    result = minimize(
        objective,
        np.repeat(1 / n, n),
        method="SLSQP",
        bounds=[(1e-8, cap)] * n,
        constraints=[{"type": "eq", "fun": lambda w: w.sum() - 1.0}],
        options={"ftol": 1e-14, "maxiter": 3000},
    )
    if not result.success:
        raise RuntimeError(result.message)

    weight = result.x
    proportion = risk_contributions(weight, cov)[3]
    equality_residual = abs(weight.sum() - 1.0)
    bound_violation = max(
        float(np.maximum(-weight, 0).max()),
        float(np.maximum(weight - cap, 0).max()),
    )
    budget_residual = float(np.max(np.abs(proportion - budget)))
    if max(equality_residual, bound_violation, budget_residual) > 1e-5:
        raise RuntimeError("Risk-budget or portfolio constraint failed")

    audit = {
        "success": bool(result.success),
        "status": int(result.status),
        "objective": float(result.fun),
        "equality_residual": equality_residual,
        "bound_violation": bound_violation,
        "maximum_budget_residual": budget_residual,
    }
    return weight, proportion, result, audit


equal_budget = np.repeat(1 / n_assets, n_assets)
unequal_budget = np.array([0.30, 0.25, 0.20, 0.15, 0.07, 0.03])

erc_weight, erc_share, erc_result, erc_audit = solve_risk_budget(
    sample_cov, equal_budget
)
budget_weight, budget_share, budget_result, budget_audit = solve_risk_budget(
    sample_cov, unequal_budget
)

risk_budget_table = pd.DataFrame(
    {
        "equal_weight": equal_weight,
        "erc_weight": erc_weight,
        "erc_risk_share": erc_share,
        "unequal_budget": unequal_budget,
        "unequal_budget_weight": budget_weight,
        "unequal_budget_risk_share": budget_share,
    },
    index=assets,
)
audits = pd.DataFrame({"erc": erc_audit, "unequal_budget": budget_audit}).T
print(risk_budget_table.round(6).to_string())
print("\nsolver messages:", erc_result.message, "|", budget_result.message)
print("\naudits")
print(audits.round(10).to_string())
```

Expected evidence: equal-risk-contribution weights approximately `(0.210130, 0.146506, 0.163916, 0.133770, 0.211377, 0.134300)` and risk shares of `1/6`. The unequal-budget weights are approximately `(0.332312, 0.205208, 0.187273, 0.133221, 0.111865, 0.030122)`, and their shares match `(0.30, 0.25, 0.20, 0.15, 0.07, 0.03)` within `1e-5`. Both solvers report `success=True`, status `0`, successful termination, and passed residuals. If a check fails, inspect budget order, positivity, covariance eigenvalues, cap feasibility, and unrounded residuals before changing tolerances.

### Activity 3 — change risk budgets

Before solving, predict how changing the budgets to `(0.25, 0.25, 0.20, 0.15, 0.10, 0.05)` affects weights for assets `A`, `E`, and `F`. Use the same sample covariance and constraints, preserve the new solver audit and risk shares, and revise your explanation. Do not interpret a larger budget as the same thing as a larger capital weight.

## 5. Construct hierarchical risk-parity weights

The class implementation converts correlation to distance $d_{ij}=\sqrt{(1-\rho_{ij})/2}$, applies single-linkage hierarchical clustering, records the leaf order, and recursively allocates between adjacent groups using inverse-variance subportfolios. Hierarchical risk parity does not target equal individual risk contributions. The 60% cap is verified after allocation; if it fails, the code stops rather than silently altering the hierarchy.

```python
def cluster_variance(cov, indices):
    sub_cov = cov[np.ix_(indices, indices)]
    diagonal = np.diag(sub_cov)
    if not np.isfinite(sub_cov).all() or (diagonal <= 0).any():
        raise ValueError("Cluster covariance must be finite with positive variances")
    inverse_variance = 1 / diagonal
    sub_weight = inverse_variance / inverse_variance.sum()
    return float(sub_weight @ sub_cov @ sub_weight)


def hrp_weights(cov, cap=0.60):
    cov = np.asarray(cov, dtype=float)
    if cov.ndim != 2 or cov.shape[0] != cov.shape[1] or not np.isfinite(cov).all():
        raise ValueError("Covariance must be a finite square matrix")
    if not np.allclose(cov, cov.T, atol=1e-10) or (np.diag(cov) <= 0).any():
        raise ValueError("Covariance must be symmetric with positive variances")
    if np.linalg.eigvalsh(cov).min() < -1e-10:
        raise ValueError("Covariance must be positive semidefinite")
    if not np.isfinite(cap) or not 0 < cap <= 1:
        raise ValueError("Cap must be finite and between zero and one")

    volatility = np.sqrt(np.diag(cov))
    correlation = np.clip(cov / np.outer(volatility, volatility), -1.0, 1.0)
    distance = np.sqrt(np.maximum((1 - correlation) / 2, 0))
    np.fill_diagonal(distance, 0.0)
    tree = linkage(squareform(distance, checks=True), method="single")
    order = leaves_list(tree).tolist()

    weight = pd.Series(1.0, index=order)
    clusters = [order]
    while clusters:
        next_clusters = []
        for cluster in clusters:
            if len(cluster) <= 1:
                continue
            split = len(cluster) // 2
            left = cluster[:split]
            right = cluster[split:]
            left_variance = cluster_variance(cov, left)
            right_variance = cluster_variance(cov, right)
            allocation_left = 1 - left_variance / (left_variance + right_variance)
            weight.loc[left] *= allocation_left
            weight.loc[right] *= 1 - allocation_left
            next_clusters.extend([left, right])
        clusters = next_clusters

    weight = weight.sort_index().to_numpy()
    if not np.isclose(weight.sum(), 1.0) or weight.min() < 0:
        raise RuntimeError("Hierarchical weights violate full investment or long-only rules")
    if weight.max() > cap + 1e-10:
        raise RuntimeError("Hierarchical weights exceed the stated cap")
    return weight, order, tree


hrp_weight, clustered_order, linkage_matrix = hrp_weights(sample_cov)
hrp_share = risk_contributions(hrp_weight, sample_cov)[3]
print("clustered asset order:", [assets[index] for index in clustered_order])
print(
    pd.DataFrame(
        {"hrp_weight": hrp_weight, "hrp_risk_share": hrp_share},
        index=assets,
    ).round(6).to_string()
)
assert np.isclose(hrp_share.sum(), 1.0)
```

Expected evidence: clustered order `A, B, C, F, D, E`; weights approximately `(0.300647, 0.105081, 0.129066, 0.105087, 0.207680, 0.152440)`; finite risk shares summing to one; and no weight above 60%. If a result differs, inspect the distance matrix, SciPy version, linkage method, clustered order, recursive split, and restoration to the original asset order.

### Activity 4 — compare what is being equalized

For equal weights, equal risk contribution, unequal risk budgets, and hierarchical risk parity, state what is fixed or targeted and what is not. Use the output to show that equal capital, equal individual risk contribution, unequal risk budgets, and recursive group allocation are different decisions. Preserve one constraint or identity check for every method.

## 6. Use common later-period evidence

All four target vectors below are fixed before the test results are inspected. On the first test date and the first business-day observation of each new month, holdings trade to target. Initial movement from cash has turnover 1; later one-way turnover is half the absolute change from drifted holdings. Every method pays 4 basis points times its own turnover, and terminal liquidation is omitted. This simplified accounting subtracts the cost directly from the portfolio return; it does not model bid–ask dynamics, market impact, taxes, or financing.

```python
def evaluate_fixed_target(sample, target_weight, cost_bp=4.0, cap=0.60):
    if not isinstance(sample, pd.DataFrame) or sample.empty:
        raise ValueError("Sample must be a nonempty DataFrame")
    if not isinstance(sample.index, pd.DatetimeIndex):
        raise ValueError("Sample must have a DatetimeIndex")
    if not sample.index.is_monotonic_increasing or sample.index.has_duplicates:
        raise ValueError("Sample dates must be ordered and unique")
    if not np.isfinite(sample.to_numpy()).all() or not sample.gt(-1).all().all():
        raise ValueError("Sample returns must be finite and greater than -1")
    if not np.isfinite(cost_bp) or cost_bp < 0:
        raise ValueError("Cost in basis points must be finite and nonnegative")
    if not np.isfinite(cap) or not 0 < cap <= 1:
        raise ValueError("Cap must be finite and between zero and one")
    target_weight = np.asarray(target_weight, dtype=float)
    if target_weight.shape != (sample.shape[1],) or not np.isfinite(target_weight).all():
        raise ValueError("Target weight is not aligned with the sample")
    if (
        not np.isclose(target_weight.sum(), 1.0)
        or target_weight.min() < -1e-10
        or target_weight.max() > cap + 1e-10
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


method_weights = {
    "equal_weight": equal_weight,
    "erc_sample": erc_weight,
    "risk_budget_sample": budget_weight,
    "hrp_sample": hrp_weight,
}
evaluations = {
    method: evaluate_fixed_target(test, weight)
    for method, weight in method_weights.items()
}
common_index = evaluations["equal_weight"].index
assert all(result.index.equals(common_index) for result in evaluations.values())

test_cov = test.cov().to_numpy() * 252
test_rows = []
for method, result in evaluations.items():
    target_weight = method_weights[method]
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
            "test_risk_share_dispersion": risk_contributions(
                target_weight, test_cov
            )[3].std(ddof=0),
        }
    )

test_summary = pd.DataFrame(test_rows).set_index("method")
print(test_summary.round(6).to_string())
assert np.isfinite(test_summary.to_numpy()).all()
```

Expected evidence: four rows with 120 identical test observations. In printed order, annualized net returns are approximately `-0.353823`, `-0.334883`, `-0.282425`, and `-0.323871`; annualized net volatilities are `0.145923`, `0.140453`, `0.139414`, and `0.139867`; and turnover totals are `1.089355`, `1.086239`, `1.072486`, and `1.081555`. The test risk-share dispersion uses the full test covariance and initial target weights, so it is a hindsight diagnostic, not information available at the initial decision. No single test period establishes future superiority. If results differ, first inspect common dates, asset order, weight constraints, monthly decisions, and each method's own turnover.

## 7. Compare equal-risk-contribution weights across covariance estimates

The first three covariance estimates use all 300 training rows but impose different structures. The trailing estimate deliberately uses only the last 126 training rows. The following table changes the covariance input while holding risk budgets, asset order, solver, and constraints fixed.

```python
erc_by_covariance = {
    name: solve_risk_budget(cov, equal_budget)[0]
    for name, cov in covariances.items()
}
covariance_weight_table = pd.DataFrame(erc_by_covariance, index=assets)

sample_erc = covariance_weight_table["sample"].to_numpy()
sensitivity_rows = []
for name in covariance_weight_table:
    absolute_change = np.abs(
        covariance_weight_table[name].to_numpy() - sample_erc
    )
    sensitivity_rows.append(
        {
            "covariance": name,
            "total_absolute_change_from_sample": absolute_change.sum(),
            "largest_asset_change": absolute_change.max(),
            "asset_with_largest_change": assets[int(absolute_change.argmax())],
        }
    )
sensitivity = pd.DataFrame(sensitivity_rows).set_index("covariance")

print("equal-risk-contribution weights")
print(covariance_weight_table.round(6).to_string())
print("\nweight sensitivity")
print(sensitivity.round(6).to_string())
assert np.isfinite(covariance_weight_table.to_numpy()).all()
```

Expected evidence: total absolute changes from sample weights of approximately `0.009281`, `0.013003`, and `0.034523` for diagonal shrinkage, one factor, and trailing 126 observations. Asset `E` has the largest individual change in each comparison. These numbers show dependence on the stated estimates; they do not prove which estimate is closer to an unknown population covariance.

### Activity 5 — change a trailing window before using test results

Predict how replacing 126 training observations with 63 affects the condition number and equal-risk-contribution weights at `2025-02-24`. Use only rows available through that date, preserve the window boundaries and weight changes, and revise your explanation. Do not use the test-period performance to choose between the two windows and continue calling that period unused.

## 8. Required evidence and completion

Submit the data and covariance audit, changed-shrinkage activity, rolling-window record, worked and independent contribution calculations, equal and unequal risk-budget tables and solver audits, hierarchical order and allocation, comparison of method meanings, common net test-period table, covariance-sensitivity table, trailing-window activity, and completed [Week 12 risk-based method record](week12_support.md#risk-based-method-record).

This evidence supports the covariance choice, mathematical definition, constraints, baseline comparison, implementation checks, and initial results required by the [Report 2 criteria](../14/week14_main.md). This lesson does not change the report weights or add another criterion.

Your work is complete when another student can reconstruct every covariance estimate and allocation, verify contribution identities and constraints, reproduce the common test comparison, and state which conclusions depend on the covariance model, window, risk budget, clustering, and artificial period.

## Exit evidence

```text
Changing the covariance estimate changed ______.
Equal capital weights did/did not create equal risk contributions because ______.
The unequal risk budget changed ______ but did not directly specify ______.
The hierarchical method used the clustered order ______.
Under the stated artificial test conditions, ______.
The evidence does not establish ______.
```
