# 智慧金融科技 — Project Context

> 本檔記錄此課程的專屬事實與規劃狀態；本 repository 的工作規則見
> `AGENTS.md`。

- 最後盤點日期：2026-08-27
- 課程狀態：115-1 課綱與18週結構已完成；12個實際授課週（Weeks 2–8、11–13、15與18）均已各自完成150分鐘改寫與重新驗證；正式共同資料、課堂環境與報告細則仍依下方待確認事項辦理
- Repository：`main` 已連接並推送至 `https://github.com/KennethWYLee/FinTech.git`
- 文件可見性：`course/` 為可公開學生教材；其他內容預設課程內部，papers、答案與行政文件需分層

## 課程專屬規則

- 保持本課為EMI與研究導向的Intelligent FinTech課程；教學順序改為
  先以AI量化交易建立共同實作經驗，再將模型與決策不穩定性轉化為研究問題。
- README、已封存的115-1 analysis、EMI 申請及歷年課綱是規劃證據集；未核定
  前不虛構 final syllabus、EMI ratio 或 learner prerequisites。
- 定稿週次前先確認核心 papers、可存取版本、reading order、source
  locators 與 distribution rights。
- `../AI量化交易/course_package/` 的學生、教師、行政與資料內容已於
  2026-08-23 複製至本課 `internal_materials/`；其四次課程安排、評量與期末
  提案仍不是本課的權威來源。
- 維持 teacher、student、admin 與 data access layers；zip、評量比重及
  rubrics 在狀態確認前不得視為 final。

## 課程定位

- 課程名稱：智慧金融科技／Intelligent FinTech
- 開課班級為資訊管理系碩士班二年甲班；正式的程式、統計與財務先備能力仍待確認，
  教材不得預設所有學生具有相同的財務或量化交易經驗。
- 本課為EMI課程；正式課綱的每週簡述使用完整中英文。逐週學生教材的英文比例與中文
  輔助方式尚待核定，不自行把課綱的雙語格式視為所有教材的固定語言比例。
- 115-1規劃以量化交易為起點，並串接AI in FinTech、model uncertainty、
  explainable AI、scenario generation、CVaR與data-driven/robust portfolio optimization。
- 本課以三次報告逐步發展學生成果：Week 10 quantitative trading report、
  Week 14 weighting method progress report，以及 Week 16–17 final report。
  Week 9 因教師出國不排課或報告。各組提出方法、英文報告與口頭問答，
  並以可追溯證據比較其他組的方法。
- 同儕收到的名次不直接決定該組成績；個人排序依證據品質評分，不自評。

## 課程用語記錄

- quantitative trading：量化交易。
- backtest：回測；look-ahead bias：前視偏誤；information leakage：資訊洩漏。
- out-of-sample evaluation：樣本外評估；不用「未來表現保證」之類超出證據的表述。
- model multiplicity：模型多重性；variable-importance uncertainty：變數重要性不確定性。
- scenario budget：情境預算；minimum-CVaR portfolio：最低CVaR投資組合。
- peer ranking：同儕排序；它是有證據的比較活動，不是以人氣決定組別成績。

## 評量決定

- Class participation and weekly learning evidence：40%。
- Report 1 — Quantitative Trading Report：20%，安排於 Week 10。
- Report 2 — Weighting Method Progress Report：15%，安排於 Week 14。
- Report 3 — Final Report：25%，Week 15 提交相同 frozen version，Week 16–17 分兩週報告。
- 本課不再安排兩次 individual technical examinations。

## 每週教材結構

- `course/01`至`course/18`對應18週；每個週次資料夾只放`weekN_main.md`與
  `weekN_support.md`兩個Markdown檔。
- Week 1的`week1_main.md`是完整課程大綱，不是第一週的逐步課堂教材；
  `week1_support.md`只保存課綱相關補充與全課共同規範連結。
- Weeks 10、14、16與17是報告週；逐週教材設計與改進程序直接略過這四週，除非教師
  另行要求，不建立或擴充為一般授課教材。
- Weeks 2–8、11–13、15與18的`main`是當週完整學生主教材，須包含可檢核學習目標、必要概念
  與公式、分析流程、必做步驟、預期結果、練習、證據與完成條件。
- `support`保存課前準備、資料字典、環境設定、檢查表、詳細除錯、延伸分析、文獻及
  其他repository資源連結，不與`main`重複同一份完整內容。
- notebook及其他跨週共用資源集中於`course/resources/`，由對應週次的`support`
  連結，不在各週資料夾複製。
- 公開學生教材不包含教師答案、隱藏測試、評分註記、學生資料、EMI行政文件或未確認
  再散布權利的資料與文章。
- Week 2以150分鐘完成價格欄位、簡單與對數報酬、累積報酬、資料品質、資料頻率及
  一期固定權重投資組合報酬；不提前進入Week 3的資訊時間與交易訊號設計。
- Week 2核心計算使用教材內明確標示的人工資料，避免在共同市場、資產、期間、欄位
  定義與發布權利核定前假裝使用真實市場證據。
- `course/resources/notebooks/00_Colab_課前環境與資料檢查.ipynb`只作Week 2
  環境、人工價格、報酬與圖形準備，不包含線上下載、特徵、target、訊號、模型或回測；
  舊版`course/resources/notebooks/01_TypeB_AI量化交易_問題設定與資料初探.ipynb`
  跨越多個現行週次，不是Week 2必做核心教材。
- Weeks 2–8、11–13、15與18的`main`原有連續120分鐘教學安排；依2026-08-23決定，這12週
  的正式目標均改為150分鐘，且已逐週獨立完成改寫與重新驗證。教材內的
  人工資料只用於計算、回測與最佳化機制練習，不作真實市場實證。各週`support`保存準備、
  紀錄模板、除錯、延伸與來源，不另造教師答案。
- Week 2的150分鐘版本已逐句完整檢查兩輪；所有六個主教材Python區塊、課前notebook、
  報酬與公司行動已知答案、資料錯誤情境、相對連結及圖形成品均已驗證。實際環境為
  Python 3.12.9、NumPy 2.2.6、pandas 2.2.3與matplotlib 3.10.8。
- Week 3的150分鐘版本已逐句完整檢查兩輪；五個主教材Python區塊已依學生閱讀順序執行，
  並另以不同日期完成發布時間對齊的已知答案與錯誤方向測試。課程時序、相對連結、公開內容、
  來源定位及Week 3資料夾檔案集合均已檢查；人工資料結果不代表真實市場實證。
- Week 4的150分鐘版本已逐句完整檢查兩輪；四個主教材Python區塊已依學生閱讀順序執行，
  手算ledger、獨立對齊修正及財富非正值失敗情境另經目標測試。正確lag、錯誤同期間對齊、
  終點部位規則、來源定位、相對連結與公開內容均已檢查；所有績效數值只屬固定人工路徑。
- Week 5的150分鐘版本已逐句完整檢查兩輪；四個主教材Python區塊已依學生閱讀順序執行，
  另驗證每10列重估與更新的獨立練習、目前target誤入training及隨機切割使用較晚資料的失敗情境。
  模型版本、training列數、最後training feature與target可觀察日期均已保存並檢查；預測結果只屬人工資料。
- Week 6的150分鐘版本已逐句完整檢查兩輪；五個主教材Python區塊已依學生閱讀順序執行，
  並另驗證basis-point換算、手算進出成本、equal-weighted共同條件比較、錯誤單位及不一致成本情境。
  成本grid、執行延遲、各方法turnover、terminal rule、來源定位及實際數值均已核對；4 bp仍只是人工假設。
- Week 7的150分鐘版本已逐句完整檢查兩輪；四個主教材Python區塊已依學生閱讀順序執行，
  並另驗證手算績效指標、獨立報酬路徑、報酬為負100%、無效block長度及零波動報酬等情境。
  固定人工路徑的財富、年化報酬、Sharpe ratio、Sortino ratio、maximum drawdown、drawdown
  duration、block bootstrap與重複選擇結果均已核對；bootstrap區間與績效估計不作母體保證。
- Week 8的150分鐘版本已逐句完整檢查兩輪；三個主教材Python區塊及support環境檢查已依學生
  閱讀順序執行，另驗證手算weights、weight drift、turnover、information lag、fallback、缺失與
  無限輸入、不可行cap及40% cap。原本在較低cap可能震盪的weight redistribution已修正並以多組
  極端raw inputs與可行cap測試；四種方法只在固定人工路徑與相同評估條件下比較，不作普遍排名。
- Week 11的150分鐘版本已逐句完整檢查兩輪；六個主教材Python區塊及support環境檢查已依學生
  閱讀順序執行，並驗證二資產covariance手算、global minimum variance、mean–variance、maximum
  Sharpe、efficient frontier、不可行target、constraint residuals、共同月度再平衡與成本及training
  window sensitivity。缺失、非對稱與非正半定covariance、不可行cap、無效penalty及零volatility等
  失敗情境均有目標測試；結果只屬固定人工training/test split與目前solver環境。
- Week 12的150分鐘版本已逐句完整檢查兩輪；七個主教材Python區塊及support環境檢查已依學生
  閱讀順序執行，並驗證sample、固定比例diagonal shrinkage、one-factor、trailing與rolling
  covariance，volatility contribution手算、equal及unequal risk budgets、hierarchical risk parity、
  solver residuals、共同月度再平衡與成本及covariance sensitivity。形狀不符、缺失、非對稱或
  非正半定covariance、無效risk budget、不可行cap、錯誤日期索引與無效成本等失敗情境均有目標
  測試；所有績效比較只屬固定人工training/test split，test covariance計算明確標示為事後診斷。
- Week 13的150分鐘版本已逐句完整檢查兩輪；七個主教材Python區塊及support環境檢查已依學生
  閱讀順序執行，並驗證empirical VaR與tail-loss convention、finite-scenario minimum-CVaR linear
  program、turnover limit、linear transaction cost、absolute-deviation regularization、solver objective
  components及residuals、scenario count與seed sensitivity、明示stress assumption及共同月度評估。
  空值、錯誤維度、無效alpha或cap、無效previous/reference weights、不可行turnover組合、錯誤日期
  索引與無效成本等失敗情境均有目標測試；測試期在所有設定固定前未用於計算，stress sensitivity
  不宣稱構成formal robust optimization，one-day scenario與monthly evaluation的期間差異亦已揭露。
- Week 15的150分鐘版本已逐句完整檢查兩輪；六個主教材Python區塊及support環境檢查已依學生
  閱讀順序執行，並將流程修正為只以development evidence修改方法與設定、先完成三類prespecified
  sensitivity checks與設定紀錄，再執行一次common final-period evaluation。原先提前列印final-period
  performance的資訊洩漏已移除；舊cap redistribution已換成能處理極端raw inputs的有限步驟分配。
  權重shape、label order、full investment、cap、information end、initial及drift turnover、basis-point
  cost、common dates、final-period rolling information與多種錯誤輸入均有目標測試；人工placeholder仍
  明確等同inverse volatility，不可當作學生自訂方法或真實市場證據。
- Week 18的150分鐘版本已逐句完整檢查兩輪；四個主教材Python區塊及support環境檢查已依學生
  閱讀順序執行，並驗證time-varying inverse-volatility weights、weight-difference threshold、
  information end、drift turnover、linear cost、兩個人工covariance conditions、common-date lookback
  sensitivity與cost sensitivity。錯誤資料型別、重複日期或欄位、缺失或小於等於-100%的returns、
  無效window、schedule、threshold、cost與evaluation start均有目標測試；教材明確區分事後condition
  label、real-time regime detection、一般sequential backtest、online portfolio selection、machine-
  learning allocation及distributionally robust optimization，並由已觀察限制發展可被證據反駁的研究問題。
- Weeks 2–8、11–13、15與18的逐句內容、公式、程式、預期結果、診斷步驟、練習、證據與
  結論限制已於2026-08-23分週反覆檢查及修正。這12週`main`的全部Python區塊與所需
  `support`檢查均先在各週獨立程序中完整執行，再以每份文件各自啟動的新Python程序重跑。
- 18個週次資料夾的檔案集合已通過結構檢查；12個150分鐘授課週另經時間表連續性、Markdown
  fence、數學分隔符、相對連結與錨點、公開內容及`git diff --check`檢查。Weeks 9、10、14、
  16與17依課程設計分別為停課或報告週，不適用150分鐘完整教材檢查。Markdown lint檢查只
  排除未列為本課規則的行長與表格欄寬樣式。
- 2026-08-27將15份公開教材中的數學分隔方式改為GitHub支援的格式，共139個行內公式及50個
  獨立公式。每份文件均經GitHub Markdown轉譯介面檢查，轉譯出的行內與獨立公式數量逐份符合
  原始文件；與前一版比較確認只更換數學分隔符，未修改公式內容或程式碼區塊。
- 以上結果只代表目前人工資料、程式版本及已定義檢查內沒有未解決的阻斷問題，不代表
  未核定的共同資料、正式Colab套件版本、真實市場結果或三次報告rubric已完成驗證。

## 金融資料、回測與方法比較規則

- 金融資料必須記錄來源、欄位定義、價格調整、報酬公式、頻率、交易日、缺失值處理、
  時間範圍、取得日期及允許的發布範圍。
- 每個特徵、標籤與交易決策都要說明觀察、發布、訊號、決策、執行與評估時間，避免
  前視偏誤與資訊洩漏。
- 回測須明確定義資產範圍、訓練與測試期間、訊號、部位、執行價格、持有期間、模型
  更新、訊號更新、再平衡、交易成本與限制。
- 訓練、驗證、測試與frozen out-of-sample結果須分開；不得用測試結果反覆修改方法後
  仍將其描述為未使用過的樣本外證據。
- 各組與各方法的比較使用相同資料、期間、交易成本、再平衡規則、投資限制與評估指標；
  方法特有的必要設定須另行揭露。
- 隨機程序須記錄seed、重複次數與結果變異；最佳化須記錄目標函數、限制、求解狀態
  與不可行或未收斂情形。
- 實證主張只限於實際資料、期間、假設與檢查能支持的範圍；預測準確度、單次最佳報酬
  或同儕名次均不能單獨證明方法具有投資價值。

## 本課教材的實作與驗證要求

- 正文、公式符號、程式變數、資料欄位、圖表標籤與報告指標必須一致；公式要定義
  符號、索引、單位與使用條件。
- 公開於GitHub的Markdown教材以`$...$`標示行內公式、以`$$...$$`標示獨立公式，使GitHub
  能將公式呈現為一般可讀的數學式；不使用GitHub頁面會直接顯示原始字元的`\(...\)`或
  `\[...\]`分隔方式。GitHub禁止的`\operatorname`不出現在公開教材；統計量與函數名稱使用
  GitHub可呈現的標準數學字體命令。公式內容、符號定義與教學精確性不得因格式轉換而省略。
- notebook的關鍵步驟須說明輸入資料與期間、當下可使用的資訊、計算或程式、預期的
  資料形狀或輸出、結果異常時的第一個診斷動作，以及需要保存的完成證據。
- 圖表須標示資產或投資組合、期間、頻率、單位、是否扣除交易成本與必要的比較基準。
- 學生練習應要求計算、預測、比較、解釋、審核、修改或除錯，不只重新執行範例；
  報告比較不得只使用單一報酬率或同儕人氣。
- 教材驗證須區分Markdown與相對連結、notebook JSON、程式語法與環境、小型已知答案、
  從乾淨狀態完整執行、指定資料與參數重現，以及共同樣本外條件下的方法比較。
- 只有實際執行的驗證層次才能標示通過；若受資料、套件、網路或授權限制，須記錄
  未驗證部分、原因與上課前需要完成的檢查。

## 權威文件與來源

- 跨課程通用的教材建立與驗證規則：`AGENTS.md`；`CLAUDE.md`保持相同內容
- 本課逐週教材的設計、改進與反覆驗證程序：`fintech_weekly_teaching_material_prompt.md`
- 課程入口：`README.md`
- 115-1過渡版本分析：`internal_materials/source_reference/1151_course_analysis_2026-08-05.md`；只作歷程證據，不是現行課程規劃
- 公開課程入口：`course/README.md`
- 115-1可編輯課綱：`course/01/week1_main.md`
- 115-1 EMI表格草案：`EMI申請/1151_Intelligent_FinTech_Quant_Research_Focused.docx`
- EMI 申請與設計：`EMI申請/`
- 歷年課綱：`internal_materials/historical_syllabi/`
- 教師、行政、資料與來源文件：`internal_materials/`
- AI量化交易內容整合紀錄：`internal_materials/AI量化交易_內容整合說明.md`
- 公開notebook：`course/resources/notebooks/`

## 與其他課程的邊界

- `../AI量化交易/` 保留整合前的完整來源；本課已取得 25 個可維護檔案，
  但仍需改寫為符合 18 週進度、EMI產出與目前評量方式的本課版本。
- `internal_materials/source_reference/` 應保存來源定位與授權資訊。
- `internal_materials/teacher/`、`internal_materials/student/`、
  `internal_materials/admin/` 與 `internal_materials/data/` 的存取層級需維持。
- `internal_materials/` 不是公開補充教材；只有完成學生版、答案、個資及
  授權檢查的檔案才可移入 `course/`。

## 待確認事項

- [ ] 核定115-1正式課綱、逐週學生教材的授課語言比例與學生先備能力。
- [ ] 選定Week 2開始使用的共同金融資料、資料字典、可公開範圍與取得方式。
- [ ] 核定學生執行環境、套件版本、資料保存方式與notebook完成證據。
- [ ] 定稿Problem 1的市場、資產、頻率、成本與frozen test period。
- [ ] 定稿Problem 2的FF49期間、scenario budget、CVaR信心水準與投資限制。
- [ ] 完成三次報告的詳細 rubric、課堂參與紀錄方式與排序證據規則。
- [ ] 選定核心papers、可存取版本與just-in-time reading order。

## GitHub 整理狀態（2026-08-23）

- 已建立獨立 Git repository，`main` 已推送第一個 commit 至 GitHub。
- Repository remote：`https://github.com/KennethWYLee/FinTech.git`。
- `course/01` 至 `course/18` 是公開學生教材的唯一目錄；課綱位於 `course/01/`。
- 18 週目錄、五份學生 notebook、Week 1 課綱與學生版 GenAI 規範已由 commit `6c739cd` 推送；未包含課程資料 CSV、教師答案或舊課程提交模板。
- 週次 main/support 結構、簡化後的課綱週次說明與集中管理的 notebook resources 已由 commit `7a06ce0` 推送。
- 三次報告與40%課堂參與的評量方式已由 commit `d8ff15b` 推送。
- 調整後的三次報告時程與18週完整中英文課綱已由 commit `e635191` 推送。
- 每週教材資料夾已由 `week1` 至 `week18` 改為 `01` 至 `18`，並由 commit `dd38014` 推送。
- 通用教材規則與本課專屬規則的分工已由 commit `26f3e5b` 推送。
- `PROJECT.md` 同時記錄本課程資料夾、固定決策、權威來源、GitHub 狀態與公開限制，不再另外維護 `COURSES.md`。
- `internal_materials/` 受 `.gitignore` 排除，尚未加入 Git 追蹤或推送；EMI 行政檔、papers、答案與資料檔待檢查後逐項決定。
- EMI 申請及個人文件不得放入 `course/`、不得 stage，也不得推送至 GitHub。
- 每次推送前檢查 staged files、Git history、個資、答案、credentials、第三方授權與 GitHub 檔案大小限制。
