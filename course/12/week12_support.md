# Week 12 — Support

This file provides preparation, a reproducibility record, required checks, troubleshooting, optional extensions, and references for [Week 12 main](week12_main.md).

## Before class

Use a clean notebook environment with `numpy`, `pandas`, and `scipy`. Week 12 builds on Week 11 covariance matrices, constrained optimization, solver checks, and chronological testing. Run this preparation cell before class:

```python
import numpy as np
import pandas as pd
import scipy
from scipy.cluster.hierarchy import leaves_list, linkage
from scipy.optimize import minimize
from scipy.spatial.distance import squareform

print("numpy", np.__version__)
print("pandas", pd.__version__)
print("scipy", scipy.__version__)
```

Expected evidence: three version strings and no import error. If an import fails, preserve the full error message and repair the environment before running the portfolio examples.

## Risk-based method record

Complete one record for every covariance-and-allocation combination that you retain.

```text
Method and purpose:
Training dates and number of observations:
Test dates, number of observations, and whether results had been inspected when weights were fixed:
Asset order:
Return frequency and annualization:
Covariance estimate and parameters:
Trailing-window length and endpoints, if used:
Factor definition and residual-covariance assumption, if used:
Smallest eigenvalue and condition number:
Risk budget and definition of contribution:
Clustering distance, linkage, and leaf order, if used:
Objective and solver:
Solver success, status, message, and objective value:
Equality, bound, and maximum budget residuals:
Weight cap and other constraints:
Weight-sum and bound checks:
Portfolio volatility and contribution-identity checks:
Rebalancing, execution, turnover, and cost rules:
Common test dates and measures:
Sensitivity comparison:
Known limitations:
Evidence preserved:
```

## Required diagnostic checks

- Keep the same asset order in returns, covariance matrices, weights, budgets, contributions, and labels.
- Verify covariance shape, finite values, symmetry, diagonal values, and eigenvalues before allocation.
- Record every trailing window's start, end, and observation count. A rolling estimate must end no later than its decision date.
- State the factor definition and the assumption made about residual covariances for a factor estimate.
- Distinguish marginal contributions, total contributions, and proportional contributions. Check that total contributions sum to portfolio volatility and proportions sum to one.
- Check that the risk-budget vector is positive, ordered like the assets, and sums to one. Verify the resulting maximum budget residual without relying on rounded output.
- Preserve solver success, status, message, objective, equality residual, bound violation, and maximum budget residual.
- Preserve the hierarchical leaf order, distance formula, linkage rule, recursive allocation, and restoration to the original asset order.
- Verify full investment, long-only bounds, and the 60% cap for every target vector.
- Freeze target weights before inspecting the test results. Compare methods on identical dates, rebalancing rules, execution assumptions, turnover definitions, and cost rates.
- Label any calculation that uses the full test covariance as a hindsight diagnostic; do not present it as information available at the initial decision.

## Troubleshooting

| Symptom | First check | Action |
|---|---|---|
| Covariance is not symmetric or finite | Missing values, array shape, and asset order | Repair the input and reconstruct the matrix before allocation |
| A trailing estimate uses the wrong rows | Window endpoint and slice length | Print the start, end, and observation count at every decision |
| Factor covariance differs | Factor definition, intercept, annualization, and residual degrees of freedom | Reproduce those assumptions before comparing results |
| Contribution calculation divides by zero | Portfolio variance and all input values | Stop and repair the covariance or weights; do not replace the result silently |
| Contribution proportions do not sum to one | Formula, covariance units, and asset order | Recalculate variance, marginal contributions, and total contributions in sequence |
| Risk-budget solver rejects the input | Budget length, positivity, order, sum, covariance eigenvalues, and cap feasibility | Correct the invalid input before changing solver settings |
| Solver reports success but budgets do not match | Unrounded maximum budget residual | Treat the solution as failed when the stated tolerance is exceeded |
| `squareform` rejects the distance matrix | Symmetry, diagonal zeros, and correlations outside `[-1, 1]` | Recreate the correlation and distance matrices using the documented checks |
| Hierarchical weights have the wrong labels | Integer leaf order and final index restoration | Restore weights to the original asset order before adding asset names |
| Hierarchical allocation breaches the cap | Uncapped recursive result | Stop and disclose the incompatibility; do not silently clip and renormalize |
| One method appears superior | Common dates, each method's turnover, costs, and whether earlier test results influenced the method | Limit the conclusion and state when the weights were fixed |

## Optional extensions

- Evaluate a grid of fixed shrinkage intensities and plot the condition number and total weight change without choosing from test performance.
- Compare 63-, 126-, and 252-observation trailing estimates at the same decision date.
- Replace single linkage with complete linkage and document the changed tree, leaf order, and allocation.
- Change the positive risk-budget vector and verify that contribution proportions, not capital weights, match it.
- Repeat the fixed-target evaluation over additional artificial periods declared before results are inspected.

Optional work must not replace any required evidence in the main material.

## References and source boundaries

- Ledoit, O., & Wolf, M. (2004). [A well-conditioned estimator for large-dimensional covariance matrices](https://doi.org/10.1016/S0047-259X%2803%2900096-4). *Journal of Multivariate Analysis, 88*(2), 365–411.
- Maillard, S., Roncalli, T., & Teïletche, J. (2010). [The properties of equally weighted risk contribution portfolios](https://doi.org/10.3905/jpm.2010.36.4.060). *Journal of Portfolio Management, 36*(4), 60–70.
- Bruder, B., & Roncalli, T. (2012). [Managing risk exposures using the risk budgeting approach](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=2009778). SSRN working paper.
- López de Prado, M. (2016). [Building diversified portfolios that outperform out of sample](https://doi.org/10.3905/jpm.2016.42.4.059). *Journal of Portfolio Management, 42*(4), 59–69.
- SciPy. [`scipy.cluster.hierarchy.linkage`](https://docs.scipy.org/doc/scipy/reference/generated/scipy.cluster.hierarchy.linkage.html), official API documentation.

The fixed diagonal-shrinkage calculation is not the Ledoit–Wolf estimator. The one-factor estimate, risk-budget solver, and hierarchical allocation are transparent teaching implementations. The artificial data and results do not reproduce the cited papers' datasets, complete methods, or empirical findings. The sources support the established concepts; they do not establish that any method is best for the artificial test period or future data.
