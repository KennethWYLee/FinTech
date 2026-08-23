# Week 13 — Support

This file provides preparation, a reproducibility record, required checks, troubleshooting, optional extensions, and references for [Week 13 main](week13_main.md).

## Before class

Use a clean notebook with `numpy`, `pandas`, and `scipy`. Confirm that SciPy provides the HiGHS linear-programming method:

```python
import numpy as np
import pandas as pd
import scipy
from scipy.optimize import linprog

check = linprog([1.0], bounds=[(0.0, None)], method="highs")
print("numpy", np.__version__)
print("pandas", pd.__version__)
print("scipy", scipy.__version__)
print("HiGHS available:", check.success)
print("HiGHS message:", check.message)
```

Expected evidence: three version strings, `HiGHS available: True`, and an optimal-status message. Preserve the complete status and message if the check fails and repair the environment before class.

## Scenario and optimization record

Complete this record for every retained formulation. Do not overwrite one formulation's assumptions with another's.

```text
Financial decision and holding period:
Training dates and observations:
Test dates, observations, and whether results had been inspected when the method was fixed:
Assets and column order:
Return frequency:
Loss sign:
Confidence level:
Empirical quantile and tail-membership conventions:
Scenario-generation method and replacement rule:
Scenario count:
Random seed:
Stress returns, repetitions, and rationale:
Previous weights:
Full-investment, long-only, and cap constraints:
Turnover definition and hard limit:
Transaction-cost assumption and objective term:
Regularization target and coefficient:
Complete optimization objective:
Scenario horizon and evaluation holding period:
Solver method, success, status, and message:
Equality, bound, scenario, objective, and turnover residuals:
Threshold, excess losses, CVaR component, and complete objective:
Common test rebalancing and cost rules:
Common test-period measures:
Scenario-count, seed, and stress sensitivity:
Limits of the evidence:
Files and output preserved:
```

## Required diagnostic checks

- Define loss with an explicit sign before calculating a quantile.
- State the empirical quantile method and the rule for observations equal to VaR.
- Keep scenario columns, weights, previous weights, reference weights, and labels in the same asset order.
- Verify scenario shape, finite values, count, seed, and sampling with replacement.
- Check full investment, bounds, nonnegative excess variables, and every scenario inequality.
- Preserve solver success, status, message, and residuals; do not convert an unsuccessful result into a portfolio.
- Calculate one-way turnover as half the absolute weight change and state which previous portfolio it uses.
- Separate the CVaR component, estimated transaction cost, regularization term, and complete objective.
- State whether the scenario horizon matches the evaluation holding and rebalancing periods.
- Check the hard turnover limit using unrounded weights.
- Prespecify scenario counts, seeds, and stress assumptions before comparing their results.
- Freeze every target before inspecting the common test period. Use identical dates, monthly decisions, and cost rules.
- Distinguish scenario CVaR, empirical training estimates, and net test-period estimates.
- Describe an appended stress scenario as a sensitivity calculation, not a general robustness guarantee.

## Troubleshooting

| Symptom | First check | Action |
|---|---|---|
| VaR or CVaR has an unexpected sign | Return-to-loss conversion | Use one sign convention in formulas, code, tables, and prose |
| Selected tail is empty | Finite losses, confidence level, quantile method, and comparison operator | Repair the inputs instead of replacing tail loss with zero |
| Linear program is infeasible | Cap times asset count, turnover limit, previous weights, and full-investment equality | Diagnose which assumptions conflict before changing the solver |
| A scenario inequality fails | Variable order and the largest residual | Rebuild that matrix row directly from the written formula |
| Objective and selected-loss mean differ | Threshold ties and empirical tail-membership rule | Preserve and label both quantities instead of forcing equality |
| Turnover exceeds the limit | Factor of one-half and unrounded weights | Recalculate absolute changes from the recorded previous portfolio |
| Estimated cost has the wrong scale | Basis-point conversion and one-way turnover | Verify that 4 basis points equals `4 / 10_000` |
| Regularization has no visible effect | Coefficient, target order, and whether the unregularized solution already equals the target | Report the result; do not raise the coefficient after seeing test performance |
| Results change on rerun | Scenario count and random seed | Reproduce every prespecified combination and retain all rows |
| Stress conclusion is too broad | Invented return vector and repetition count | Limit the statement to the explicit scenario matrix |
| Test data influenced a setting | Decision timeline | Use a new untouched period or label the result as development evidence |

## Optional extensions

- Compare ordinary row bootstrap with a block bootstrap and state which dependence the blocks are intended to retain.
- Change the confidence level over a prespecified set and compare thresholds, selected counts, and weights.
- Add a minimum scenario-mean-return constraint and diagnose feasibility.
- Use a non-equal reference portfolio for absolute-deviation regularization and justify it before test evaluation.
- Formulate an explicit uncertainty set and worst-case objective, then explain how that problem differs from appending stress rows.

Optional work must not replace any required main-material evidence.

## References and source boundaries

- Rockafellar, R. T., & Uryasev, S. (2000). [Optimization of Conditional Value-at-Risk](https://sites.math.washington.edu/~rtr/papers/rtr179-CVaR1.pdf). *Journal of Risk, 2*(3), 21–41. DOI: `10.21314/JOR.2000.038`.
- Lobo, M. S., Fazel, M., & Boyd, S. (2007). [Portfolio optimization with linear and fixed transaction costs](https://stanford.edu/~boyd/papers/portfolio.html). *Annals of Operations Research, 152*(1), 341–365.
- Caccioli, F., Kondor, I., Marsili, M., & Still, S. (2014). [`L_p` regularized portfolio optimization](https://arxiv.org/abs/1404.4040). arXiv:1404.4040.
- Calafiore, G. C. (2007). [Ambiguous risk measures and optimal robust portfolios](https://doi.org/10.1137/060654803). *SIAM Journal on Optimization, 18*(3), 853–877.
- SciPy. [`scipy.optimize.linprog`](https://docs.scipy.org/doc/scipy/reference/generated/scipy.optimize.linprog.html), official API documentation.
- NumPy. [`numpy.quantile`](https://numpy.org/doc/stable/reference/generated/numpy.quantile.html), official API documentation.

Rockafellar and Uryasev support the finite-scenario CVaR formulation. The other papers establish that transaction costs, norm regularization, and formal treatment of model uncertainty are recognized portfolio-optimization topics. The class's particular absolute-deviation target, coefficients, artificial scenarios, and stress repetitions are teaching choices and do not reproduce those papers' complete models or empirical findings. Software documentation supports the implemented functions, not the financial assumptions or claims.
