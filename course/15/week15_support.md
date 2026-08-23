# Week 15 — Support

This file provides preparation, a record of the submitted version, required checks, troubleshooting, optional extensions, and sources for [Week 15 main](week15_main.md). It does not add a grading rubric.

## Before class

Bring the exact Week 14 code, results, questions, and proposed revisions. Confirm that all nonpublic data remain outside `course/` and that no credential, personal information, restricted data, article file, or other nonpublic course material appears in an export. Run this clean-environment check:

```python
import numpy as np
import pandas as pd

print("numpy", np.__version__)
print("pandas", pd.__version__)
```

Expected evidence: two version strings and no import error. Preserve the complete error if preparation fails.

## Record the submitted version

Complete every applicable field. Use “not applicable” with a reason rather than silently omitting a required decision.

```text
Team and project title:
Frozen version identifier:
Freeze date and time with time zone:
Files included:
Environment and package versions:
Data source, access date, redistribution status, and checksum or exact query:
Assets, fields, adjustments, frequency, and dates:
Development evaluation dates:
Final-period dates and confirmation that results were not inspected before settings stopped changing:
Method wording exactly as used in the report:
Inputs and their financial meaning:
Formula, objective, or allocation rule:
Fixed settings and constraints:
Failure and fallback behavior:
Decision, execution, holding, and rebalancing timing:
Previous holdings or cash assumption at evaluation start:
Turnover and transaction-cost rules:
Baseline methods and common conditions:
Evaluation measures and units:
Random seeds and repetitions:
Solver, tolerances, status, messages, residuals, and failed runs:
Development sensitivity settings and all retained results:
Final-period result:
Main supported claim:
Claims explicitly not made:
Known limitations:
Independent rerun performed by:
Rerun result, date, and first difference if any:
```

## Required checks

### Data and timing

- Record origin, access or generation, fields, adjustments, dates, frequency, missing-value decisions, and redistribution status.
- Show that every input window ends before the return it is used to earn.
- Use only final-period metadata and prespecified data-quality checks until the proposed function, settings, baselines, accounting, and measures stop changing.
- Disclose any use of final-period values in method selection, parameter choice, code repair, or claim revision.
- State whether later realized final-period observations enter later rolling estimates; this is different from using future observations.

### Method and implementation

- Make the prose, equation, function, settings, and report table describe the same method.
- Preserve asset order from data through weights and reported labels.
- Test shape, labels, finite values, full investment, bounds, and method-specific properties.
- Test invalid and infeasible inputs; do not silently clip an invalid solution and continue.
- Preserve solver, status, message, tolerances, residuals, and failures when optimization is used.
- Verify the cap allocation with an extreme input, not only ordinary weights.

### Common evaluation

- Use identical dates, initial holdings, rebalancing, costs, constraints, and measures for the proposal and baselines.
- Keep gross return, cost, and net return separate.
- Calculate one-way turnover from target and current drifted holdings.
- Report net performance with volatility, maximum drawdown, turnover, cost, and concentration.
- Run sensitivity settings on development data before the final period is revealed.
- Retain an unfavorable or contradictory result and narrow the claim when necessary.

### Freeze and reproduction

- Make every table and figure traceable to a cell or script in the frozen version.
- Make the identifier point to the exact report, code, settings, and results.
- Rerun from the first cell in a clean environment and record the first difference.
- Scan the public package for credentials, private paths, personal information, answers, and restricted files.
- Do not revise the submitted method after final evaluation without disclosing that the period has become development evidence.

## Troubleshooting

| Symptom | First check | Action |
|---|---|---|
| Another team member obtains different output | File versions, package versions, seed, data checksum, and execution order | Identify the first different value and rerun after reconciling that cause |
| Proposed method equals a baseline | Weights at several decision dates and the written formula | Explain the identity or implement the documented difference before final evaluation |
| Weight validation fails | Raw output, labels, asset order, and solver message | Repair the method; do not hide the failure with clipping or renormalization |
| Cap allocation does not terminate | Feasibility and redistribution logic | Fix weights that exceed the cap, redistribute only the remaining budget, and test extreme raw inputs |
| A decision uses the current return | Window endpoint and evaluation row | End history on the preceding observation and rerun every affected result |
| Costs appear too large | Basis-point conversion, one-way turnover, and initial holdings | Hand-calculate one trade and compare every ledger field |
| Higher cost produces higher net return | Whether targets or turnover changed across the cost comparison | Hold the path fixed and change only the cost rate |
| Sensitivity rows use different dates | Evaluation-start rule and available lookback | Use a common start with sufficient earlier observations |
| Final performance changed after a revision | Whether final values informed the change | Disclose reuse and obtain another period for a fresh out-of-sample claim |
| A sensitivity or final result contradicts the claim | Common conditions and calculation correctness | Preserve the result and narrow the claim |
| Report and notebook differ | Generating cell and version identifier | Regenerate the artifact and update every dependent statement |

## Independent rerun record

```text
Reviewer:
Frozen identifier checked:
Clean execution started:
Clean execution completed:
Files or steps missing:
First differing output:
Constraint and information-timing checks observed:
Tables matched to report:
Issues returned to the team:
Final rerun result:
```

## Optional extensions

- Automate weight, timing, cost, and constraint checks for every decision.
- Build a small table mapping each report claim to a frozen output.
- Add computation time as a separate measure rather than treating it as financial performance.
- Reuse a covariance or scenario sensitivity already introduced in Weeks 12–13, provided it is declared before final evaluation.
- Write one command or notebook sequence that rebuilds every submitted table from approved inputs.

Optional work must not replace required evidence.

## Sources and scope

- Arnott, R., Harvey, C. R., & Markowitz, H. (2019). [A Backtesting Protocol in the Era of Machine Learning](https://doi.org/10.3905/jfds.2019.1.064). *The Journal of Financial Data Science, 1*(1), 64–74.
- Bailey, D. H., Borwein, J. M., López de Prado, M., & Zhu, Q. J. (2016). [The Probability of Backtest Overfitting](https://www.davidhbailey.com/dhbpapers/backtest-prob.pdf). *The Journal of Computational Finance, 20*(4), 39–69. DOI: `10.21314/JCF.2016.322`.
- Patton, A. J., & Weller, B. M. (2020). [What You See Is Not What You Get: The Costs of Trading Market Anomalies](https://doi.org/10.1016/j.jfineco.2020.02.012). *Journal of Financial Economics, 137*(2), 515–549.
- White, H. (2000). [A Reality Check for Data Snooping](https://doi.org/10.1111/1468-0262.00152). *Econometrica, 68*(5), 1097–1126.
- NumPy. [Random sampling documentation](https://numpy.org/doc/stable/reference/random/index.html).
- pandas. [`DataFrame` API reference](https://pandas.pydata.org/docs/reference/frame.html).

These sources support explicit backtest design, caution about repeated strategy selection, and attention to trading costs. The class workflow, artificial generator, split, weighting placeholder, sensitivity grid, and numerical thresholds do not reproduce the sources' complete procedures or empirical findings. A clean rerun shows reproducibility under recorded conditions; it does not prove that a financial conclusion is true.
