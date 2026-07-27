---
name: briefing-pack
description: 產出台股「個股簡易紀錄」——1-2 頁精簡摘要，聚焦最新狀態、近期變化、即將事件、市場觀點。資料源全用 TEJ MCP，每段標來源、標資料截止時間。改編自原版 meeting-prep 的 briefing。
---

# 個股簡易紀錄（Briefing Pack — TW 版）

## 何時使用

- 需要快速掌握某個股或某投組的最新狀況（不是完整投顧報告，完整報告用 earnings-reviewer-tej）。

## 骨架（5 塊，1-2 頁）

1. **一句話結論**：估值貴/便宜、籌碼進出方向、近期有無重大事件。
2. **最新狀態快照**（小表）：收盤價、報酬 vs 大盤、PER/PBR/殖利率相對歷史、三大法人買賣超與持股 — `[TEJ MCP - get_price_stats_daily / get_stock_return_comparison / get_stock_valuation_statistics / get_latest_chip_distribution]`
3. **近期動態**：最新重大訊息、最新月營收 — `[TEJ MCP - get_company_material_disclosures / get_monthly_revenue_detail]`
4. **即將到來的事件**：除權息/股東會等 — `[TEJ MCP - get_company_events_calendar / get_shareholder_meetings]`
5. **市場觀點**：券商最新評等/目標價/EPS 共識 + 新聞 — `[TEJ MCP - get_broker_investment_ratings]` + `[WebSearch]`

## Workflow

1. 界定範圍（單股 or 投組）。
2. 依 5 塊順序拉 TEJ 資料。
3. 近期動態聚焦「變化」；即將事件過濾出未來的。
4. 組成 1-2 頁；開頭標資料截止時間；每個數字標來源。
5. 投組模式：先給投組層級摘要，再逐檔小卡。

## Output Specification

**檔名**：`[代號或投組名]_簡易紀錄_[日期].md`（或 .docx）
**篇幅**：單股 1-2 頁。

## Dependencies

- TEJ MCP（`mcp__tejapi__*`）
- WebSearch（新聞面）
