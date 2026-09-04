# Week 1 — From an Investment Idea to a Testable Quantitative Trading Question

[Read the complete course syllabus](../README.md).

## Core question

How can a broad investment idea become a question that later financial evidence can answer?

After completing Week 1, students should be able to:

- identify the financial decision behind an investment idea;
- replace words such as “better,” “safer,” or “works” with an explicit outcome and comparison baseline;
- state what information must be available before the decision;
- identify what must remain fixed when two methods are compared;
- state a result that would not support the proposed idea; and
- separate current assumptions from evidence that will be produced in later weeks.

## Course workflow

[![Course workflow from financial data to portfolio research. The course moves through financial data, features, signals, backtesting, machine-learning prediction, costs, performance evaluation, portfolio weighting, repeated cost and performance evaluation, and research reports.](assets/week1-course-workflow.svg)](assets/week1-course-workflow.svg)

*Figure 1. The course moves from interpretable financial data to a portfolio decision. Portfolio weights must be evaluated again after transaction costs and rebalancing. Select the figure to open it at full size.*

The figure is a dependency chain. A later result cannot repair an earlier definition error. For example, a high reported return does not answer the intended question if the backtest used information that was unavailable when the trade should have been decided.

## 1. Start with the financial decision

An investment idea is not yet an answerable question when a reader cannot determine what is chosen, when the choice is made, what evidence is available, or what result is being compared.

| Initial statement | What the statement does not yet specify |
| --- | --- |
| “AI can beat the market.” | The assets, portfolio decision, model output, execution time, costs, benchmark, evaluation period, and meaning of “beat.” |
| “Technical indicators improve performance.” | The indicators, information available before each decision, trading rule, performance measure, baseline, and conditions held fixed. |
| “This weighting method is robust.” | The data changes or market periods being examined, the measured outcome, the comparison method, and the result that would contradict the claim. |

The first question should therefore be: **What financial decision will the analysis support?** A prediction is useful to this course only after the student explains how it affects a trade, position, or portfolio weight.

## 2. State the parts that make the question testable

| Required part | What must be stated | Why it matters |
| --- | --- | --- |
| Financial decision | What position or portfolio weight is chosen, and when | Connects analysis to an action |
| Asset scope | Which securities and whether cash is allowed | Defines the population covered by the conclusion |
| Information available before the decision | Which observations are available before the decision time | Prevents future information from entering the decision |
| Outcome and horizon | The measured quantity and the period over which it is measured | Replaces undefined words such as “works” |
| Comparison baseline | The rule or portfolio used for comparison | Makes a reported difference interpretable |
| Conditions held fixed | Data, dates, execution, costs, constraints, and measures that remain the same | Makes the reported difference easier to interpret |
| Result that would not support the idea | State the exact result that would not support the proposed idea | Allows the idea to be challenged by evidence |
| Scope limitation | The assets, period, assumptions, and checks beyond which the conclusion does not extend | Prevents a limited backtest from becoming a general market claim |

This sentence structure may help, but every bracket must be replaced with a concrete answer:

> Within [asset scope and historical period], how does [stated decision rule], using only [information available before each decision], compare with [baseline] on [defined outcome and horizon], when [common conditions] are held fixed?

## 3. Worked example

The following scenario is an **illustrative teaching example**. Its assets, dates, model, and assumed cost are not the official settings for Report 1, and it provides no evidence that the proposed method is profitable.

### Initial idea

> AI can select better investments.

### Question specification

| Part | Illustrative specification |
| --- | --- |
| Financial decision | After each trading day's close, select nonnegative target weights for Apple Inc. common stock, Microsoft Corporation common stock, and USD cash. The three weights total 100%. |
| Intended security labels | AAPL for Apple Inc. common stock and MSFT for Microsoft Corporation common stock. The provider's identifiers, venue, currency, and effective dates must be verified in Week 2 before data are used. |
| Available information | Daily prices and volume available through the decision day's close. |
| Return predicted by the model | Each stock's price change from the next regular session's opening to that session's closing, expressed as a percentage of the opening price. |
| Model-based decision rule | A documented machine-learning model predicts each stock's next-session opening-to-closing price return. Replace a negative prediction with zero and divide positive predictions by their sum. If neither prediction is positive, allocate 100% to USD cash. |
| Execution and holding interval | Establish stock positions at the next regular session's opening and close them at that session's closing. Apply the same rule to both portfolios. |
| Development data | Predictor observations begin on 2020-01-02. Every return used to fit or select the model ends no later than the 2023-12-29 close. |
| Evaluation return intervals | Regular-session opening-to-closing intervals from 2024-01-02 through 2024-12-31. No evaluation-period return is used to fit or select the model. |
| Baseline decision rule | At each evaluation-session opening, allocate 50% to each stock; close both positions at that session's closing. |
| Conditions held fixed | Same intended securities, evaluation sessions, starting capital of USD 100,000, execution and holding times, long-only constraint, and assumed one-way cost of 10 basis points per USD traded. |
| Primary outcome | Net cumulative return after the stated costs over the evaluation period. |
| Additional evidence | Maximum drawdown and turnover over the same evaluation period. |
| Result not supporting the primary claim | The model-based portfolio's net cumulative return is less than or equal to the baseline's net cumulative return. |
| Scope limitation | The result concerns these two stocks, this period, this model, and these assumptions; it does not establish future profitability or performance in another market. |

The cost of 10 basis points is an assumption for this example. It is not an observed historical commission, spread, or execution price. The AAPL and MSFT labels state the intended securities; they do not replace the Week 2 identity and data checks.

### Terms used in the example

| Term | Meaning in this example |
| --- | --- |
| Long-only weights | No stock has a negative weight, and the stock and cash weights total 100%. |
| One-way cost of 10 basis points | An assumed cost of 0.10% for each USD amount bought or sold. |
| Net cumulative return | The percentage change in portfolio value across all evaluation sessions after applying the stated costs. |
| Maximum drawdown | The largest percentage decline from an earlier portfolio-value peak to a later trough during the evaluation period. |
| Turnover | The amount bought or sold relative to portfolio value under a stated calculation convention. Week 6 defines the convention used in the backtest. |
| Evaluation period not used for fitting or selection | The model and decision rule are fixed without using any return from the 2024 evaluation intervals. |

### Revised question

> Across the regular-session opening-to-closing return intervals from 2024-01-02 through 2024-12-31, how does the model-based decision rule specified above compare with the 50%-50% decision rule in net cumulative return, maximum drawdown, and turnover, when the model uses only information available through the prior session's close, every return used to fit or select it ends by 2023-12-29, and both portfolios use the same intended securities, sessions, starting capital, execution and holding times, long-only constraint, and assumed one-way cost of 10 basis points per USD traded?

The revised wording still does not prove that the experiment is valid. Weeks 2–8 must establish the data, calculations, information timing, backtest, prediction, costs, performance measures, and weighting rule used to answer it.

## 4. What changes, and what stays fixed?

Suppose two teams use the same assets but Team A trades at the same day's close without costs and Team B trades at the next session's opening with transaction costs. Their results are produced under different execution and cost conditions. A return difference cannot be attributed only to their models.

For the worked example, the intended comparison is:

| Comparison element | Treatment |
| --- | --- |
| Factor that changes | The portfolio decision rule: model predictions and their conversion to weights, compared with the fixed 50%-50% allocation |
| Factors held fixed | Assets, dates, information cutoff, execution time, starting capital, constraints, rebalancing dates, transaction-cost definition, and evaluation measures |
| Primary comparison | Difference in net cumulative return over the same evaluation period |
| Evidence reported alongside the primary comparison | Maximum drawdown and turnover under the same conditions |

This comparison evaluates the complete decision rules. It cannot by itself determine whether any difference came from the prediction model or from the conversion of predictions to weights. If a material condition cannot be held fixed, disclose the difference and describe the results as outcomes under different conditions.

## 5. Evidence must match the claim

| Evidence available | Claim that may be made | Claim that is not supported |
| --- | --- | --- |
| A calculation from controlled teaching data | The calculation follows from the stated artificial values and assumptions | The same result occurs in the market |
| One historical evaluation period that was not used to fit or select the method | The method produced the reported result for the stated assets, period, and rules | The method will remain profitable in the future |
| Higher prediction accuracy | The model improved the stated prediction measure on the stated data | The resulting portfolio necessarily earned a higher net return |
| Higher gross return | The strategy earned more before the stated trading costs | The strategy earned more after costs |
| Similar results when several stated data or modeling assumptions are changed one at a time | The conclusion did not reverse under those specific changes | The method is stable under every market condition or modeling choice |

Evidence should lead to a conclusion whose scope is no broader than the completed experiment.

## 6. Activity — Rewrite and audit a question

Choose one initial statement:

1. “A machine-learning model gives better trading signals.”
2. “A new weighting method reduces portfolio risk.”

First, complete the specification below. Do not calculate returns, indicators, signals, or portfolio performance in Week 1.

| Required part | Your answer | Evidence still needed later |
| --- | --- | --- |
| Financial decision |  |  |
| Asset scope |  |  |
| Information available before the decision |  |  |
| Decision, execution, and holding times |  |  |
| Outcome, return interval, and date convention |  |  |
| Development period and evaluation period |  |  |
| Comparison baseline |  |  |
| Conditions held fixed |  |  |
| Result that would not support the idea |  |  |
| Scope limitation |  |  |

Next, write one complete quantitative trading question. Exchange it with another student and record one revision after applying this audit:

| Audit question | Pass or revise | Exact missing item or revision |
| --- | --- | --- |
| Can another reader identify the decision and when it is made? |  |  |
| Are the outcome, horizon, and baseline explicit? |  |  |
| Must every input be available before the decision? |  |  |
| Are the comparison conditions the same? |  |  |
| Is one result that would not support the idea stated? |  |  |
| Does the wording avoid claiming evidence that has not yet been produced? |  |  |

## Completion evidence

Keep the original investment idea, the completed specification, the revised question, and one peer-identified revision. The work is complete when another student can identify:

- what decision is made, when it is executed, and how long the position is held;
- which assets and historical period the question covers;
- what information is available before the decision;
- which outcome and baseline are compared;
- what conditions remain fixed;
- what result would not support the idea; and
- what evidence must still be produced in later weeks.

## What later weeks add

| Week | Evidence produced | Week 1 does not yet establish |
| --- | --- | --- |
| 2 | Audited financial data and reproducible return calculations | Whether the proposed data are valid or the return is correctly calculated |
| 3 | Financially motivated features, explicit information timing, and dated signals | Whether a feature is available before the decision or generates the intended signal |
| 4 | Lagged positions, trades, gross returns, and a reproducible wealth path | Whether the signal produces a valid backtest |
| 5–8 | Prediction evidence, costs, performance evaluation, and feasible portfolio weights | Whether the complete strategy supports the Week 1 question |

## Course operations

The [complete syllabus](../README.md) contains the course-operation rules, and the [course statement and GenAI policy](GenAI使用規範.md) applies throughout the semester. A Git commit identifies a version; it does not replace the financial evidence, interpretation, or limitations required by the course.

Proceed to [Week 2 — Financial Data and Returns](week2_main.ipynb) after the Week 1 question can pass the audit above.
