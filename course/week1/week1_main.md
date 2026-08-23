# 115學年度第1學期課程教學大綱

學年：115學年<br>
學期：第1學期<br>
開課班級：M0L20A 資訊管理系碩士班二年甲班<br>
科目代碼：M0L23910<br>
科目名稱：智慧金融科技（ARTIFICIAL INTELLIGENCE IN FINTECH）<br>
學分數：3.0學分<br>
每週上課時數：3.0小時<br>
任課老師：李文毅

## 教學目標

This EMI course introduces artificial intelligence in financial technology through a structured progression from financial data to backtesting, performance evaluation, and portfolio weighting. Students will first learn how financial data are collected, aligned, transformed, and used without information leakage. They will then construct reproducible quantitative-trading backtests that incorporate rolling or expanding estimation windows, model updating, signal updating, portfolio rebalancing, transaction costs, and realistic execution assumptions.

After the midterm assessment, students will study established portfolio-weighting methods and compare their assumptions, required inputs, risk characteristics, transaction costs, and out-of-sample stability. Each team will identify a specific limitation of an existing method, formulate a clearly defined modification, and evaluate the proposed method using common data, benchmarks, rebalancing rules, transaction costs, and out-of-sample periods.

After completing the course, students should be able to prepare and audit financial data; calculate and align returns correctly; identify look-ahead bias, information leakage, and other sources of invalid backtest results; implement fixed, rolling, expanding, and walk-forward evaluation; distinguish model retraining, signal updating, and portfolio rebalancing; incorporate transaction costs and execution assumptions; evaluate financial performance and its uncertainty; implement and compare established portfolio-weighting methods; develop a mathematically defined modification of an existing method; and communicate financial evidence, limitations, and conclusions in professional English.

## 教學內容

The course begins by establishing the relationship among financial data, trading signals, backtesting, performance evaluation, and portfolio weights. Students first learn how prices, returns, financial variables, and trading calendars are constructed. Particular attention is given to observation time, publication time, signal time, decision time, execution time, and evaluation time so that trading experiments use only information that would have been available at the relevant decision point.

Students then construct quantitative-trading backtests. The course covers signal and position rules, holding periods, rolling windows, expanding windows, walk-forward evaluation, model retraining, signal updating, rebalancing frequency, transaction costs, bid-ask spreads, slippage, turnover, and execution delay. Simple financial rules and machine-learning predictions are evaluated under the same backtesting conditions.

After constructing a valid backtest, students learn how to evaluate investment performance. The course considers returns after costs, annualized return, volatility, the Sharpe ratio, the Sortino ratio, maximum drawdown, drawdown duration, turnover, benchmark comparison, market-period sensitivity, and statistical uncertainty. Students also examine overfitting, repeated model selection, multiple testing, and data snooping. Predictive accuracy is treated as model evidence rather than as sufficient evidence of investment value.

The second half of the course focuses on portfolio weighting. Students examine equal weighting, market-capitalization weighting, weights based on normalized trading signals, inverse-volatility weighting, minimum-variance portfolios, mean-variance optimization, maximum-Sharpe portfolios, equal risk contribution, risk budgeting, hierarchical risk parity, and minimum-CVaR portfolios. Covariance estimation, estimation error, portfolio concentration, downside risk, turnover constraints, transaction-cost penalties, and robust optimization are introduced when they are relevant to the weighting methods being studied.

Throughout the second half of the course, students gradually develop their own modification of an existing weighting method. The proposed method must address an explicitly stated limitation, define its inputs and calculation procedure, satisfy portfolio constraints, specify its rebalancing rule, and be reproducible by another person. Its performance must be compared with appropriate baseline methods using the same information boundary and out-of-sample evaluation conditions.

Research articles are introduced when they help students explain an empirical or methodological problem encountered during implementation. Students are expected to distinguish what the evidence demonstrates from what remains uncertain and to limit their conclusions to the data, periods, assumptions, and tests actually examined.

## 第一週

Course introduction, learning outcomes, assessment, semester projects, English reporting, reproducibility, peer comparison, and the permitted use of generative AI.

## 第二週

Financial prices, return calculation, data frequency, adjusted prices, missing values, trading calendars, and variable alignment.

## 第三週

Information timing, look-ahead bias, information leakage, financial feature construction, and simple trading signals.

## 第四週

Construction of a reproducible backtest from trading signals, positions, trades, execution assumptions, and portfolio returns.

## 第五週

Fixed train-test splits, rolling windows, expanding windows, and walk-forward evaluation for financial time series.

## 第六週

Model retraining, signal updating, portfolio rebalancing, and alternative update frequencies and rules.

## 第七週

Transaction costs, bid-ask spreads, slippage, execution delay, turnover, cost sensitivity, and benchmark strategies.

## 第八週

Performance measures, statistical uncertainty, overfitting, multiple testing, backtest auditing, and the first individual technical examination.

## 第九週

Asynchronous midterm submission of reproducible quantitative-trading code, results, and an English report through the LMS.

## 第十週

Individual short reports on the midterm work and the transition from trading signals to portfolio weights.

## 第十一週

Portfolio constraints, signal normalization, equal weighting, market-capitalization weighting, signal-based weighting, and inverse-volatility weighting.

## 第十二週

Expected returns, covariance estimation, minimum-variance, mean-variance, maximum-Sharpe portfolios, and weight instability.

## 第十三週

Covariance estimation, equal risk contribution, risk budgeting, hierarchical risk parity, and diversification of capital and risk.

## 第十四週

Value at Risk, Conditional Value at Risk, scenario-based optimization, regularization, turnover limits, transaction-cost penalties, and robust portfolio decisions.

## 第十五週

Development of student weighting methods, the second individual technical examination, and submission of final reports, code, and results.

## 第十六週

First session of final reports and oral discussion of student portfolio-weighting methods.

## 第十七週

Second session of final reports and evidence-based comparison and ranking of other teams.

## 第十八週

Advanced portfolio-weighting issues and the development of research questions from limitations observed during the semester.

## 成績評量

- Class participation and weekly learning evidence: 10%

- Two individual technical examinations: 40%

  - First technical examination on financial data, backtesting, rebalancing, and performance evaluation: 20%

  - Second technical examination on portfolio weighting, risk estimation, constraints, and empirical comparison: 20%

- Midterm quantitative-trading work: 25%

  - Team implementation, reproducibility, and written report: 15%

  - Individual short report and oral explanation: 5%

  - Individual evidence-based review of other approaches: 5%

- Final portfolio-weighting work: 25%

  - Team weighting method, implementation, and empirical evaluation: 15%

  - Final report presentation and oral responses: 5%

  - Individual evidence-based comparison of other teams: 5%

Students will not evaluate their own team. Rankings received from classmates will not directly determine team grades. Individual comparison scores will depend on whether the student uses common test results, financial measures, implementation evidence, and clearly stated comparison criteria.

## 指定教科書及參考書籍

The course does not require a single textbook. It uses selected English journal articles, instructor-prepared notebooks and research notes, and open or authorized financial datasets. Supporting materials cover financial data construction, backtesting, performance evaluation, covariance estimation, portfolio weighting, risk parity, CVaR, transaction costs, and robust portfolio optimization. Research readings are assigned when the corresponding empirical or methodological issue is introduced.
