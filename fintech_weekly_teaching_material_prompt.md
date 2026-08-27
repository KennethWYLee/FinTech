# 智慧金融科技逐週教材設計與改進 Prompt

請依照下列程序，建立、檢查及改進「智慧金融科技／Intelligent FinTech」的逐週教材。
本 prompt 用於維護現有課程，不自行改變正式課綱、評量、報告週次、資料來源、學生
先備能力或公開範圍。一次完整處理一個教學週；完成該週的範圍核對、來源查核、教材、
範例、練習與驗證後，再處理下一個教學週。

本程序沿用「資料庫管理」課程教材 prompt 的完整範圍核對、範例與練習對齊、逐句檢查、
實際執行及反覆修正架構；課程範圍、金融有效性檢查、檔案分工與評量銜接則依本課重新設計。

目前需要完整150分鐘授課教材的週次依序為 Weeks 2–8、11–13、15與18。Week 1是完整
課程大綱；Week 9因教師出國不授課；Weeks 10、14、16與17是報告週。執行本 prompt 時
直接略過報告週，不建立、擴充或逐句檢查其報告文件；報告目的只用來反向核對先前教學週
是否提供足夠準備。除非使用者另外指定，不得把報告週改成一般授課週或納入教材改寫範圍。

## 一、固定課程結構與正式範圍

以`course/01/week1_main.md`的現行課綱為正式週次範圍，以`PROJECT.md`記錄的教師決定
與未決問題為限制。開始逐週改寫前，先比較課綱與現有教材；如果現有`main`或`support`
比課綱窄、比課綱廣或使用不同方法，不得靜默決定，必須列出差異、受影響檔案及需要
教師確認的選項。

| 週次 | 正式方向 | 該週主要成果 | 不得提前取代的後續內容 |
|---:|---|---|---|
| 1 | Course orientation and syllabus | 完整中英文課綱、學習成果、評量與三次報告說明 | 不改寫成150分鐘技術教材 |
| 2 | Financial data and returns | 可稽核的價格、報酬、頻率與一期投資組合報酬計算 | 不進入交易訊號、模型或回測績效 |
| 3 | Information timing, features, and signals | 從金融假說建立point-in-time feature與signal | 不在尚未定義執行與成本前宣稱策略績效 |
| 4 | Basic backtesting | 從signal到position、trade、return與wealth的有效ledger | 成本敏感度與完整績效評估留待Weeks 6–7 |
| 5 | Chronological evaluation and updating | fixed、rolling、expanding與walk-forward比較；區分model、signal與rebalance更新 | 不以同一最終期間反覆選擇設定 |
| 6 | Costs, execution, and benchmarks | 同日期、同成本條件下的net backtest、delay，以及cash、buy-and-hold、rule-based與equal-weight benchmarks比較 | 統計不確定性與多重選擇留待Week 7 |
| 7 | Performance and backtest validity | 報酬、風險、回撤、週轉率、不確定性、overfitting與audit | 不用單一指標或單一路徑判定方法普遍較好 |
| 8 | Basic portfolio weighting | 在相同資訊、再平衡、成本及樣本外條件下，比較equal、market-cap、signal-based及inverse-volatility weights | 不提前取代後續最佳化與風險配置方法 |
| 9 | Instructor travel | 無正式授課或必要報告 | 不新增必做活動 |
| 10 | Report 1 | 全員報告quantitative-trading solution及初步weighting approach | 本 prompt 直接略過，不建立或改寫報告教材 |
| 11 | Mean–variance weighting | expected return、covariance、minimum variance、mean–variance、maximum-Sharpe與weight instability | 不把in-sample frontier當成out-of-sample證據 |
| 12 | Covariance and risk-based weighting | sample、shrinkage、factor與rolling covariance，以及equal risk contribution、risk budgeting與hierarchical risk parity | 不用不同資料或限制比較方法 |
| 13 | Downside risk and minimum-CVaR | VaR、CVaR、scenario-based optimization、regularization、constraints、turnover limits、cost penalties與robust portfolio decisions | 不把scenario estimate當成最大可能損失或未來保證 |
| 14 | Report 2 | weighting-method limitation、proposed modification、definition、implementation與初步證據 | 本 prompt 直接略過，不建立或改寫報告教材 |
| 15 | Method revision and freeze | 在共同樣本外條件下檢查績效、交易成本、集中度、週轉率、robustness、reproduction與frozen final version | final period若已用於修改，不再稱為untouched evidence |
| 16 | Final report, session 1 | 第一批團隊依共同條件報告 | 本 prompt 直接略過，不建立或改寫報告教材 |
| 17 | Final report, session 2 | 其餘團隊報告、evidence-based comparison與peer ranking | 本 prompt 直接略過，不建立或改寫報告教材 |
| 18 | Advanced issues and research questions | 從已觀察限制形成可反駁問題、簡單比較與後續驗證方式 | 不把未實作的進階方法寫成已證實結果 |

課綱列出的內容若超過150分鐘可合理教授的分量，先指出衝突並提出「保留於主要教學」、
「移至support作延伸」或「由教師調整課綱」的選項。未取得教師決定前，不自行刪除課綱
內容，也不把它假裝成已完整教授。

## 二、開始工作前必讀

開始任何週次前，依序完整閱讀：

1. repository根目錄的`AGENTS.md`。
2. repository根目錄的`PROJECT.md`。
3. `course/01/week1_main.md`及`course/01/week1_support.md`。
4. 目標週的`weekN_main.md`與`weekN_support.md`。
5. 前一個實際授課週及下一個實際授課週的`main`與`support`，確認先備能力與後續依賴。
6. 與目標週直接相關的公開notebook、程式、資料字典、生成來源與驗證紀錄；只有實際存在
   的檔案才使用。
7. `internal_materials/source_reference/`中與該週相關的來源定位與授權紀錄。
8. 需要用到的完整核心paper、正式技術文件或資料提供者文件，不得只讀搜尋摘要、引用
   片段或二手介紹。
9. Git工作樹與目前差異；既有未提交變更不得覆寫或回復。

`internal_materials/`、EMI申請、歷年課綱、教師答案、行政文件及受限制papers可供教師端
查核，但不得複製到公開的`course/`。舊版AI量化交易notebook只能作為來源或活動參考，
不得把其舊課程週次、評量或未核定用語直接移植成本課正式內容。

## 三、編輯前先逐項核對正式內容

在修改學生教材前，先建立一張逐項核對表。每一列只能放一個實際要教的概念、公式、
資料決策、程式操作、金融判讀或研究能力，至少包含：

- 課綱中的對應句子。
- `PROJECT.md`中的相關限制或未決問題。
- paper章節、頁碼、公式、圖表、官方文件或資料提供者頁面的精確位置。
- 現有`main`講解、公式、範例及活動的位置。
- 現有`support`準備、記錄、除錯、延伸及來源的位置。
- 學生完成後可觀察或檢查的行為。
- 完整範例的位置。
- 學生自行完成的練習或操作。
- 預期結果、判讀方式及結果不同時的第一個安全診斷動作。
- 要保存或繳交的個人證據。
- 與Class participation、Report 1、Report 2或Final Report的關係；沒有關係時明確寫無。
- 目前狀態：完整、部分、缺少、衝突或需要教師決定。

任何正式內容若缺少講解、完整範例、學生練習、預期結果、診斷或完成證據，均視為尚未
完成。先補齊缺口，再增加延伸內容。

## 四、每個正式內容都必須有講解、範例與學生工作

每個正式教學內容至少同時具備：

1. 以學生可自行閱讀的英文完整說明用途、定義、條件及常見誤解，不只給名詞或條列。
2. 一個從輸入、假設、計算到金融解釋都完整呈現的worked example。
3. 範例中每個資料欄位、時間索引、公式、程式步驟、權重或限制都有解釋。
4. 一個不同於範例的學生練習，要求預測、計算、修改、比較、解釋、審核或除錯。
5. 學生執行前先記錄預測；執行後必須以輸出或證據修正原判斷。
6. 明確的預期形狀、數值關係、輸出現象、constraint或solver status。
7. 結果不同時先檢查哪一個輸入、時間邊界、單位、資料列、公式或程式狀態。
8. 明確的保存證據與完成條件。
9. 適用時提供錯誤案例、反例、失敗情境或不可行狀態，並解釋為何錯誤。
10. 實證結論只限於實際資料、期間、假設、參數、seeds及檢查能支持的範圍。

worked example可以揭示完整過程與結果，但其後的必做練習必須使用不同數值、不同日期、
不同假設或不同故障情境，不能把可直接繳交的答案放進學生教材。討論活動不能取代完整
講解、範例或實際操作。

## 五、不同金融主題的最低範例要求

- Financial data and returns：提供小型價格表、欄位定義、日期索引、簡單與對數報酬、
  compounding、frequency conversion及資料品質檢查；adjusted price的意義必須依資料提供者
  定義，不能當成通用欄位規則。
- Information timing and features：提供observation、publication、signal、decision、
  execution與evaluation time；至少比較一個有效feature與一個故意使用未來資訊的錯誤例子。
- Backtesting：提供逐列ledger，清楚連接feature、signal、position、trade、asset return、
  strategy return與wealth；同時展示有效lag與invalid same-period alignment。
- Rolling and walk-forward evaluation：對一個prediction date逐列指出可用training rows、
  尚未發生的target及preprocessing fit範圍；分開model refit、signal update與portfolio rebalance。
- Costs and execution：手算一筆entry或exit的commission、half-spread、slippage與turnover；
  cost grid中的candidate與benchmark必須使用同一成本率，delay比較須重新計算自己的turnover。
- Performance：使用可手算的小型return sequence檢查annualized quantities、Sharpe、Sortino、
  maximum drawdown及duration；最大回撤的running peak必須包含初始wealth 1。
- Basic weighting：從raw input到normalized weight逐步計算，檢查finite、sum-to-one、long-only、
  cap與fallback；清楚區分target weights、實際持有權重及return後weight drift。
- Mean–variance：先用二資產例子計算covariance effect，再建立完整optimization objective、
  units、constraints、feasibility、solver status及test-period evidence。
- Covariance and risk allocation：比較同一training sample的covariance estimates，保留eigenvalue、
  condition number、asset order及risk-contribution identity；clustering與solver輸出須可重現。
- VaR, CVaR, and scenarios：先手算一個有限loss sample的quantile與tail convention，再逐步查核
  linear-program objective、scenario constraint、scenario budget、seed、stress assumption及
  out-of-sample evaluation。
- Student-developed weighting method：從一個已觀察限制開始，明確定義input、equation、settings、
  constraints、fallback及failure condition；不得只替既有方法換名稱。
- Advanced research question：先指出已觀察證據，再寫可被結果反駁的問題；保持相同data boundary、
  comparator及outcome，先設計最簡單可信比較，再說明複雜方法增加了哪一項可檢驗解釋。

## 六、金融資料、時間與實證主張查核

使用任何資料前，逐項記錄並檢查：

- 資料來源、取得日期、授權及可再發布範圍。
- 市場、資產、欄位、幣別、頻率、時區、交易日與日期範圍。
- raw、close、adjusted或total-return欄位的提供者定義。
- corporate actions、missing values、duplicates、non-trading days與frequency conversion。
- return公式、單位、annualization、cash或risk-free return假設。
- observation、publication、signal、decision、execution及evaluation time。
- training、validation、test與frozen out-of-sample期間。

共同金融資料仍未核定時，使用清楚標示的人工資料教授機制，不自行選定市場、ticker、期間、
資料供應商或宣稱真實市場結果。人工資料的輸出只能支持計算與方法機制，不能用來排名真實
投資方法。

每個feature與target都要回答：最後使用哪一筆資訊、何時可取得、預測哪個holding horizon，
以及該target何時才可進入training data。scaler、imputer、feature selection、hyperparameter及
threshold只能用當時允許的training或validation資料估計。

## 七、回測、機器學習與方法比較查核

逐行檢查回測與model code，確認：

- signal不能賺到signal形成前已結束的return。
- 初始missing feature、missing prediction及cash position的處理明確。
- position、trade、turnover、gross return、cost、net return及wealth的會計一致。
- entry、exit、terminal liquidation、cash return、short selling、leverage及weight drift明確。
- 交易成本單位與basis-point conversion只轉換一次。
- 同一比較中的data、dates、information、constraints、rebalancing、costs及metrics一致。
- model prediction accuracy與portfolio decision quality分開報告。
- 所有嘗試過的model、window、threshold及parameter有記錄，不只留下最好結果。
- final period一旦用於選擇、修正或除錯，便不能繼續稱為untouched evidence。
- block bootstrap、Monte Carlo或其他隨機程序記錄seed、repetitions及結果分散。
- 多次測試、data snooping、market-period sensitivity與失敗條件沒有被省略。

比較結論必須說明使用的判準。單一最高return、最高Sharpe、最低CVaR、最低prediction error或
peer rank都不能單獨證明一個方法具有普遍投資價值。

## 八、權重、最佳化與風險方法查核

每個weighting method至少記錄：

- financial purpose及所處理的已知限制。
- required inputs、units、lookback及availability time。
- equation、normalization、constraints及asset order。
- zero、missing、nonfinite或不可normalise input的處理。
- budget、long-only、short-selling、leverage、cap及turnover constraints。
- rebalance schedule、weight-to-return lag及transaction costs。
- baseline methods與共同比較條件。
- 失敗或不可行時的明確訊息，不得silently clip或renormalize錯誤輸出。

使用optimizer時另須保存objective、parameter units、solver、method、tolerance、status、message、
constraint residuals及failed runs。minimum variance、mean–variance、risk contribution、CVaR及
robust optimization的公式必須與code完全一致。

使用scenario generation時另須保存training sample、sampling rule、replacement、scenario budget、
seed、repetitions及stress scenarios。不得因較大scenario budget在一次實驗中表現較穩定，就宣稱
它修正了錯誤的distribution assumption。

## 九、兩個學生檔案的角色與150分鐘設計

每個週次資料夾只能包含`weekN_main.md`與`weekN_support.md`。若需要共用notebook、圖、資料或
script，放在`course/resources/`適當位置並由support連結；不得在週次資料夾新增第三個檔案。

`main`是學生在沒有教師投影片時仍可依序閱讀、重現與完成的完整教材，至少包含：

- week title與本週核心金融問題。
- 與前一授課週及下一授課週的關係。
- observable outcome與5個左右可檢核learning objectives。
- 課前條件及需要的環境。
- 時段連續、無重疊且合計150分鐘的class plan。
- 完整概念講解、公式、符號、單位及成立條件。
- 每個正式內容對應的worked example。
- 完整可執行的code或明確標示依賴與插入位置的片段。
- 每個關鍵步驟的expected evidence與first diagnostic action。
- 至少兩個需要學生預測、計算、修改、比較、解釋、審核或除錯的活動。
- common mistakes、counterexample、failure condition及limits of conclusions。
- required evidence、submission items、completion criteria與exit evidence。

`support`只保存main不宜重複展開的內容：

- before-class preparation與package check。
- data dictionary、assumption record或reproduction template。
- 詳細troubleshooting與required checks。
- optional extensions；不得混入必做要求。
- authoritative papers、official software documentation及source-scope notes。
- repository中實際存在的notebook、data或其他資源連結。

support不得變成第二份完整main，也不得包含教師講稿、完整練習答案、hidden test、grading notes、
學生資料或未公開結果。

## 十、三次報告與每週學習證據對齊

教材須支援已核定的評量結構，但不得自行創造詳細rubric或改變配分：

- Class participation and weekly learning evidence：40%。
- Report 1 — Quantitative Trading Report：20%，Week 10。
- Report 2 — Weighting Method Progress Report：15%，Week 14。
- Report 3 — Final Report：25%，Week 15提交frozen version，Weeks 16–17報告。

逐週檢查：

- 每個learning objective至少有一段教學、一個範例及一個可保存的個人證據。
- Week 2–8的證據能逐步支持Report 1，而不是在Week 10才第一次要求。
- Weeks 8、11–13的證據能支持Report 2的方法選擇、數學定義與初步實作。
- Week 15能把Report 2 feedback轉為一個可追蹤修改，並建立相同frozen version供Weeks 16–17報告。
- report weeks要求所有學生口頭參與，但不把同一團隊內容重複計分為個人技術成果。
- peer ranking必須使用共同金融條件與可追溯證據；rank本身不直接決定團隊成績，學生不自評。
- 尚未核定的detailed rubric、共同資料與數值設定標示pending，不得補造。

## 十一、來源、術語與著作權

每個technical term或research claim須有適當權威來源。金融與學術主張優先使用完整paper、
正式學術出版頁或disciplinary standard；Python、API及資料格式使用官方documentation。
不得使用搜尋摘要、購物頁、未驗證blog或AI產生文字作為正式證據。

引用paper時記錄author、year、title、journal、volume、issue、pages、DOI或穩定locator，並說明
該來源實際支持哪個定義或主張。課堂簡化實作若未重現paper的完整method、data或inference，
必須明確寫出。

不得自行創造、改名、縮寫或翻譯project-specific concept、method、metric、stage、role、feature、
category或technical label。只有權威來源以相同意義使用的標準術語可直接使用；需要本課特有
用語時，先把exact wording、definition、meaning、translation及scope交給教師核准，再記錄於
`PROJECT.md`。在取得核准前，改用一般、直接的描述性語句說明實際做法，不得把該描述排版、
大寫、加引號、縮寫或反覆使用成另一個看似正式的名稱，也不得用另一個自創名詞取代原詞。
現行學生教材維持英文；中文輔助比例未核定前，不自行加入新的術語翻譯。

教材以自己的文字及自行建立的教學例子說明，不大量複製paper、教科書、投影片、圖表、
程式或答案。第三方資料、圖片、code及文章在放入`course/`前須確認授權與必要標示。

## 十二、初稿完成後逐句檢查

逐句檢查所有學生可見內容，包括title、heading、paragraph、bullet、table、caption、formula、
activity、prompt、code comment、expected output、troubleshooting及exit evidence。每一句至少檢查：

1. 是否正確且可由來源、公式、資料或實際輸出支持。
2. 是否使用已核定或有權威來源支持的術語。
   若不是，是否已改成一般、直接的描述性語句，而沒有形成另一個未核定名稱。
3. 主詞、動詞、比較對象、時間順序及因果關係是否精確。
4. symbol、index、unit、parameter、field、variable及table label是否一致。
5. 是否把人工資料、單一路徑、單一seed或單一period過度推廣。
6. 是否遺漏data boundary、execution、cost、constraint或failure condition。
7. 是否與code、formula、table、figure、expected evidence及其他週次一致。
8. 是否預設了尚未核定的finance、statistics、programming或English能力。
9. 學生能否在沒有教師口頭補充時理解、執行、診斷及保存證據。
10. 是否與learning objective、worked example、student activity、report及completion criteria對齊。
11. 是否不必要重複main與support，或增加與本週核心問題無關的負擔。

發現問題時先修正來源檔，再重新執行受影響檢查。逐句檢查至少重複一次；第二輪必須使用
修正後的完整檔案，不能只看已修改的句子。

## 十三、程式、資料與成品驗證

完成逐句檢查後，依實際內容執行適用的驗證：

- 確認18個週次資料夾各自只有指定的main與support。
- 檢查Markdown fences、headings、tables、relative links、anchors及resource paths。公開於GitHub的
  Markdown以`$...$`標示行內公式、以`$$...$$`標示獨立公式；不得使用GitHub頁面不會依本課
  預期呈現的`\(...\)`、`\[...\]`分隔方式或GitHub禁止的`\operatorname`。核對每一對數學
  分隔符、公式在GitHub上的閱讀順序，以及實際頁面有無受限巨集錯誤或未轉譯的原始公式。
- 對notebook執行JSON、cell order、dependency及clean execution檢查。
- 在新的Python process或乾淨kernel中，依學生閱讀順序執行同一檔案的全部code blocks。
- 記錄Python與package versions；目前本機成功版本不能自動變成正式課堂版本。
- 讀取實際輸出，逐項核對教材聲稱的shape、count、identity、constraint、solver status及financial result。
- 為return compounding、lag、cost、turnover、drawdown、weight cap、risk contribution、CVaR及其他核心
  公式建立小型已知答案或target tests。
- 測試missing、duplicate、nonfinite、zero denominator、infeasible constraints、failed solver及其他相關
  edge cases。
- 若有figure、diagram、slide或PDF，實際render並檢查title、axis、period、frequency、unit、cost status、
  benchmark、文字重疊及可讀性。
- 檢查學生版本沒有answer key、teacher note、hidden test、grades、personal data、credentials、private path、
  restricted paper或未授權資料。
- 檢查external source locator；受publisher防機器人限制時，另核對DOI metadata或author repository，
  並如實記錄無法自動存取的部分。
- 執行`git diff --check`並完整檢閱Git差異；確認modified files符合本次allow-list且沒有意外staged files。

exit code 0只表示程式結束，不能取代known-answer check、output inspection或financial interpretation。
無法執行的內容須列出檔案、原因、未驗證範圍及上課前的檢查方式，不得推定為通過。

## 十四、反覆修正與完成條件

重複「範圍核對、來源查核、逐句檢查、修正、完整執行、target test、輸出核對、Git差異檢閱」，
直到下列條件全部成立：

- 課綱每一項正式內容都在逐項核對表中有明確狀態。
- 每項本週正式內容都有講解、worked example、學生練習、expected evidence、diagnostic及completion evidence。
- main與support角色清楚，且週次資料夾沒有第三個檔案。
- 150分鐘class plan連續、無重疊、份量足夠且能合理完成。
- 核心technical term與research claim均有適當來源。
- data、time、formula、code、output及financial interpretation沒有已知矛盾。
- 所有可合理執行的code與target tests均通過，輸出已人工核對。
- comparisons使用共同條件，unsupported conclusion已刪除或限縮。
- learning objectives、activities、weekly evidence及三次reports逐步對齊。
- 學生能自行閱讀、重現、診斷與完成必做內容。
- 沒有答案洩漏、個資、credentials、授權或公開範圍問題。
- `AGENTS.md`與`PROJECT.md`要求的適用檢查均已完成。
- 沒有尚未處理的blocking error。

「完成」或「converged」只表示已定義檢查在目前版本與目前範圍沒有發現blocking issue，不是
正確性的證明，也不代表未核定資料、環境、rubric或真實市場測試已完成。

## 十五、逐週交付與全課整合

完成一週後，先提供該週紀錄，至少包含：

1. 建立或修改的檔案。
2. 課綱要求、實際教授及移至support的內容。
3. 每個正式內容的講解、worked example、student activity及expected evidence位置。
4. 實際查核的papers、official documentation、data definitions及repository resources。
5. 實際執行的code、environment、target tests及結果。
6. 發現並修正的主要問題。
7. 未執行或仍需教師決定的項目。
8. 該週與前後週及報告的銜接。
9. 是否符合進入下一週的條件。

若本次任務只核准一週，完成後停止。若使用者明確要求完成全部教材，則依序處理Weeks 2–8、
11–13、15與18；每週通過後才進入下一週。全部完成後，再進行一次跨週整合檢查，確認：

- terminology、notation、units、field names及code conventions一致。
- financial data到backtest、performance、weighting、optimization及research question的學習順序連續。
- 同一benchmark、cost、turnover、drawdown、annualization、constraint及out-of-sample用法不互相矛盾。
- Week 10、14、16–17的report requirements確實由先前教材逐步準備。
- Week 18只從學期已觀察限制發展研究問題，不新增未準備的必考技術內容。

除非使用者明確要求，不自行commit、push、發布、建立外部資源或移動private materials。

## 十六、每次使用本 prompt 時的任務輸入

開始前，先根據使用者要求及現有檔案填寫；未知項目寫`pending`，不得猜測：

```text
Target week or scope:
Task type: audit / revise / create / verify
Student-facing language decision:
Approved data source and redistribution status:
Approved execution environment and package versions:
Approved market, assets, frequency, and periods:
Approved costs, rebalancing, and portfolio constraints:
Approved report requirements or rubric:
Required papers or official sources:
Files allowed to change:
Files that must remain private:
Known unresolved decisions:
Requested Git action: none / commit / push
```

若任一`pending`項目會實質改變教材、實證結果、評量或公開範圍，先完成不依賴該決定的查核與
改進，再明確列出blocker及需要教師決定的內容；不得用自行假設掩蓋缺失。
