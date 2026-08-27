# Week 16 — Support

## Frozen-submission check

Confirm the following before the deadline:

- all files show the same team, contributors, submission date, and version identifier;
- the data source, retrieval record, fields, periods, frequency, and return definition are complete;
- the report and code use the same method, parameters, constraints, costs, dates, and baselines;
- training and development evidence is separated from the common evaluation of the final period;
- the result for the final period was produced only after the method and settings were frozen;
- seeds, repetitions, solver status, residual checks, failures, and fallback behavior are recorded when applicable;
- tables and figures identify period, frequency, units, cost treatment, and comparison method;
- conclusions cite the corresponding result and disclose material limitations; and
- generative AI use is disclosed in accordance with the course policy.

The required common result table uses one row for the proposed method and one row for every required baseline. It reports the common final period, number of return observations, cumulative and annualized return after costs, annualized volatility, the stated Sharpe ratio convention, the required downside risk measure, maximum drawdown, total one-way turnover, total cost deduction, mean concentration, mean change in target weights, and failed or infeasible decisions. Every row must use the same dates and metric definitions.

For this table, concentration at a rebalance is the sum of squared target weights. The change in target weights is the sum of absolute differences between consecutive target weight vectors, averaged across rebalances. This differs from turnover, which compares the new target with the drifted holdings immediately before trade. The common settings announced in Week 11 specify the downside risk convention, confidence level, annualization, and risk-free rate convention.

## Presentation check

Use the submitted version. Be prepared to identify the source of each input, explain when it becomes available, reproduce the weight calculation for one date, trace one reported portfolio result, and explain one failed or unfavorable case. A visually polished slide is not a substitute for missing implementation evidence.

If a technical correction is necessary after the freeze, preserve both versions and record:

| Frozen result | Error found | Evidence of the error | Correction | Effect on the conclusion |
| --- | --- | --- | --- | --- |
|  |  |  |  |  |

Discussion focuses on whether the reported conclusions follow from the method, assumptions, common comparisons, and out-of-sample evidence. Keep notes on all teams for the individual comparison in Week 17.
