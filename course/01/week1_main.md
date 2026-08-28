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

After completing the quantitative-trading report, students will study established portfolio-weighting methods and compare their assumptions, required inputs, risk characteristics, transaction costs, and out-of-sample stability. Each team will identify a specific limitation of an existing method, formulate a clearly defined modification, and evaluate the proposed method using common data, benchmarks, rebalancing rules, transaction costs, and out-of-sample periods.

After completing the course, students should be able to prepare and audit financial data; calculate and align returns correctly; identify look-ahead bias, information leakage, and other sources of invalid backtest results; implement fixed, rolling, expanding, and walk-forward evaluation; distinguish model retraining, signal updating, and portfolio rebalancing; incorporate transaction costs and execution assumptions; evaluate financial performance and its uncertainty; implement and compare established portfolio-weighting methods; develop a mathematically defined modification of an existing method; and communicate financial evidence, limitations, and conclusions in professional English.

## 教學內容

The course begins by establishing the relationship among financial data, trading signals, backtesting, performance evaluation, and portfolio weights. Students first learn how prices, returns, financial variables, and trading calendars are constructed. Particular attention is given to observation time, publication time, signal time, decision time, execution time, and evaluation time so that trading experiments use only information that would have been available at the relevant decision point.

Students then construct quantitative-trading backtests. The course covers signal and position rules, holding periods, rolling windows, expanding windows, walk-forward evaluation, model retraining, signal updating, rebalancing frequency, transaction costs, bid-ask spreads, slippage, turnover, and execution delay. Simple financial rules and machine-learning predictions are evaluated under the same backtesting conditions.

Report 1 must include at least one prediction model estimated from historical data and compare it with a transparent rule-based or statistical prediction baseline on the same target dates. Students may use the interpretable model practiced in Week 5 or another documented model. Model complexity does not by itself earn a higher score; the assessed evidence is chronological validity, reproducibility, financial interpretation, and whether prediction results lead to a defensible portfolio decision after costs.

After constructing a valid backtest, students learn how to evaluate investment performance. The course considers returns after costs, annualized return, volatility, the Sharpe ratio, the Sortino ratio, maximum drawdown, drawdown duration, turnover, benchmark comparison, market-period sensitivity, and statistical uncertainty. Students also examine overfitting, repeated model selection, multiple testing, and data snooping. Predictive accuracy is treated as model evidence rather than as sufficient evidence of investment value.

The second half of the course focuses on portfolio weighting. Students examine equal weighting, market-capitalization weighting, weights based on normalized trading signals, inverse-volatility weighting, minimum-variance portfolios, mean-variance optimization, maximum-Sharpe portfolios, equal risk contribution, risk budgeting, hierarchical risk parity, and minimum-CVaR portfolios. Covariance estimation, estimation error, portfolio concentration, downside risk, turnover constraints, transaction-cost penalties, and robust optimization are introduced when they are relevant to the weighting methods being studied.

Throughout the second half of the course, students gradually develop their own modification of an existing weighting method. The proposed method must address an explicitly stated limitation, define its inputs and calculation procedure, satisfy portfolio constraints, specify its rebalancing rule, and be reproducible by another person. Its performance must be compared with appropriate baseline methods using the same information boundary and out-of-sample evaluation conditions.

Research articles are introduced when they help students explain an empirical or methodological problem encountered during implementation. Students are expected to distinguish what the evidence demonstrates from what remains uncertain and to limit their conclusions to the data, periods, assumptions, and tests actually examined.

## 第一週

English: Week 1 introduces the overall course direction, learning outcomes, class-participation requirements, three staged reports, English reporting, reproducibility expectations, evidence-based comparison of other teams, and permitted uses of generative AI.

中文：第一週介紹課程整體方向、學習成果、課堂參與要求、三次階段性報告、英文報告方式、可重現性要求、以證據比較其他組別的方法，以及生成式人工智慧的允許用途。

## 第二週

English: Week 2 introduces financial prices, simple and log returns, cumulative and portfolio returns, data frequency, adjusted prices, corporate actions, missing values, trading calendars, and the alignment of financial variables.

中文：第二週介紹金融資產價格、簡單報酬率與對數報酬率、累積報酬率與投資組合報酬率、資料頻率、還原價格、公司行動、缺失值、交易日曆，以及金融變數的時間對齊。

## 第三週

English: Week 3 examines observation time, publication time, signal time, decision time, execution time, and evaluation time. Students learn how to prevent look-ahead bias and information leakage and how to transform financial hypotheses into measurable features and simple trading signals.

中文：第三週說明觀察時間、發布時間、訊號時間、決策時間、執行時間與評估時間。學生將學習如何避免前視偏誤與資訊洩漏，並將財務假說轉換為可衡量的特徵與簡單交易訊號。

## 第四週

English: Week 4 introduces the construction of a reproducible backtest by converting signals into positions, trades, holdings, and portfolio returns under clearly stated entry, exit, holding-period, position-limit, and execution-price assumptions.

中文：第四週介紹如何建立可重現的回測，將交易訊號轉換為部位、交易、持有狀態與投資組合報酬，並清楚設定進場、出場、持有期間、部位限制及執行價格假設。

## 第五週

English: Week 5 implements an interpretable prediction model, compares it with a transparent prediction baseline, and studies fixed train-test splits, rolling windows, expanding windows, and walk-forward evaluation. The class also distinguishes model retraining, signal updating, and portfolio rebalancing and compares alternative update frequencies and rules.

中文：第五週實作一個可解釋的預測模型，將其與透明的預測基準比較，並探討固定訓練測試切割、滾動視窗、擴展視窗與逐步向前評估。課程也區分模型重新訓練、訊號更新及投資組合再平衡，並比較不同的更新頻率與規則。

## 第六週

English: Week 6 incorporates commissions, bid-ask spreads, slippage, execution delay, and turnover into backtests. Students conduct cost-sensitivity analysis and compare AI-based strategies with cash, buy-and-hold, rule-based, and equal-weighted benchmarks.

中文：第六週將手續費、買賣價差、滑價、執行延遲與週轉率納入回測。學生將進行交易成本敏感度分析，並將人工智慧策略與現金、買入持有、規則式策略及等權重基準進行比較。

## 第七週

English: Week 7 evaluates annualized return, volatility, Sharpe and Sortino ratios, maximum drawdown, drawdown duration, turnover, and returns after costs. Students also examine statistical uncertainty, overfitting, multiple testing, data snooping, and backtest auditing.

中文：第七週評估年化報酬率、波動度、夏普比率、索提諾比率、最大回撤、回撤期間、週轉率與扣除成本後報酬，並檢視統計不確定性、過度配適、多重檢定、資料探勘偏誤及回測稽核。

## 第八週

English: Week 8 introduces portfolio constraints, signal normalization, equal weighting, market-capitalization weighting, signal-based weighting, and inverse-volatility weighting under common information, rebalancing, transaction-cost, and out-of-sample evaluation conditions.

中文：第八週介紹投資組合限制、訊號標準化、等權重、市值加權、訊號式加權與反波動度加權，並在相同資訊、再平衡、交易成本及樣本外評估條件下比較各方法。

## 第九週

English: No class or required report is scheduled in Week 9 because of instructor travel. Students may review the quantitative-trading and basic-weighting materials in preparation for Report 1.

中文：第九週因教師出國，不安排正式上課或必要報告。學生可自行複習量化交易與基本加權方法，準備第一次報告。

## 第十週

English: Week 10 contains Report 1. Every student presents the team's required prediction model, transparent prediction baseline, quantitative-trading strategy, reproducible backtesting evidence, financial performance, limitations, and initial approach to converting trading signals into portfolio weights.

中文：第十週進行第一次報告。每位學生說明所屬團隊必要的預測模型、透明的預測基準、量化交易策略、可重現的回測證據、財務績效、研究限制，以及將交易訊號轉換為投資組合權重的初步方法。

## 第十一週

English: Week 11 introduces expected-return and covariance estimation, the efficient frontier, minimum-variance portfolios, mean-variance optimization, and maximum-Sharpe portfolios, with attention to concentration and weight instability caused by estimation error.

中文：第十一週介紹預期報酬與共變異數估計、效率前緣、最小變異數投資組合、平均數—變異數最佳化及最大夏普比率投資組合，並討論估計誤差造成的集中度與權重不穩定問題。

## 第十二週

English: Week 12 compares sample, shrinkage, factor-based, and rolling covariance estimates and introduces equal risk contribution, risk budgeting, hierarchical risk parity, and the distinction between diversification of capital and diversification of risk.

中文：第十二週比較樣本共變異數、收縮估計、因子模型估計與滾動共變異數估計，並介紹等風險貢獻、風險預算、階層式風險平價，以及資金分散與風險分散的差異。

## 第十三週

English: Week 13 introduces Value at Risk, Conditional Value at Risk, minimum-CVaR portfolios, scenario-based optimization, regularization, portfolio constraints, turnover limits, transaction-cost penalties, and robust portfolio decisions.

中文：第十三週介紹風險值、條件風險值、最低條件風險值投資組合、情境式最佳化、正則化、投資組合限制、週轉率限制、交易成本懲罰及穩健投資組合決策。

## 第十四週

English: Week 14 contains Report 2. Each team presents a limitation of an existing weighting method, its proposed modification, mathematical definition, portfolio constraints, rebalancing rule, baseline methods, implementation progress, and preliminary evidence.

中文：第十四週進行第二次報告。各組說明既有加權方法的限制、提出的修改方式、數學定義、投資組合限制、再平衡規則、基準方法、實作進度及初步證據。

## 第十五週

English: Week 15 is used to revise the proposed weighting method, complete common out-of-sample validation, transaction-cost analysis, concentration and turnover measures, and robustness checks, and submit the frozen final report, code, and results before Week 16.

中文：第十五週用於修改所提出的加權方法，完成共同樣本外驗證、交易成本分析、集中度與週轉率衡量及穩健性檢查，並在第十六週前提交凍結版本的期末報告、程式碼與結果。

## 第十六週

English: Week 16 is the first final-report session. The first set of teams presents its weighting method, financial motivation, mathematical definition, implementation, baseline comparison, out-of-sample results, and limitations under the common presentation requirements.

中文：第十六週為期末報告第一場。第一批組別依共同報告要求，說明其加權方法、財務動機、數學定義、實作方式、基準比較、樣本外結果與限制。

## 第十七週

English: Week 17 is the second final-report session. The remaining teams present under the same requirements, after which students compare and rank the other teams using common financial measures, implementation evidence, assumptions, and robustness results.

中文：第十七週為期末報告第二場。其餘組別依相同要求完成報告，之後學生使用共同財務指標、實作證據、假設與穩健性結果比較並排序其他組別。

## 第十八週

English: Week 18 connects the semester's findings with advanced issues such as time-varying weights, market regimes, online portfolio selection, machine-learning allocation, distributionally robust optimization, estimation uncertainty, and transaction-cost-aware rebalancing and develops research questions from observed limitations.

中文：第十八週將整學期的發現連結至進階議題，包括時變權重、市場狀態、線上投資組合選擇、機器學習資產配置、分布穩健最佳化（distributionally robust optimization）、估計不確定性及考量交易成本的再平衡，並由觀察到的限制發展研究問題。

## 成績評量

- Class participation and weekly learning evidence: 40%

- Report 1 — Quantitative Trading Report: 15%

  - Team prediction model, strategy, reproducible backtest, performance evidence, and written report: 8%

  - Individual presentation and oral responses: 4%

  - Individual evidence-based review of other approaches: 3%

- Report 2 — Weighting Method Progress Report: 15%

  - Team method proposal, mathematical definition, implementation, and preliminary evidence: 10%

  - Brief presentation and oral responses: 5%

- Report 3 — Final Report: 30%

  - Team weighting method, implementation, and empirical evaluation: 18%

  - Final presentation and oral responses: 6%

  - Individual evidence-based comparison of other teams: 6%

Students will not evaluate their own team. Rankings received from classmates will not directly determine team grades. Individual comparison scores will depend on whether the student uses common test results, financial measures, implementation evidence, and clearly stated comparison criteria.

Detailed observable criteria, team and individual responsibilities, permitted resources, version rules, and arrangements for the three report sessions are provided in [Week 1 support](week1_support.md) and the corresponding report-week files. Report 1 common settings are announced by the start of Week 3, and final portfolio settings are announced by the start of Week 11. Students will not be evaluated against an unannounced setting.

## 指定教科書及參考書籍

The course does not require a single textbook. It uses selected English journal articles, instructor-prepared notebooks and research notes, and open or authorized financial datasets. Supporting materials cover financial data construction, backtesting, performance evaluation, covariance estimation, portfolio weighting, risk parity, CVaR, transaction costs, and robust portfolio optimization. Research readings are assigned when the corresponding empirical or methodological issue is introduced.
