# Week 18 — Support

This file provides preparation, a template for the proposed study, review checks, troubleshooting, optional extensions, and sources for [Week 18 main](week18_main.md).

## Before class

Bring the frozen final report, reproducible code, common evaluation outputs, questions received during Weeks 16–17, and one table or figure showing a limitation. Do not reopen the final result merely to improve its score; Week 18 uses the limitation to plan a new study.

Run this clean-environment check before class:

```python
import numpy as np
import pandas as pd

print("numpy", np.__version__)
print("pandas", pd.__version__)
```

Expected evidence: two version strings and no import error. Preserve the complete error if preparation fails.

## Plan the proposed study

```text
Observed limitation and exact evidence location:
Data, period, and assumptions of that observation:
Financial decision affected:
Primary research question:
Primary outcome:
Proposed method or modification:
Why it addresses the observed limitation:
Simple and strong baselines:
Advanced extension, if any, and why a simpler comparison is insufficient:
Assets, market, frequency, and sample period:
Observation, signal, decision, execution, and evaluation times:
Development, validation, and final test design:
Rebalancing, transaction costs, and portfolio constraints:
Settings fixed before the final test:
Market-condition definition and real-time detection rule, if used:
Sequential information protocol and comparator, if online portfolio selection is claimed:
Machine-learning target, predictors, training, validation, and portfolio mapping, if used:
Reference distribution, uncertainty set, distance, and set size, if distributionally robust optimization is used:
Random seeds and repetitions, if applicable:
Primary statistical or financial comparison:
Robustness checks tied to failure mechanisms:
Result that would count against the explanation:
Handling of missing, failed, or infeasible runs:
Narrowest claim supported if the result is positive:
Interpretation if the result is negative or mixed:
Data, software, computation, or licensing needs:
```

## Review the proposed study

### Question and mechanism

- The question refers to an observed financial limitation rather than only a new algorithm.
- The proposed modification targets a stated mechanism.
- A negative or mixed result remains interpretable.
- One primary outcome is chosen before final testing.

### Information and chronology

- Every input has an observation and availability time.
- Model fitting and parameter selection occur before the associated test return.
- A reused test period is labeled as used.
- Regime labels are not used before they could have been identified in real time.
- A sequential weight update is not described as a particular online method unless its update rule and comparator are implemented.

### Common comparison

- Baselines include a simple credible allocation and a relevant strong method.
- Data, periods, constraints, execution, costs, and measures are common.
- Method-specific settings and computational requirements are disclosed.
- Failed or infeasible runs remain in the evidence.
- A trade threshold is distinguished from an objective that explicitly includes transaction costs.

### Claims and reproducibility

- The design states what would falsify the explanation.
- Robustness checks correspond to plausible failure mechanisms.
- The claim is limited to the population, periods, assumptions, and checks.
- Code, data definition, environment, seeds, and outputs can be preserved.

## Established terms used this week

| Term | Meaning in Week 18 |
|---|---|
| falsifiable research question | A question for which evidence could count against the proposed explanation |
| time-varying weights | Portfolio weights recomputed as available information changes |
| market regime | A state associated with different distributional behavior; a portfolio application needs a rule for estimating the current state without future information |
| online portfolio selection | Sequential updating and evaluation as observations arrive |
| machine-learning allocation | Use of a learned conditional estimate or direct mapping within a documented portfolio decision |
| distributionally robust optimization | Optimization over a specified set of plausible distributions |
| estimation uncertainty | Variation in estimated inputs caused by finite data and model choices |

## Troubleshooting

| Symptom | First check | Action |
|---|---|---|
| The question can only have a positive answer | Identify the falsifying result | Rewrite it as a comparison with a measurable outcome |
| “Regime” has no operational definition | Identify the observable variable and decision timing | Define and validate it without future information, or use ordinary period labels |
| A rolling backtest is called an online algorithm | Identify the implemented update and comparator | Use ordinary sequential-backtest language unless the stated online method is actually implemented |
| A stress row is called distributionally robust optimization | Identify the reference distribution and uncertainty set | Describe it as stress sensitivity or formulate the required set and worst-case problem |
| A trade threshold is described as optimized for costs | Inspect the objective and selection procedure | Call it a stated threshold unless costs actually determine it |
| A complex method has no financial purpose | Trace it to the observed limitation | Use a simpler method unless complexity tests a specific mechanism |
| The widest trade threshold looks best | Check how thresholds were selected and whether test data were reused | Report the grid as exploratory and reserve new data for confirmation |
| Different lookbacks reverse the conclusion | Verify common periods and accounting | Report specification sensitivity and narrow the claim |
| A negative result seems unusable | Return to the proposed mechanism and power of the design | Explain what was ruled out and what remains unresolved |
| Required data cannot be redistributed | Check source terms and repository visibility | Keep restricted data outside `course/` and publish acquisition instructions only when permitted |

## Optional extensions

- Replace the artificial known-condition label with a detection model fitted only on earlier observations and audit the delay.
- Compare fixed and volatility-scaled rebalancing thresholds under identical costs.
- Add a turnover penalty directly to an optimization objective and compare it with the threshold rule.
- Evaluate several near-performing prediction models and compare their portfolio weights, not only their prediction scores.
- Specify a distributional uncertainty set and test sensitivity to its size only after identifying an appropriate theoretical reference.

## Reading directions

The following literature supports the established meanings of advanced terms used in this week:

- Hamilton, J. D. (1989). [A New Approach to the Economic Analysis of Nonstationary Time Series and the Business Cycle](https://doi.org/10.2307/1912559). *Econometrica, 57*(2), 357–384.
- Li, B., & Hoi, S. C. H. (2014). [Online Portfolio Selection: A Survey](https://doi.org/10.1145/2512962). *ACM Computing Surveys, 46*(3), Article 35.
- Ban, G.-Y., El Karoui, N., & Lim, A. E. B. (2018). [Machine Learning and Portfolio Optimization](https://doi.org/10.1287/mnsc.2016.2644). *Management Science, 64*(3), 1136–1154.
- Mohajerin Esfahani, P., & Kuhn, D. (2018). [Data-driven distributionally robust optimization using the Wasserstein metric: performance guarantees and tractable reformulations](https://doi.org/10.1007/s10107-017-1172-1). *Mathematical Programming, 171*, 115–166.

These sources establish terminology and possible research directions; Week 18 does not reproduce their methods, theoretical guarantees, datasets, or empirical results. The class threshold, artificial condition labels, and sensitivity tables are teaching calculations. Core readings must still be confirmed with the instructor before they become required readings. Use the Week 11–13 references for mean–variance, covariance estimation, risk allocation, and CVaR foundations. Software documentation can support implementation details but not a financial research claim.
