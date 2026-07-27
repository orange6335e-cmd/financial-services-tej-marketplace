---
name: industry-landscape
description: 產出台股「產業/主題」研究報告——產業概況、競爭格局、可比公司群、候選標的清單。資料源全用 TEJ MCP，每段標註來源。原版為美股 CapIQ/FactSet trading comps 格式，已改版為台股 tejind 產業體系＋TEJ 篩選工具。
---

# 台股產業/主題研究報告（Industry / Theme Landscape — TW 版）

## 何時使用

- 使用者要求某台股「產業」的概況（如「金控業現況」「IC 設計競爭格局」）
- 使用者給一個「主題」要找標的（如「高股息」「谷底翻揚」「營收高成長且估值不貴」）
- 需要為某支個股建立「可比公司群」，供後續估值 comps 使用

## 報告骨架（5 區塊）

完整工具對照見下方 Workflow。以下為區塊總覽：

1. **產業概況**：家數、總市值、市值分佈（龍頭集中度）、估值帶（產業 PER/PBR/殖利率區間）、產業指數近期走勢 — `[TEJ MCP - get_industry_snapshot / get_industry_index_summary]`
2. **競爭格局**：前 5-8 大依市值排序，多維並排比較（估值／獲利／籌碼／股利），指出龍頭與追隨者結構、估值分歧 — `[TEJ MCP - compare_companies]`
3. **可比公司群**：指標公司的同業清單＋估值快照，標明該產業的估值原型與核心指標，並剔除下市/合併空殼（寫明剔除理由）— `[TEJ MCP - get_peer_companies]`
4. **候選標的清單**：依主題篩出的候選股，每支附入選的實際數字與篩選條件（可回溯）— `[TEJ MCP - screen_stocks / screen_growth_stocks / screen_dividend_stocks / screen_turnaround_stocks]`
5. **質化脈絡與結論**：產業結構性成長/衰退的敘事判斷、主題的投資邏輯 — `[WebSearch]`，數字佐證回指前四區塊

## Workflow

### Phase 1：界定範圍
- **產業切入**：`get_industry_snapshot(industry_code=<tejind>, include_valuation=true)`。不確定產業碼→先 `get_company_basic_info(<代表股>)` 讀 tejind3 欄位。
- **主題切入**：把主題轉成篩選——高股息→`screen_dividend_stocks`；營收高成長→`screen_growth_stocks`；虧轉盈→`screen_turnaround_stocks`；自訂財務/估值門檻→`screen_stocks(conditions=[...])`。

### Phase 2：產業概況
- `get_industry_snapshot` 取家數/市值排序/估值快照。
- `get_industry_index_summary(idx_ids=[...], include_series=true)` 取產業指數走勢。
- 歸納：產業規模、龍頭集中度、估值帶（min/中位數/max 的 PER/PBR/殖利率）、近期漲跌。

### Phase 3：競爭格局
- 取前 5-8 大 coid，`compare_companies(coids=[...], sections=["valuation","financials","dividend_policy","chip_distribution"])`。
- 產出並排比較表；指出龍頭、市佔/市值集中度、估值為何分歧。

### Phase 4：可比公司群（依估值原型）
- `get_peer_companies(coid=<指標股>, include_valuation=true)`。
- **判定估值原型**（標準獲利／金融／循環／資產收益／未獲利管線…），用該原型核心指標排比較，不要一律套 PER。
- **清洗**：剔除 mkt=DIST 或資料停在過去年份的下市/合併空殼，寫明剔除了誰與理由（避免扭曲產業中位數）。

### Phase 5：候選標的清單
- 依主題條件跑 screen 工具，取候選股。
- 每支附入選數字（如殖利率 X%、營收 YoY +X%、PER X 倍）與篩選門檻。
- 若要收斂，說明排序依據（sort_by）。

### Phase 6：組裝與品質檢查
- 依 5 區塊組裝報告，每段落結尾標資料來源。
- 檢查：每個數字有來源標籤？工具名都真實存在（無幻覺）？可比公司清洗過？篩選條件可回溯？
- 缺口記到 `MR_GAPS.md`。

## Output Specification

**檔名**：`[產業或主題]_產業研究_[日期].docx`（或 .md）
**附帶檔案**：`MR_GAPS.md` — 本次執行遇到的資料缺口

## Guardrails

- 不生成個股投資評等/目標價（發現層職責邊界）
- 每個數字標來源；不幻覺工具名；可比公司先清洗再算中位數
- 篩選條件可回溯，不黑箱

## Dependencies

- TEJ MCP（`mcp__tejapi__*`）
- WebSearch（產業結構性敘事）
- DOCX skill（若輸出 docx）
