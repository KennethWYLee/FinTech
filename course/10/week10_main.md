# Week 10 — Report 1: Quantitative Trading Report

## Purpose

Report 1 demonstrates that the team can turn a financial question into a reproducible quantitative trading analysis that includes at least one prediction model estimated from historical data. Every student must explain at least one empirical result and answer questions about its validity and financial interpretation. A more complex model does not receive credit merely for being more complex.

## Required team submission

The frozen submission is due through GitHub 48 hours before the scheduled start of class. Follow the shared formats and version rules in [Week 1](../01/week1_main.md). The Report 1 main text may contain at most 2,500 words and eight figures or tables in total. Each student may use at most three content slides for the individual presentation. Material beyond these limits is not assessed.

The submission must include the written report, executable code or notebook, data and environment records, result files, presentation, contribution statement, GenAI disclosure, and a version identifier. The report, code, results, and presentation must describe the same analysis. Students should expect approximately 6–8 hours of additional preparation for Report 1, using the evidence already produced in Weeks 2–8.

The submission must show:

1. the financial question and why it matters;
2. the assets, fields, dates, frequency, return definition, and permitted information at each decision time;
3. at least one prediction model estimated from historical data, including its target, features, training rule, prediction timing, and update schedule;
4. comparison of the required model with a transparent rule-based or statistical prediction baseline on the same target dates;
5. the conversion of predictions into a trading signal, position rule, execution assumption, and holding period;
6. the fixed, rolling, expanding, or walk-forward evaluation design and the model, signal, and rebalancing update rules;
7. transaction costs, turnover, portfolio constraints, and failure behavior;
8. comparison with the required common trading benchmarks under the same dates, information, execution, and cost assumptions;
9. predictive results and financial results after costs, including return, risk, drawdown, and turnover measures, with prediction quality kept distinct from portfolio performance;
10. uncertainty, possible leakage or overfitting, implementation limitations, and claims that remain unsupported; and
11. an initial, clearly defined method for converting signals into portfolio weights.

The common dataset and required numerical settings are announced through GitHub by the start of Week 3. Teams must use those settings for the common comparison and may separately label an additional analysis.

## Individual presentation and review

Each student receives an equal short presentation slot. The student must identify their contribution, explain one result using traceable evidence, and respond without relying on another team member to answer in their place. Students then review and rank the other teams; students do not review their own team.

All students follow the same presentation and question requirements. Students record evidence during the presentations and use it to complete the individual comparison. The presentation order is published after the number of students is known.

## Scoring — 15 points

| Component | Points | Observable criteria |
| --- | ---: | --- |
| Team submission | 2 | The financial question, data, return definition, dates, and information boundary are precise and consistent. |
| Team submission | 2 | The required prediction model and transparent prediction baseline have defined targets, inputs, chronological training, update rules, and common-date measures. |
| Team submission | 2 | Predictions, signals, positions, trades, execution, costs, and portfolio returns are correctly connected. |
| Team submission | 1 | Code, dependencies, inputs, checks, results, and required common benchmarks can be traced and rerun. |
| Team submission | 1 | Conclusions follow from the evidence and disclose uncertainty, failures, and limitations. |
| Individual presentation and oral responses | 1 | The student gives a clear financial explanation of the assigned work. |
| Individual presentation and oral responses | 2 | The student connects a stated result to a specific table, figure, calculation, or execution record. |
| Individual presentation and oral responses | 1 | Answers identify relevant assumptions, validity threats, or additional checks. |
| Individual review of other approaches | 1 | The review uses the common financial and implementation criteria. |
| Individual review of other approaches | 1 | The ranking cites traceable evidence from the other teams' submitted or presented results. |
| Individual review of other approaches | 1 | The ordering and stated limitations are logically supported. |

The team submission contributes 8 points, the individual presentation and oral responses contribute 4 points, and the individual review contributes 3 points. Completion requires the team submission, the student's own presentation and oral response, and the student's review of all other teams. The individual review is due through GitHub 24 hours after the scheduled end of class. See [Week 10 support](week10_support.md) for the submission and comparison records and the [course GenAI policy](../GenAI使用規範.md) for permitted use and disclosure. Rubric feedback is returned by the start of Week 11.
