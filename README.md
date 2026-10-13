# Sales Teams Quarterly Performance — Power BI Analytics Project

An executive-level **Power BI Sales Analytics Dashboard** and data modeling project analyzing quarterly B2B sales pipeline performance, sales manager pacing, sales representative productivity, conversion rates, and pipeline health for Fiscal Year 2017.

---

## 📌 Project Overview

This project transforms raw transactional CRM pipeline data into an actionable, multi-page business intelligence dashboard. It addresses core sales leadership questions:
1. **Quarterly Growth & Pacing**: Are we meeting quarter-over-quarter revenue and conversion targets?
2. **Sales Funnel & Health**: How much revenue is locked in active negotiation, and where is deal leakage occurring?
3. **Sales Rep Productivity**: Who are our high-impact performers, and which reps need structured coaching?
4. **Product & Sector Mix**: Which product lines and customer sectors drive sustainable profit vs. operational drag?

---

## 🛠️ Data Modeling & Engineering Fixes Applied

During data auditing, critical bugs and inconsistencies were identified and resolved to ensure 100% data integrity:

### 1. Indian Names Transformation
All 6 Sales Managers and 35 Sales Agents were mapped to realistic, diverse Indian corporate names to contextualize the dataset for regional presentation while maintaining all underlying relationship keys.
* **Top Manager**: **Manish Mehta** (Central Office, \$2.25M won revenue).
* **Top Rep**: **Dhruv Sharma** (Central Office, \$1.15M won revenue, 349 closed-won deals).

### 2. Resolved Product Key Disconnect (`GTXPro` → `GTX Pro`)
* **Issue**: In `sales_pipeline.csv`, the top product was recorded as `GTXPro` (without space), whereas `products (1).csv` listed it as `GTX Pro`.
* **Impact**: This product represents **\$3,510,578 (35.1% of company revenue)** and 1,480 opportunities.
* **Fix**: Normalized all pipeline entries to `GTX Pro`, establishing a 100% clean relationship with `dim_Products`.

### 3. Cleaned Sector Typo
* In `accounts.csv`, the typo `technolgy` was corrected to `technology`.

---

## 📊 Key Executive Findings & Benchmark Metrics (FY 2017)

| Metric / Dimension | Value | Strategic Business Significance |
| :--- | :---: | :--- |
| **Total Closed-Won Revenue** | **\$10,005,534** | Company surpassed the \$10M milestone across 4,238 won deals. |
| **Quarterly Revenue Trend** | **Q1**: \$1.13M<br>**Q2**: \$3.09M (+172%)<br>**Q3**: \$2.98M (-3.4%)<br>**Q4**: \$2.80M (-6.0%) | Massive expansion occurred in Q2; second half stabilized with a minor Q4 dip. |
| **Overall Win Rate** | **63.15%** | 4,238 Won vs. 2,473 Lost (Loss Rate: 36.85%). West leads at 63.8%. |
| **Average Sales Cycle** | **51.8 Days** (Won)<br>**41.5 Days** (Lost) | Lost deals are disqualified ~10 days faster than deals carried to close. |
| **Active Open Pipeline** | **\$3,892,229** (1,589 deals) | High-value pipeline currently in active negotiations ("Engaging"). |
| **Early Discovery Pipeline** | **\$1,073,986** (500 deals) | Untapped prospecting volume. |
| **Pipeline Rot / Stale Deals** | **686 Deals (>180 Days)** | Over 43% of the open pipeline is older than 6 months (~$1.7M at risk). |
| **Pareto 80/20 Rule** | **GTX Pro + GTX Plus Pro + MG Advanced** | **Top 3 products generate 83.5% of all revenue** (\$8.36M). |
| **Bandwidth Drain** | **MG Special** | 793 won deals generated only **\$43,768** total (\$55/deal), consuming excessive rep time. |
| **Key Person Risk** | **Dhruv Sharma** | Single sales rep drives **11.5% of total company revenue** (\$1.15M). |

---

## 📈 Power BI Report Architecture (3-Page Story)

```text
[Page 1: Executive Overview] ──────> High-level KPIs, Manager Leaderboard, Product Mix
           │
           ├─────────> [Page 2: Pipeline Funnel & Leakage] ──> Funnel, Pipeline Aging, Loss Reasons
           │
           └─────────> [Page 3: Rep Coaching Matrix] ───────> 4-Quadrant Scatter, Leaderboard, Drilldown
```

### Page 1: Executive Overview & Target Pacing
* **Top KPI Cards**: Total Revenue Won, Win Rate %, Won Deal Volume, Avg Sales Cycle (with Prior Quarter comparison and QoQ % change badges).
* **Manager Performance Leaderboard**: Clustered horizontal bar chart comparing QTD Sales Won against prior quarter with manager win rate callouts.
* **Product Revenue Pareto**: Revenue mix highlighting the top 3 high-margin drivers.
* **Slicers**: Fiscal Quarter (`2017-Q1` to `2017-Q4`), Regional Office (`Central`, `East`, `West`), Manager, and Product.

### Page 2: Pipeline Health & Funnel Diagnostics
* **Full-Funnel Visualization**:
  $$\text{Prospecting (500)} \longrightarrow \text{Engaging (1,589)} \longrightarrow \begin{cases} \text{Won: 4,238 (\$10.01M)} \\ \text{Lost: 2,473 (\$0)} \end{cases}$$
* **Pipeline Aging Breakdown**: Highlights deals open `<90 days`, `90-180 days`, and `>180 days` to eliminate pipeline rot.
* **Loss Analysis by Series**: Identifies product lines with the highest drop-off rate.

### Page 3: Sales Rep Performance & Coaching Matrix
* **4-Quadrant Rep Matrix**:
  * **X-Axis**: Win Rate % (Benchmark = 63.2%)
  * **Y-Axis**: Total Won Revenue (\$)
  * **Bubble Size**: Total Opportunities Handled
  * *Categories*: Stars (Top revenue & win rate), Workhorses (High volume, lower ticket), Coaching Needed (High opportunity, low conversion), At-Risk (Low activity).
* **35-Agent Leaderboard**: Searchable scorecard with individual cycle times, deal counts, and revenue.

---

## 📐 DAX Formula Reference

### Corrected QoQ Variance Measures
```dax
// Corrected QOQ % Opportunities
QOQTD % Opportunities = 
VAR _CQ = [QTD Opportunities]
VAR _PQ = [PQTD Opportunities]
RETURN
DIVIDE(_CQ - _PQ, _PQ, 0)

// Corrected QOQ % Conversion Rate
QOQTD % Conversion Rate % = 
VAR _CQ = [QTD Conversion Rate %]
VAR _PQ = [PQTD Conversion Rate %]
RETURN
DIVIDE(_CQ - _PQ, _PQ, 0)

// Corrected QOQ % Days to Make a Deal
QOQTD % Days to make a deal = 
VAR _CQ = [QTD Days to make a deal]
VAR _PQ = [PQTD Days to make a deal]
RETURN
DIVIDE(_CQ - _PQ, _PQ, 0)
```

### Advanced Pipeline & Funnel Measures
```dax
// Active Open Pipeline Estimated Value ($)
Open Pipeline Value = 
CALCULATE(
    SUMX(fct_Sales_Pipeline, RELATED(dim_Products[sales_price])),
    fct_Sales_Pipeline[deal_stage] = "Engaging"
)

// Overall Win Rate %
Win Rate % = 
VAR _Won = CALCULATE(COUNTROWS(fct_Sales_Pipeline), fct_Sales_Pipeline[deal_stage] = "Won")
VAR _Lost = CALCULATE(COUNTROWS(fct_Sales_Pipeline), fct_Sales_Pipeline[deal_stage] = "Lost")
RETURN
DIVIDE(_Won, _Won + _Lost, 0)

// Stale Deals Count (> 90 Days in Engaging)
Stale Deals Count = 
CALCULATE(
    COUNTROWS(fct_Sales_Pipeline),
    fct_Sales_Pipeline[deal_stage] = "Engaging",
    DATEDIFF(fct_Sales_Pipeline[engage_date], DATE(2017, 12, 31), DAY) > 90
)

// Sales Velocity Index ($/Day)
Sales Velocity = 
VAR _WonRev = [Sales Won]
VAR _AvgDays = [Avg days to Won Deals]
RETURN
DIVIDE(_WonRev, _AvgDays, 0)
```

---

## 👥 Sales Leadership & Team Directory

### Regional Sales Managers
* **Manish Mehta** — Central Regional Office (\$2,251,930 \| 62.2% Win Rate)
* **Sanjay Singhania** — West Regional Office (\$1,964,750 \| 64.3% Win Rate)
* **Rahul Kapoor** — East Regional Office (\$1,960,545 \| 62.1% Win Rate)
* **Priya Nair** — West Regional Office (\$1,603,897 \| 63.4% Win Rate)
* **Kavita Sharma** — East Regional Office (\$1,130,049 \| 64.4% Win Rate)
* **Deepak Verma** — Central Regional Office (\$1,094,363 \| 63.0% Win Rate)

### Key Sales Representatives
* **Dhruv Sharma** — Top Revenue Rep (\$1,153,214 \| 349 Won Deals)
* **Vivek Lokhande** — Runner-Up Rep (\$478,396 \| 221 Won Deals)
* **Kunal Hegde** — #3 Rep (\$454,298 \| 209 Won Deals)
* **Bhavin Patel** — Benchmark Representative (\$249,704 \| 171 Won Deals)

---

## 🚀 How to Open and Refresh the Report

1. Launch **Microsoft Power BI Desktop**.
2. On the **Home** tab ribbon, click **Refresh Data**:
   * Power BI will load the updated CSV files with the Indian names and normalized product keys.
3. **Agents Page Drillthrough Note**:
   * Navigate to the **Agents** page. In the Filters pane, update the page filter to **`Bhavin Patel`** (or clear the page-level filter so any agent selected from the Main page dynamically populates the visual).
4. Save your `.pbix` file.

---

## 🌐 Interactive Web Prototype
An interactive HTML companion dashboard is available for demonstration and quick exploration:
* Open `interactive_sales_dashboard.html` in any modern web browser to interact with the quarterly slicers, manager rankings, and coaching matrix.
