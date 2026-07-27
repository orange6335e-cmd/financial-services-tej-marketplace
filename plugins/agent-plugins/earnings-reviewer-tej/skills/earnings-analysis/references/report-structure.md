# 台股投顧報告 — 逐頁模板與資料來源對照

依 3 份真實券商投顧報告歸納（玉山證券投顧、富邦投顧、華南投顧，2026/06/30 檔期）。取代原版美股 SEC/EDGAR 超連結引用規範。

引用規範：每個數字/圖表結尾用 `[TEJ MCP - 工具名]` 或 `[WebSearch]` 標註，不用超連結格式。

---

## PAGE 1：封面摘要（v2：house view，非共識呈現）

```
[公司代號] [公司名稱]                          [報告日期]
投資評等：[買進/中立/賣出，AI house view]      目標價：NT$[X] 元
收盤價：[X] 元   上漲空間：[X]%
評價方法：目標本益比 [X]x × [FY202XF] 預估EPS [X] 元
目標本益比依據：[說明——例如現價本益比落於近2年歷史區間低檔（現價PER/歷史中位數/25分位），
                故採 [X]x（介於現價與25分位/中位數之間）作溫和均值回歸假設]
對照：TEJ 券商共識目標價區間 NT$[low]–[high] 元（近N筆，發布期間[起]–[迄]）
⚠️ 本評等與目標價為 AI 依 TEJ 資料模型化推算，非人工分析師研究意見，僅供示範用途。
```

**方法論可回溯性要求**：目標本益比的選擇必須引用 `get_stock_valuation_statistics` 的 min/max/mean/median/p25/p75 數字，並寫清楚落點理由；EPS 必須引用具體年度預估值（`get_analyst_forecasts` 或 `get_broker_investment_ratings` 的 f_eps1/f_eps2），不能用區間帶過。

**交易資料表**（右側或下方小表）：
```
市值 (億元)                 [TEJ MCP - get_company_basic_info]
流通在外股數                [TEJ MCP - get_company_basic_info]
董監持股%                   [TEJ MCP - get_director_supervisor_holdings]
外資持股%                   [TEJ MCP - get_foreign_investor_holdings]
投信持股%                   [TEJ MCP - get_institutional_trades]
52週高/低                   [TEJ MCP - get_price_stats_daily]
日均量(近月)                [TEJ MCP - get_daily_stock_prices]
融資/融券使用率             [TEJ MCP - get_margin_transactions]
```

**個股 vs 大盤走勢圖**：`[TEJ MCP - get_stock_return_comparison / get_cumulative_return_comparison]`

**研究員聯絡方式**：固定樣板，非資料驅動

---

## PAGE 2：投資建議段落

敘述段落，說明評等理由。

```
■ **[標題，如「產能利用率回升，訂單能見度改善」]**

[段落內容——結合 TEJ 財務數字與產業脈絡的敘述]

資料來源：TEJ MCP（財務數字）＋ WebSearch（產業/客戶動向）
```

---

## PAGE 3：重點評論分析

分點列出成長動能/催化劑，質化＋量化混寫：

```
■ [催化劑 1 標題]
[內容，含具體數字佐證]
[TEJ MCP - get_monthly_revenue_summary / get_product_sales_mix_yearly]

■ [催化劑 2 標題]
[內容，如產業循環、客戶拉貨動向]
[WebSearch]
```

---

## PAGE 4：投資風險（v2：精簡，不窮舉）

只列 3-4 個最關鍵風險，每點 2-3 句話寫清楚「風險是什麼＋對財務數字的量化影響」，不要條列式窮舉次要風險：

```
1. [風險標題，如「原物料成本上升侵蝕毛利率」]
   [2-3句：具體影響幅度，如毛利率可能壓縮X個百分點；哪個財務項目受影響]

2. [風險標題]
   [2-3句]

3. [風險標題]
   [2-3句]
```

資料來源標於本區塊結尾一次即可：`資料來源：WebSearch（產業脈絡）＋ TEJ MCP（財務敏感度數字，如有）`，不要每一點都插標籤。

---

## PAGE 5-6：主要財務數據及預估值表

```
                    20XXA   20XXA   20XXE   20XXE   20XXE
營業收入(百萬)
營收成長率(%)
毛利率(%)
營業利益率(%)
稅前淨利(百萬)
EPS(元)
股利(元)
股利成長率(%)

Source: TEJ MCP - get_company_financial_data / get_full_company_report / get_financial_statements_pivoted
```

---

## PAGE 7-8：公司簡介／產品營收比重／產能布局

- 公司沿革、主要產品線 — `[TEJ MCP - get_company_basic_info]`
- 產品營收比重表/圖 — `[TEJ MCP - get_product_sales_mix_yearly / get_product_segment_revenue / get_segment_information]`
- 產能布局、轉投資架構 — `[TEJ MCP - get_affiliated_companies / get_consolidated_entities]`＋`[WebSearch 補充最新擴產計畫新聞]`

---

## PAGE 9-11：營運概況／訪談內容

季度營運詳細敘述，通常包含法說會/公司說法：

```
[季度營運文字說明，含客戶/產品/地區別動態]

資料來源：TEJ MCP - get_company_news_summary / get_company_material_disclosures / get_monthly_revenue_detail
補充：WebSearch（法說會後續報導、管理層公開發言）
```

---

## PAGE 12：季度財務數據表

```
                    Q[X-3]  Q[X-2]  Q[X-1]  Q[X]    YoY    QoQ
營收(百萬)
毛利率(%)
營業利益率(%)
稅後淨利(百萬)
EPS(元)

Source: TEJ MCP - get_financial_statements_pivoted / get_monthly_revenue_earnings
```

---

## PAGE 13：獲利預估修正表（v2：真正的舊估 vs 新估，含逐季點估）

不能只寫區間，要有具體點估數字：

```
                    FY2026F(舊估)  FY2026F(新估)  調整%   FY2027F(新估)
營收(百萬)
EPS(元)
                    Q2'26F  Q3'26F  Q4'26F
營收(百萬)
EPS(元)

調整理由：[1-2句，說明為何上修/下修/維持]
市場共識對照：FY2026F EPS 共識區間 [低]–[高] 元（近N筆券商預估，TEJ get_broker_investment_ratings）

Source: TEJ MCP - get_analyst_forecasts / get_broker_investment_ratings
```

**規則**：不得用「約9-10元」這種寬區間帶過，必須選定具體點估（可用共識中位數或近期最新一筆），並附上為何選這個點估的理由（如：採最近30天內發布的券商預估中位數）。

---

## PAGE 14-16：圖表包

| 圖表 | TEJ 工具 |
|---|---|
| 營收趨勢 | `get_monthly_revenue_summary` |
| 毛利率/營益率/淨利率趨勢 | `get_financial_statements_pivoted` |
| 經營能力天數（存貨/應收週轉） | `get_financial_statement_details` |
| 自由現金流量 | `get_company_financial_data` |
| PE-band | `get_stock_valuation_series` |
| PB-band | `get_stock_valuation_series` |
| 外資/投信買賣超與持股 | `get_foreign_investor_holdings` / `get_institutional_trades` / `get_three_institutional_trades` |
| ROE/ROA | `get_financial_statements_pivoted` |

全部標註 `[TEJ MCP]`，此區塊幾乎無需外部資料。

---

## PAGE 17-19：完整財務報表

損益表、資產負債表、現金流量表、財務比率 — `[TEJ MCP - get_financial_statements_pivoted / get_company_simple_financial_statements]`

---

## PAGE 20（選配）：ESG 專區

僅富邦式報告有此區塊：

```
ESG Rating（E/S/G 子分數）      [TEJ MCP - get_esg_metrics]
碳排放                          [TEJ MCP - get_carbon_emissions]
用水量                          [TEJ MCP - get_water_usage]
員工人數/福利                   [TEJ MCP - get_employee_turnover / get_entry_level_compensation]
公司治理評鑑                     [TEJ MCP - get_governance_evaluation]
```

---

## 最後一頁：免責聲明／評等定義／研究人員聲明／智財權聲明

固定樣板文字，三家券商大同小異，非資料驅動，直接沿用制式模板即可。

---

## 總結：本骨架的資料來源分布

13 個區塊中，11 個區塊主要或完全由 TEJ MCP 覆蓋；只有「投資建議」「重點評論分析」「投資風險」「營運概況」4 個區塊需要 WebSearch 補產業/總經/管理層敘事（其中 2 個是與 TEJ MCP 混合使用，非純外部）。國際共識交叉比對（如 Bloomberg consensus）出現機率低，且非必要引用。


## v2 引用規範變更（重要）

v1 版本要求「每個數字/圖表結尾標註 `[TEJ MCP]`/`[WebSearch]`」，實際產出後發現逐句插入標籤讀起來破碎、不像真實報告的果斷語氣。**v2 改為：標籤只放在每個區塊（大標題底下的段落群）或每張表格/圖表的結尾一次，不逐句插入。** 正文行文維持流暢、果斷，不因為要標來源而打斷語氣。
