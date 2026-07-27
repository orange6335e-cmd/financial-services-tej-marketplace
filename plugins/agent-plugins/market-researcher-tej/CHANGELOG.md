# 修改紀錄：market-researcher → market-researcher-tej

改編自 `anthropics/financial-services` 的 market-researcher，用於台股「產業/主題」層級研究，資料源改接 TEJ MCP。

## 已建立/修改的檔案

| 檔案 | 改動 | 原因 |
|---|---|---|
| `.claude-plugin/plugin.json` | 新建，改名改描述 | 標示 TEJ 移植版、發現層定位 |
| `.mcp.json` | 新建，只留 TEJ MCP 一個 server | 原版 CapIQ/FactSet 等外部連接器台股用不到 |
| `agents/market-researcher.md` | tools 白名單換成 `mcp__tejapi__*` + `WebSearch`；workflow 改用 tejind 產業碼＋TEJ 篩選工具；新增估值原型對照表；新增「發現層不生成個股評等」的職責邊界；新增 `MR_GAPS.md` 機制 | 核心接線 |
| `skills/industry-landscape/SKILL.md` | 報告骨架從美股 trading comps 改成台股 5 區塊（產業概況/競爭格局/可比公司群/候選標的清單/質化結論） | 台股產業研究格式與美股 IB 模板不同 |

## 設計要點

- **發現層定位**：本 agent 只做產業與標的發現，不生成個股投資評等/目標價（那是 earnings-reviewer-tej 的職責）。產出的可比公司群＋候選標的清單是它的上游輸入。
- **估值原型**：可比公司比較依產業事先判定估值原型（標準獲利/金融/循環/資產收益/未獲利管線），用該原型核心指標，不一律套 PER。
- **資料清洗**：可比公司清單須剔除下市/合併空殼（mkt=DIST、資料過期），避免扭曲產業中位數。

## 尚未處理

- `.mcp.json` 的 TEJ MCP URL 為示意值，需依實際部署填入。
- 實跑驗證：見 outputs 的 MR 測試產出與 `MR_GAPS.md`。
