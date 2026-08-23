# 智慧金融科技 — Project Context

> 本檔記錄此課程的專屬事實與規劃狀態；本 repository 的工作規則見
> `AGENTS.md`。

- 最後盤點日期：2026-08-23
- 課程狀態：115-1 課綱與18週結構已完成，準備逐週設計學生教材
- Repository：`main` 已連接並推送至 `https://github.com/KennethWYLee/FinTech.git`；目前公開提交為 `dd38014`
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
- Week 2至Week 18的`main`是當週完整學生主教材，須包含可檢核學習目標、必要概念
  與公式、分析流程、必做步驟、預期結果、練習、證據與完成條件。
- `support`保存課前準備、資料字典、環境設定、檢查表、詳細除錯、延伸分析、文獻及
  其他repository資源連結，不與`main`重複同一份完整內容。
- notebook及其他跨週共用資源集中於`course/resources/`，由對應週次的`support`
  連結，不在各週資料夾複製。
- 公開學生教材不包含教師答案、隱藏測試、評分註記、學生資料、EMI行政文件或未確認
  再散布權利的資料與文章。

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
- `PROJECT.md` 同時記錄本課程資料夾、固定決策、權威來源、GitHub 狀態與公開限制，不再另外維護 `COURSES.md`。
- `internal_materials/` 受 `.gitignore` 排除，尚未加入 Git 追蹤或推送；EMI 行政檔、papers、答案與資料檔待檢查後逐項決定。
- EMI 申請及個人文件不得放入 `course/`、不得 stage，也不得推送至 GitHub。
- 每次推送前檢查 staged files、Git history、個資、答案、credentials、第三方授權與 GitHub 檔案大小限制。
