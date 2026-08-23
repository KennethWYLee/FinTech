# 智慧金融科技 課程聲明與 GenAI 使用規範

## 教育用途聲明

本課程所有 notebook、範例、策略、回測與討論皆僅供教育用途，不構成任何投資建議、投資招攬、研究報告或交易指示。

學生不應根據課堂範例直接進行真實資金交易。任何真實投資決策皆需自行承擔風險，並諮詢合格專業人士。

## 回測限制聲明

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

## GenAI 使用原則

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

## GenAI 使用揭露格式

學生報告中若使用 GenAI，需附上：

```text
使用工具：
使用目的：
主要 prompt：
採納內容：
刪除或修改內容：
人工檢查方式：
```

## 語言與責任

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

## 最低風險揭露要求

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

## 課堂共識

這門課的目標不是找出「一定賺錢」的策略，而是訓練學生建立一種負責任的金融 AI 判斷能力：

```text
提出假說
-> 建立資料與模型
-> 檢查偏誤
-> 回測與壓力測試
-> 誠實揭露限制
-> 用 evidence 做決策
```
