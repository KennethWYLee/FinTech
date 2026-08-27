# Week 10 — Support

## Team submission check

Before submission, confirm that the following items agree across the report, code, tables, and presentation:

- team and contributors;
- version identifier and submission date;
- data source, retrieval date, assets, fields, frequency, and observation period;
- return definition and treatment of missing values or differences in market calendars;
- observation, signal, decision, execution, and evaluation times;
- training, validation, and frozen test periods;
- model, signal, position, rebalancing, and terminal-position rules;
- units used for transaction costs, turnover calculation, and execution delay;
- benchmarks, metrics, uncertainty checks, and failed cases;
- environment and dependency record; and
- required generative AI disclosure.

The presentation should use only the figures or tables needed to support its main claims. Every item must identify the period, frequency, units, cost treatment, and comparison method when these details affect interpretation. Provide traceable links to the submitted report and code.

The required common result table uses one row for the proposed strategy and one row for every required benchmark. It reports the common period, number of return observations, cumulative and annualized return after costs, annualized volatility, the stated Sharpe and Sortino ratio conventions, maximum drawdown, drawdown duration, total one-way turnover, total cost deduction, and the count of failed or unavailable decisions. Add a predictive measure only when the approach actually produces a prediction. Every row must use the same dates and metric definitions.

## Individual comparison record

Use the following table once for every other team. Use whole-number ratings from 0 to 10 and cite the result, assumption, file, table, figure, or answer that supports each rating. Do not rate your own team.

Team being reviewed: ____________________

| Comparison dimension | Weight | Rating from 0 to 10 | Traceable evidence and limitation |
| --- | ---: | ---: | --- |
| Valid data timing and information boundary | 25% |  |  |
| Reproducible trading and portfolio accounting | 20% |  |  |
| Financial performance after costs relative to the common benchmarks | 20% |  |  |
| Risk, drawdown, stability, and failed conditions | 20% |  |  |
| Conclusions, uncertainty, and limitations | 15% |  |  |

Use the same interpretation for every rating:

- 0: no usable evidence;
- 1–3: major requirements are missing or the evidence is invalid;
- 4–6: partial or mixed evidence with material unresolved problems;
- 7–8: adequate, traceable evidence with limitations disclosed; and
- 9–10: strong and internally consistent evidence that also addresses unfavorable results.

Calculate the weighted total by multiplying each rating by its decimal weight and adding the five products. The result is on a 0–10 scale. Order teams from the highest total to the lowest. If totals are equal, compare the data-timing rating, then the reproducibility rating, and then the risk and stability rating. If the teams remain equal, report a tie rather than inventing a distinction.

| Rank | Team | Weighted total | Main evidence supporting this position |
| ---: | --- | ---: | --- |
|  |  |  |  |

The ordering must follow from the recorded evidence rather than presentation style or personal preference. If evidence is missing or two results are not comparable, state that limitation explicitly. Rankings received do not determine a team's grade; the quality of each student's reasoning determines the individual review score.

If code fails during verification, preserve the error message and identify the last verified result. Do not replace a failed execution with an unrecorded result. Any correction after the frozen deadline must follow the version rule in [Week 1 support](../01/week1_support.md).
