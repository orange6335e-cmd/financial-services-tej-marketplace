# 修改紀錄：meeting-prep → meeting-prep-tej

改編自 `anthropics/financial-services` 的 meeting-prep agent，資料源改接 TEJ MCP。

## 已建立/修改的檔案

| 檔案 | 改動 | 原因 |
|---|---|---|
| `.claude-plugin/plugin.json` | 新建 | TEJ 移植版、個股簡易紀錄定位 |
| `.mcp.json` | 新建，只留 TEJ MCP | 原版 CRM/Box/FactSet 外部連接器台股用不到 |
| `agents/meeting-prep.md` | 產出定位為「個股簡易紀錄」；tools 換成 `mcp__tejapi__*` + `WebSearch`；workflow 改用 TEJ 即時/籌碼/事件工具 | 核心接線與定位 |
| `skills/briefing-pack/SKILL.md` | 骨架為 5 塊精簡摘要（1-2 頁），非完整報告 | 簡易紀錄定位 |

## 設計要點

- 與 earnings-reviewer-tej 的分工：本 agent 是「個股簡易紀錄」，聚焦變化與即將事件、篇幅短；earnings-reviewer-tej 是完整 13 區塊投顧報告。
- 簡易紀錄務必標資料截止時間。
- 不生成個股評等，只呈現券商共識與客觀變化。
