# 修改紀錄：earnings-reviewer → earnings-reviewer-tej

改編自 `anthropics/financial-services` 的 `plugins/agent-plugins/earnings-reviewer/`，用於台股投顧報告場景，資料源改接 TEJ MCP。

## 已修改的檔案

| 檔案 | 改動 | 原因 |
|---|---|---|
| `.claude-plugin/plugin.json` | 改名、改描述 | 標示這是 TEJ 移植版 |
| `.mcp.json`（新增，原本沒有，連接器定義在 financial-analysis 核心） | 只留 TEJ MCP 一個 server | 原版 11 個外部連接器（Daloopa/FactSet/Morningstar/S&P Global/Moody's/MT Newswires/Aiera/LSEG/PitchBook/Chronograph/Egnyte/Box）皆為西方資料商，台股投顧報告用不到 |
| `agents/earnings-reviewer.md` | tools 白名單從 `mcp__factset__*, mcp__daloopa__*` 換成 `mcp__tejapi__*` + `WebSearch`；workflow 步驟改寫；新增評等護欄（不自生成評等，呈現 TEJ 共識評等）；新增 `TEJ_GAPS.md` 記錄機制 | 核心接線 |
| `skills/earnings-analysis/SKILL.md` | 整個報告規格改寫：8-12頁美股格式 → 15-25頁台股投顧格式；引用規範從 SEC/EDGAR 超連結改成 `[TEJ MCP]`/`[WebSearch]` 標籤；13 區塊骨架取代原本 5-phase 美股結構 | 真實報告格式與美股 IB 模板完全不同（見下方發現） |
| `skills/earnings-analysis/references/report-structure.md` | 整個逐頁模板重寫，改成 13 區塊骨架，每區塊標註對應 TEJ 工具 | 同上 |

## 刻意保持原文未修改的檔案（測試用途，非疏漏）

| 檔案 | 為什麼不改 |
|---|---|
| `skills/comps-analysis/SKILL.md`（從 market-researcher 複製） | 使用者要求先測試舊版 DCF/trading comps 方法論在只有 TEJ 資料時能跑到哪裡，卡住的地方記錄為 TEJ 缺口，不預先假設要換成 PE-band/PB-band |
| `skills/dcf-model/SKILL.md`（從 financial-analysis 核心複製） | 同上 |
| `skills/sector-overview/SKILL.md`（從 market-researcher 複製） | 同上，原文仍保留 CapIQ/FactSet trading comps 段落，允許輸出比真實投顧報告更豐富的內容 |
| `skills/earnings-analysis/references/workflow.md`、`references/best-practices.md` | 通用寫作品質原則與美股/台股格式無關，尚未逐條核對，留待下一輪 |

## 尚未處理

- `model-update`、`audit-xls`、`morning-note`、`earnings-preview` 四個沿用 skill 尚未檢查是否有隱藏的美股假設（如 GAAP 專有名詞、SEC 引用格式），下一輪需要過一遍
- `.mcp.json` 內的 TEJ MCP URL 為示意值，需依實際部署方式填入

## v2 更新（使用者實測比對真實報告後要求）

用 8436 大江生醫產出範例報告後，跟真實的富邦、玉山投顧報告逐段比對，發現 v1 版本「只呈現 TEJ 共識評等、不生成 AI 意見」的護欄設計，讓報告讀起來像研究資料彙編，不像投顧報告的果斷語氣。使用者明確決定放寬護欄，改為：

1. **`agents/earnings-reviewer.md`**：允許生成單一 house 評等＋目標價，但方法論必須錨定 `get_stock_valuation_statistics` 的歷史 PE-band 百分位（不得憑空喊價），並揭露「AI模型化推算」字樣、同時並陳 TEJ 共識區間對照。
2. **`earnings-analysis/SKILL.md` + `report-structure.md`**：
   - 封面摘要改成單一評等+目標價+方法論說明（不再只寫共識區間）
   - 獲利預估修正表（Page 13）改成真正的舊估vs新估點估數字＋逐季預估，不能用寬區間帶過
   - 投資風險精簡成 3-4 點，不窮舉
   - 引用標籤改成「區塊/表格結尾標一次」，不逐句插入，讀起來更像真實投顧報告

**重要**：這是使用者主動要求放寬的護欄變更，不是我方擅自決定。放寬後的方法論仍要求可回溯（目標本益比要能對照歷史 PE-band 說明依據），不是完全不受控的 AI 喊價。
