---
name: earnings-analysis
description: 產出台股投顧格式的個股報告（15-25 頁，仿真實券商投顧報告，非西方 IB 模板）。用於季後更新、定期覆蓋更新。每個段落標註資料來源（TEJ MCP 或 WebSearch）。原版此 skill 是美股格式（8-12頁、SEC 10-Q/EDGAR 超連結、GAAP/Adjusted EPS），已改版。
---

# 台股投顧報告（Earnings Update / Coverage Update — TW 版）

## 與原版的差異

| | 原版（美股） | 這版（台股投顧） |
|---|---|---|
| 頁數/字數 | 8-12 頁 / 3,000-5,000 字 | 15-25 頁，依真實報告（玉山/富邦/華南）落在此區間 |
| 估值方法 | DCF + trading comps | Forward PER × 預估 EPS 目標價，搭配 PE-band/PB-band 區間圖（`comps-analysis`/`dcf-model` 保留原文作為對照測試，非本骨架採用的方法） |
| 評等來源 | AI 自行判斷 BUY/HOLD/SELL，無方法論揭露 | **v2**：AI 生成單一 house 評等＋目標價，但方法論必須錨定 `get_stock_valuation_statistics` 的歷史 PE-band（不得憑空喊價），且並陳 TEJ `get_broker_investment_ratings` 共識區間對照，並揭露「AI模型化推算」字樣 |
| 引用規範 | SEC 10-Q/8-K/EDGAR 超連結 | 每段落標註 `[TEJ MCP - 工具名]` 或 `[WebSearch]` |
| 圖表 | 25-35 張美式圖表（含 DCF 敏感度熱圖、valuation football field） | PE-band、PB-band、營收趨勢、毛利率/營益率/淨利率趨勢、外資投信買賣超、ROE/ROA 等（依真實報告歸納） |

## 何時使用

- 使用者要求某台股標的的「投顧報告」「季度更新」「財報後更新」「覆蓋更新」
- 公司已有既有覆蓋（有歷史評等/目標價可參照）

## 報告骨架（13 個區塊，依 3 份真實投顧報告歸納）

完整頁面模板見 `references/report-structure.md`。以下為區塊總覽與資料來源對照：

1. **封面摘要**：評等（TEJ 共識）＋目標價＋收盤價＋上漲空間％、交易資料（市值/流通股數/董監外資投信持股%/52週高低/成交量/融資融券）、個股 vs 大盤走勢圖 — `[TEJ MCP]`
2. **投資建議段落**：評等理由敘述 — `[TEJ MCP 數據 + WebSearch 產業脈絡綜合撰寫]`
3. **重點評論分析**：成長動能/催化劑，質化＋量化混寫 — `[WebSearch 為主，數字佐證 TEJ MCP]`
4. **投資風險**：精簡為 3-4 點最關鍵風險（不是列舉式窮舉全部風險），每點 2-3 句話講清楚「風險是什麼＋量化影響」— `資料來源標於段落結尾`
5. **主要財務數據及預估值表**：歷史＋預測（營收/毛利率/營益率/稅前盈餘/EPS/股利/成長率）— `[TEJ MCP]`
6. **公司簡介／產品營收比重／產能布局** — `[TEJ MCP]`
7. **營運概況／訪談內容**：季度營運敘述 — `[TEJ MCP 數據 + WebSearch 補充管理層說法]`
8. **季度財務數據表** — `[TEJ MCP]`
9. **獲利預估修正表**：真正的「本次估值 vs 上次估值」修正表，含 FY2026F／FY2027F 全年點估數字（不是區間），以及未來 2-3 季逐季點估，並列出調整方向與理由 — `[TEJ MCP - get_analyst_forecasts / get_broker_investment_ratings]`
10. **圖表包**：營收趨勢、毛利率/營益率/淨利率、經營能力天數、自由現金流量、PE-band、PB-band、外資/投信買賣超與持股、ROE/ROA — `[TEJ MCP]`
11. **完整財務報表**：損益表、資產負債表、現金流量表、財務比率 — `[TEJ MCP]`
12. **ESG 專區**（選配，富邦式報告才有）：ESG Rating、碳排放、用水量、員工福利、公司治理評鑑 — `[TEJ MCP]`
13. **免責聲明／評等定義／研究人員聲明** — 固定樣板文字，非資料驅動

## Workflow

### Phase 1：資料收集
- 用 `mcp__tejapi__*` 拉區塊 1、5-12 所需資料（見 `references/report-structure.md` 逐段工具對照）
- 用 `WebSearch` 補區塊 2-4、7 的產業/總經/管理層敘事
- 記錄任何拉不到的資料到 `TEJ_GAPS.md`

### Phase 2：分析
- 財務預測更新：新舊估值比較（區塊 9）
- 評等：直接呈現 TEJ 共識評等，不自行生成

### Phase 3：圖表產生
- PE-band/PB-band：用 `get_stock_valuation_series`/`get_stock_valuation_statistics`
- 其餘趨勢圖用對應 TEJ 財務/籌碼工具

### Phase 4：報告組裝
- 依 13 個區塊組裝，每段落結尾附資料來源標籤
- 輸出 DOCX，15-25 頁

### Phase 5：品質檢查
- 檢查每個數字都有來源標籤
- 檢查評等/目標價只呈現共識，沒有 AI 自行生成的意見
- 檢查 `TEJ_GAPS.md` 是否完整記錄本次執行遇到的資料缺口

## Output Specification

**檔名**：`[公司代號]_[公司名稱]_投顧報告_[日期].docx`
**附帶檔案**：`[公司代號]_TEJ_GAPS.md` — 本次執行記錄的資料缺口

## Resources

- `references/report-structure.md` — 逐頁模板＋每區塊 TEJ 工具對照表（完整版）
- `references/workflow.md`（沿用原版，資料萃取細節部分需依 TEJ 工具重新對照，尚未改版）
- `references/best-practices.md`（沿用原版，通用寫作品質原則不受美股/台股格式影響）

## Dependencies

- TEJ MCP（`mcp__tejapi__*`）
- WebSearch（產業/總經/管理層敘事）
- DOCX skill（報告輸出）
