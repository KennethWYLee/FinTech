# Artificial Intelligence in FinTech — Course Statement and GenAI Policy

## English version

### Educational-use statement

All notebooks, examples, strategies, backtests, and discussions in this course are for educational purposes. They are not investment advice, a solicitation, a research report, or a trading instruction.

Students should not use a classroom example to trade real money. A real investment decision remains the investor's responsibility and may require advice from a qualified professional.

### Limits of backtest evidence

Backtest results do not represent future performance. Classroom results may be affected by information leakage, look-ahead bias, survivorship bias, underestimated transaction costs, slippage, liquidity constraints, overfitting, parameter selection, changes in market conditions, short samples, and unrealistic execution assumptions.

A high return, high Sharpe ratio, or small drawdown in class is research evidence under the stated conditions. It is not a guarantee of profit.

### Permitted and prohibited uses of GenAI

GenAI may assist learning, but it may not replace the student's judgment or verification.

Permitted uses include:

- drafting a strategy hypothesis;
- explaining a programming error message;
- organizing a feature dictionary;
- revising overconfident wording; and
- generating questions or a list of possible risks for later verification.

Prohibited uses include:

- submitting GenAI output as a final answer without review;
- concealing material GenAI use;
- fabricating data, performance, execution records, or literature;
- presenting generated investment advice as reliable; and
- using generated code or a financial explanation without checking it.

The student or team remains responsible for every submitted calculation, source, line of code, interpretation, and oral answer. Do not enter credentials, personal data, restricted data, or another student's work into a GenAI service.

### Required disclosure

If GenAI affects a submitted report, code file, figure, table, or presentation, include:

```text
Tool used:
Purpose:
Main prompt:
Content retained:
Content removed or changed:
Human verification performed:
```

The disclosure is not a substitute for verification. During oral questions, every student must be able to explain the submitted work without asking GenAI to answer in real time.

### Language and responsibility

Avoid claims such as:

```text
This strategy guarantees profit.
The model proves this market pattern.
The AI found the best strategy.
This result will continue in the future.
```

Use statements whose strength matches the evidence, for example:

```text
The evidence in this sample suggests ______.
The result is conditional on ______.
The strategy remains fragile when ______.
Before deployment, we need ______.
```

### Minimum risk disclosure

Every strategy report must identify:

- the data source and period;
- the target and holding period;
- the benchmark;
- transaction-cost assumptions;
- maximum drawdown or another relevant downside-risk measure;
- whether information leakage was checked;
- whether cost or threshold sensitivity was examined;
- the parts affected by GenAI; and
- why the classroom evidence is insufficient for direct real-money trading.

The goal is not to find a strategy that is certain to earn a profit. The goal is to formulate a hypothesis, construct data and a model, check bias, backtest and stress test, disclose limitations honestly, and make a decision whose reasoning can be traced to evidence.

## 中文版本

### 教育用途聲明

本課程所有 notebook、範例、策略、回測與討論皆僅供教育用途，不構成任何投資建議、投資招攬、研究報告或交易指示。

學生不應根據課堂範例直接進行真實資金交易。任何真實投資決策皆需自行承擔風險，並諮詢合格專業人士。

### 回測限制聲明

回測結果不代表未來績效。課堂回測可能受到下列因素影響：

- data leakage
- look-ahead bias
- survivorship bias
- transaction cost underestimation
- slippage and liquidity constraints
- overfitting
- parameter selection bias
- regime change
- short sample period
- unrealistic execution assumptions

因此，課堂上看到的高報酬、高 Sharpe 或低 drawdown，都只能視為研究 evidence，不能視為獲利保證。

### GenAI 使用原則

本課程允許使用 GenAI，但 GenAI 的角色是輔助，不是替代判斷。

允許用途：

- 產生策略假說草稿。
- 解釋程式錯誤訊息。
- 協助整理 feature dictionary。
- 幫助改寫過度自信的文字。
- 產生質詢問題或風險清單。

不允許用途：

- 直接把 GenAI 文字當成最終答案。
- 隱藏 GenAI 使用。
- 讓 GenAI 編造不存在的資料、績效或文獻。
- 用 GenAI 生成投資建議並宣稱可靠。
- 未經檢查就採用 GenAI 產生的程式碼或金融解釋。

學生或團隊仍須對每一項提交的計算、來源、程式碼、解釋與口頭回答負責。不得將帳號憑證、
個人資料、受限制資料或其他學生的作品輸入 GenAI 服務。

### GenAI 使用揭露格式

若 GenAI 影響提交的報告、程式碼、圖、表或簡報，需附上：

```text
使用工具：
使用目的：
主要 prompt：
採納內容：
刪除或修改內容：
人工檢查方式：
```

揭露不能取代人工查證。口頭問答時，每位學生都必須自行說明提交內容，不得即時要求 GenAI 代答。

### 語言與責任

課程提案與簡報中應避免下列語句：

```text
This strategy guarantees profit.
The model proves this market pattern.
The AI found the best strategy.
This result will continue in the future.
```

建議改成：

```text
The evidence in this sample suggests ______.
The result is conditional on ______.
The strategy remains fragile when ______.
Before deployment, we need ______.
```

### 最低風險揭露要求

每份策略提案至少要揭露：

- 資料來源與時間範圍。
- target 與 holding period。
- benchmark。
- transaction cost 假設。
- 最大回撤或 downside risk。
- 是否做過 leakage 檢查。
- 是否做過成本或 threshold sensitivity。
- 使用 GenAI 的部分。
- 不建議直接真實交易的原因或條件。

### 課堂共識

這門課的目標不是找出「一定賺錢」的策略，而是訓練學生建立一種負責任的金融 AI 判斷能力：

```text
提出假說
-> 建立資料與模型
-> 檢查偏誤
-> 回測與壓力測試
-> 誠實揭露限制
-> 用 evidence 做決策
```
