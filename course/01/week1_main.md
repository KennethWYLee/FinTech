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

This EMI course introduces artificial intelligence in financial technology through the following progression: financial data, technical indicators and features derived from stated financial hypotheses, trading signals, backtesting, machine-learning prediction, trading costs and execution, performance evaluation, and portfolio weighting. Students first learn how financial data are collected, aligned, transformed, and used without information leakage. Each later stage must preserve the information boundary and evidence produced by the earlier stages.

Week 8 introduces basic portfolio-weighting methods before the quantitative-trading report. Weeks 11, 12, 13, 15, and 18 then discuss papers related to weighting methods. The assigned papers and detailed discussion topics will be announced later. Each team will use these readings to support the development and evaluation of its own weighting method.

After completing the course, students should be able to prepare and audit financial data; calculate and align returns correctly; construct technical indicators and financially interpretable features; convert a stated rule or model output into a trading signal; identify look-ahead bias, information leakage, and other sources of invalid backtest results; construct a reproducible backtest; estimate and evaluate a prediction model with fixed, rolling, expanding, and walk-forward designs; distinguish model retraining, signal updating, and portfolio rebalancing; incorporate transaction costs and execution assumptions; evaluate financial performance and its uncertainty; implement and compare established portfolio-weighting methods; develop a mathematically defined modification of an existing method; and communicate financial evidence, limitations, and conclusions in professional English.

## 教學內容

The course follows one cumulative sequence: financial data, technical indicators and features derived from stated financial hypotheses, signals, backtesting, machine-learning prediction, costs, performance evaluation, and weighting. Students first learn how prices, returns, financial variables, and trading calendars are constructed. Particular attention is given to observation time, publication time, signal time, decision time, execution time, and evaluation time so that every later calculation uses only information that would have been available at the relevant decision point.

Students next calculate trailing indicators and other features that have an explicit financial interpretation. They connect each feature to a testable financial hypothesis, document its lookback and availability time, and convert a stated rule or model output into a signal. An indicator or signal is not treated as evidence of investment value before execution and evaluation rules have been defined.

Students then construct quantitative-trading backtests by converting signals into positions, trades, holdings, and portfolio returns. After the basic accounting and timing are valid, the course introduces an interpretable machine-learning prediction model, fixed and chronological train-test designs, rolling and expanding estimation windows, and walk-forward evaluation. Model retraining, signal updating, and portfolio rebalancing are treated as separate decisions.

Trading costs and execution assumptions are added after the gross backtest is reproducible. The course covers commissions, bid-ask spreads, slippage, turnover, execution delay, and cost sensitivity. Simple financial rules and machine-learning predictions are evaluated under the same dates, execution assumptions, and cost convention.

Report 1 must include at least one prediction model estimated from historical data and compare it with a transparent rule-based or statistical prediction baseline on the same target dates. Students may use the interpretable model practiced in Week 5 or another documented model. Model complexity does not by itself earn a higher score; the assessed evidence is chronological validity, reproducibility, financial interpretation, and whether prediction results lead to a defensible portfolio decision after costs.

After the backtest contains the prediction, execution, and cost rules, students learn how to evaluate investment performance. The course considers returns after costs, annualized return, volatility, the Sharpe ratio, the Sortino ratio, maximum drawdown, drawdown duration, turnover, benchmark comparison, market-period sensitivity, and statistical uncertainty. Students also examine overfitting, repeated model selection, multiple testing, and data snooping. Predictive accuracy is treated as model evidence rather than as sufficient evidence of investment value.

Week 8 begins the transition to portfolio weighting with equal weighting, market-capitalization weighting, weights based on normalized trading signals, and inverse-volatility weighting. Weeks 11, 12, 13, 15, and 18 discuss papers related to weighting methods. The exact methods and supporting topics depend on the assigned papers and therefore are not specified in advance in this syllabus.

Throughout the second half of the course, students gradually develop their own modification of an existing weighting method. The proposed method must address an explicitly stated limitation, define its inputs and calculation procedure, satisfy portfolio constraints, specify its rebalancing rule, and be reproducible by another person. Its performance must be compared with appropriate baseline methods using the same information boundary and out-of-sample evaluation conditions.

Papers related to weighting methods are the primary learning materials in Weeks 11, 12, 13, 15, and 18. Reading details will be provided after the papers are selected.

## 第一週

English: Week 1 introduces the overall course direction, learning outcomes, class-participation requirements, three staged reports, English reporting, reproducibility expectations, evidence-based comparison of other teams, and permitted uses of generative AI.

中文：第一週介紹課程整體方向、學習成果、課堂參與要求、三次階段性報告、英文報告方式、可重現性要求、以證據比較其他組別的方法，以及生成式人工智慧的允許用途。

## 第二週

English: Week 2 introduces financial prices, simple and log returns, cumulative and portfolio returns, data frequency, adjusted prices, corporate actions, missing values, trading calendars, and the alignment of financial variables.

中文：第二週介紹金融資產價格、簡單報酬率與對數報酬率、累積報酬率與投資組合報酬率、資料頻率、還原價格、公司行動、缺失值、交易日曆，以及金融變數的時間對齊。

## 第三週

English: Week 3 examines observation time, publication time, signal time, decision time, execution time, and evaluation time. Students calculate technical indicators and features derived from stated financial hypotheses, including trailing momentum and rolling volatility, check their information boundaries, and convert a stated feature rule into a simple trading signal.

中文：第三週說明觀察時間、發布時間、訊號時間、決策時間、執行時間與評估時間。學生將計算包含過去動能與滾動波動度在內的技術指標及由明確財務假說推導的特徵，檢查其資訊邊界，並依明確的特徵規則產生簡單交易訊號。

## 第四週

English: Week 4 introduces the construction of a reproducible backtest by converting signals into positions, trades, holdings, and portfolio returns under clearly stated entry, exit, holding-period, position-limit, and execution-price assumptions.

中文：第四週介紹如何建立可重現的回測，將交易訊號轉換為部位、交易、持有狀態與投資組合報酬，並清楚設定進場、出場、持有期間、部位限制及執行價格假設。

## 第五週

English: Week 5 introduces machine-learning prediction through an interpretable prediction model, compares it with a transparent prediction baseline, and studies fixed train-test splits, rolling windows, expanding windows, and walk-forward evaluation. The class also distinguishes model retraining, signal updating, and portfolio rebalancing and compares alternative update frequencies and rules.

中文：第五週以一個可解釋的預測模型介紹機器學習預測，將其與透明的預測基準比較，並探討固定訓練測試切割、滾動視窗、擴展視窗與逐步向前評估。課程也區分模型重新訓練、訊號更新及投資組合再平衡，並比較不同的更新頻率與規則。

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

English: Week 11 discusses papers related to weighting methods.

中文：第十一週討論與投資組合加權方法相關的論文。

## 第十二週

English: Week 12 discusses papers related to weighting methods.

中文：第十二週討論與投資組合加權方法相關的論文。

## 第十三週

English: Week 13 discusses papers related to weighting methods.

中文：第十三週討論與投資組合加權方法相關的論文。

## 第十四週

English: Week 14 contains Report 2. Each team presents a limitation of an existing weighting method, its proposed modification, mathematical definition, portfolio constraints, rebalancing rule, baseline methods, implementation progress, and preliminary evidence.

中文：第十四週進行第二次報告。各組說明既有加權方法的限制、提出的修改方式、數學定義、投資組合限制、再平衡規則、基準方法、實作進度及初步證據。

## 第十五週

English: Week 15 discusses papers related to weighting methods.

中文：第十五週討論與投資組合加權方法相關的論文。

## 第十六週

English: Week 16 is the first final-report session. The first set of teams presents its weighting method, financial motivation, mathematical definition, implementation, baseline comparison, out-of-sample results, and limitations under the common presentation requirements.

中文：第十六週為期末報告第一場。第一批組別依共同報告要求，說明其加權方法、財務動機、數學定義、實作方式、基準比較、樣本外結果與限制。

## 第十七週

English: Week 17 is the second final-report session. The remaining teams present under the same requirements, after which students compare and rank the other teams using common financial measures, implementation evidence, assumptions, sensitivity results, failed or infeasible cases, and stated limitations.

中文：第十七週為期末報告第二場。其餘組別依相同要求完成報告，之後學生使用共同財務指標、實作證據、假設、敏感度結果、失敗或不可行案例及明確陳述的限制，比較並排序其他組別。

## 第十八週

English: Week 18 discusses papers related to weighting methods.

中文：第十八週討論與投資組合加權方法相關的論文。

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

Detailed observable criteria and arrangements for the three report sessions are provided in the corresponding report-week files. Report 1 common settings are announced by the start of Week 3, and final portfolio settings are announced by the start of Week 11. Students will not be evaluated against an unannounced setting.

## 課程運作與繳交說明

The [course statement and GenAI policy](../GenAI使用規範.md) apply throughout the semester. Detailed report instructions are provided for [Report 1](../10/week10_main.md), [Report 2](../14/week14_main.md), and the [final report](../16/week16_main.md).

### How assessment evidence is used

Grades are based only on work submitted through GitHub or directly observed in class. Team scores apply to the shared submission. Individual presentation, oral response, review, and comparison scores are based on each student's own evidence. Rankings received from classmates do not determine a team's grade.

For every scoring row, full credit requires complete and traceable evidence that satisfies the stated criterion. Partial credit reflects evidence that is incomplete, inconsistent, only partly correct, or not adequately explained. No credit is awarded when the required evidence is absent or cannot be connected to the assessed work. Concerns about academic integrity are handled under university rules and are not decided by peer rankings.

Students may use course materials, authorized data, cited public sources, their team's submitted code, and generative AI in accordance with the course policy. Any use that affects analysis, code, wording, or interpretation must be disclosed. Every student remains responsible for verifying the submitted work and explaining it during oral questions. Fabricated data, results, execution records, or sources are not acceptable evidence.

### Report submission through GitHub

Submit each report and its related files through GitHub by the deadline stated in the report-week file. The submitted files must provide enough evidence to review the report, presentation, code, data sources, results, member contributions, and required GenAI disclosure. Do not upload credentials, restricted data, grading records, or personal information to a public repository.

The main report, presentation, and oral explanation use English. Original field names, market labels, or source titles in another language may be retained when an English explanation is provided. Use descriptive headings, table headers, captions, and link text. Figures must remain legible when projected and when viewed on a smaller screen; color alone should not carry essential meaning.

The stated word limits apply to the main text and exclude references, table notes, figure captions, and appendices. An appendix may provide audit details, but the main report must cite the relevant appendix item. The grader is not required to search an uncited appendix for missing evidence. Presentation limits count content slides; a title slide and reference slide do not count, and appendix slides may be used only to answer questions.

The workload estimates assume that teams reuse verified weekly evidence. If the proposed method cannot be completed within the upper estimate, narrow the financial question or implementation scope before the frozen deadline rather than omitting required validity and reproducibility checks.

### Deadlines, frozen versions, and corrections

- Report 1 and its presentation are due 48 hours before the scheduled start of the Week 10 class.
- Report 2 and its presentation are due 48 hours before the scheduled start of the Week 14 class.
- The final report, code, results, and presentation are due 72 hours before the scheduled start of the Week 16 class. The same version is used by teams presenting in either Week 16 or Week 17.
- The Report 1 individual review is due 24 hours after the scheduled end of the Week 10 class. The Report 2 revision record is due 24 hours after the scheduled end of the Week 14 class. The final individual comparison is due 24 hours after the scheduled end of the Week 17 class.

At each deadline, the most recent complete GitHub version becomes the frozen version. A later version does not silently replace it or enter the common presentation and comparison. If a correctness error is found, retain both versions and disclose the error, correction, and effect on the conclusion. If the method or settings change after the final evaluation period has been inspected, that period can no longer be described as unused out-of-sample evidence.

If no complete version is available by a report deadline, the team presents the latest verifiable evidence that was available at the deadline and is evaluated only on that evidence. There is no additional percentage deduction added to the rubric; however, missing version, reproducibility, or timeliness evidence cannot receive credit. University rules or an approved accommodation take precedence when they require a different treatment.

### Presentation, comparison, and feedback

All students or teams in the same report receive the same presentation and question time. The exact schedule is published after enrollment and team membership are known. Report 1 common data, required prediction baseline, trading benchmarks, and settings are announced through GitHub by the start of Week 3. The final portfolio data, final period, costs, portfolio constraints, and required numerical settings are announced by the start of Week 11. Students are not evaluated against an unannounced setting.

A required common setting is not changed after the relevant freeze. If a correction is necessary before the freeze and fewer than seven calendar days remain, all teams receive the same corrected specification and a revised deadline. If a correctness problem is discovered after the freeze, the instructor preserves the affected evidence, documents the problem, and applies the same remedy to every team.

Students do not evaluate their own team. Individual reviews use the required common criteria and cite evidence from submitted or presented results. The instructor may publish the class median rank and the spread of ranks without student names to support discussion; these summaries do not alter team grades.

For comparison activities, access to GitHub materials follows the course repository permissions. Restricted raw data, individual rankings, and grading records are not made public. Only an anonymous class summary of individual rankings is returned to students.

Individual reviews, rankings, and other grading-related personal records submitted through GitHub must be placed in a private repository that the instructor can access. Do not place these records in a public team repository.

Report 1 rubric feedback is returned by the start of Week 11. Report 2 feedback and unresolved questions are returned by the start of Week 15 so teams can decide what to revise. Final report feedback and the anonymous class comparison summary are returned within the university's grade-reporting schedule.

Students who need an approved accommodation should use the university's official procedure. The reporting format or timing may be adjusted while preserving the assessed learning outcomes. An approved absence is handled through the same official process, and the instructor documents any alternative accepted under those rules.

## 指定教科書及參考書籍

The course does not require a single textbook. Weeks 2–8 primarily use instructor-prepared notebooks and open or authorized financial datasets. Weeks 11, 12, 13, 15, and 18 use papers related to weighting methods. Assigned papers and accessible versions will be announced through the course GitHub repository.
