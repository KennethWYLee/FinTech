# Week 11 — Support

This file provides preparation, a reproduction record, required checks, troubleshooting, optional extensions, and sources for [Week 11 main](week11_main.md).

## Before class

Use a clean notebook environment with Python, `numpy`, `pandas`, and `scipy`. Run this check and preserve either the output or the full error message:

```python
import numpy as np
import pandas as pd
import scipy
from scipy.optimize import minimize

print("numpy", np.__version__)
print("pandas", pd.__version__)
print("scipy", scipy.__version__)
```

Week 11 assumes that you can maintain a chronological information boundary and can distinguish target weights, drifted holdings, turnover, cost, and net return. No classroom package version, real dataset, or report parameter has yet been approved.

## Portfolio-method record

```text
Method:
Financial decision and objective:
Decision date:
Training observations and date range:
Test observations and date range:
Asset order:
Return frequency and annualization factor:
Expected-return estimate and units:
Covariance estimate and units:
Risk-free rate and units, if used:
Variance penalty and scaling, if used:
Target return and units, if used:
Full-investment constraint:
Lower and upper bounds:
Additional constraints:
Solver, method, tolerance, and maximum iterations:
Solver success, status, and message:
Objective value:
Maximum equality, bound, and target violations:
Estimated return, volatility, and Sharpe ratio:
Concentration:
Rebalance, turnover, and cost rules:
Test-period measures:
Estimation-window comparison:
Known limitations and failure conditions:
Files and outputs preserved:
```

## Required checks

- Keep the same asset order in returns, expected returns, covariance, weights, and labels.
- Verify that expected returns and covariance are finite and use one annual scale.
- Verify that covariance is square, symmetric, and has no materially negative eigenvalue.
- Preserve the smallest eigenvalue and condition number rather than silently replacing the matrix.
- State the objective, risk-free rate, variance penalty, target, and cap before using test results.
- Preserve solver success, status, message, objective value, and unrounded constraint residuals.
- Reject an infeasible target explicitly; do not silently lower it.
- Verify full investment, long-only weights, and every upper bound before calculating returns.
- Apply every target to the same test dates, monthly schedule, and cost rate.
- Calculate each method's turnover against its own drifted holdings.
- Report concentration and estimation-window changes with financial results.
- Do not select a setting with the stated test period and still call that period unused.

## Troubleshooting

| Symptom | First check | Safe next action |
|---|---|---|
| `ModuleNotFoundError: scipy` | Confirm the active notebook environment | Install SciPy in that environment, restart if required, and rerun from the first cell |
| Matrix multiplication has a shape error | Print the asset labels and every array shape | Restore one common asset order before optimization |
| Covariance check fails | Inspect missing values, symmetry, annualization, and eigenvalues | Diagnose the estimate before passing it to the solver |
| Solver reports failure | Preserve status and message; inspect feasibility and starting values | Correct the stated problem before changing tolerances |
| Weights do not sum to one | Print unrounded weights and equality residual | Stop before evaluation and inspect the constraint |
| A weight exceeds the cap | Print the bound violation and supplied `cap` | Correct the bounds and rerun every solver check |
| Frontier target fails | Compare it with the maximum return feasible under the cap | Record infeasibility rather than treating it as a portfolio |
| Maximum-Sharpe output is extreme | Check expected returns, covariance, annual risk-free rate, units, and bounds | Report sensitivity; do not infer future performance from the optimized ratio |
| Methods have different test rows | Compare their indices before aggregation | Restore the common chronological boundary |
| Costs are unexpectedly equal | Inspect each method's drifted holdings and turnover | Recalculate turnover independently for every method |
| Test result is used to choose a penalty | Inspect the decision log and period labels | Move selection to training or validation data and reserve new evidence |

## Optional extensions

- Compare a prespecified grid of variance penalties using training estimates only.
- Change the annual risk-free rate and explain its effect on the maximum-Sharpe solution.
- Replace the 60% cap with 40% and calculate the new maximum feasible return.
- Use several feasible starting values and compare solver solutions and objective values.
- Add a turnover penalty relative to an existing holding and state its units.
- Reserve a validation period between training and test before choosing any setting.

## Financial and software sources

- Markowitz, H. (1952). [Portfolio Selection](https://doi.org/10.1111/j.1540-6261.1952.tb01525.x). *The Journal of Finance, 7*(1), 77–91.
- Sharpe, W. F. (1994). [The Sharpe Ratio](https://doi.org/10.3905/jpm.1994.409501). *The Journal of Portfolio Management, 21*(1), 49–58.
- Chopra, V. K., & Ziemba, W. T. (1993). [The Effect of Errors in Means, Variances, and Covariances on Optimal Portfolio Choice](https://doi.org/10.3905/jpm.1993.409440). *The Journal of Portfolio Management, 19*(2), 6–11.
- [`scipy.optimize.minimize` with SLSQP](https://docs.scipy.org/doc/scipy/reference/optimize.minimize-slsqp.html) documents the constrained numerical method used in class.
- [`numpy.linalg.cond`](https://numpy.org/doc/stable/reference/generated/numpy.linalg.cond.html) documents the matrix condition-number calculation.

Markowitz supports the mean–variance relationship and diversification problem; Sharpe supports the excess-return-to-variability measure; and Chopra and Ziemba examine portfolio sensitivity to estimation errors. The class uses its own artificial data, constraints, parameter values, solver checks, rebalancing, and cost calculation. It does not reproduce any paper's complete model, data, experiment, or empirical claims. Software documentation defines numerical behavior but does not validate the financial assumptions.
