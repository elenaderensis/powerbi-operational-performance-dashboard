# Power BI Operational Performance Dashboard

End-to-end Power BI solution transforming raw operational data into actionable insights for plant-level profitability monitoring across 15+ countries.

## Executive Summary

This Power BI dashboard consolidates 500K+ transactional records to enable real-time profitability monitoring and data-driven resource allocation.

Key outcomes:
- Identified -$265.29K YTD variance vs PYTD, enabling targeted investigation into underperforming regions
- Revealed 74.91% YTD growth in top-performing country (Philippines), providing model for expansion strategy
- Enabled monthly drill-down analysis across Country-Product-Month dimensions, replacing quarterly manual reporting
- Supported executive decision-making through dynamic KPI monitoring (Gross Profit, Quantity, Sales, GP%)

Project Resources: [Download Power BI Dashboard](https://github.com/elenaderensis/powerbi-operational-performance-dashboard/blob/main/Power%20Bi%20Project.pbix)

**Dashboard Overview**
<img width="907" height="509" alt="Power BI Dashboard" src="https://github.com/user-attachments/assets/0519a3ff-ee6e-4253-b074-77dbe73f6b62" />


## Business Context and Objectives

The Challenge:<br>
Plant operations teams managed performance through disconnected Excel reports, creating delays in identifying trends and making data reconciliation time-consuming. Stakeholders needed a single source of truth with ability to compare current performance against historical benchmarks.

Project Scope:<br>
This Power BI solution consolidates 500K+ transactional records across multiple plants and countries, enabling:
- Real-time KPI monitoring (YTD Sales, Gross Profit, Quantity, GP%)
- Year-over-year comparison capabilities (YTD vs PYTD)
- Multi-dimensional analysis (Country, Product Type, Time)
- Profitability segmentation and outlier identification


## Data Architecture

### Data Structure

**Plant_Fact (Fact table):** 500K+ transactional records
- Fields: Gross Profit, Quantity, Sales, Date_Time

**Accounts (Dimension):** Entity-level segmentation
- Fields: Entity-level segmentation and profitability classification

**Plant_Hierarchy (Dimension):** Geographic aggregation
- Fields: Hierarchical aggregation across plants, regions, countries

**Date (Dimension):** Time intelligence
- Fields: Custom date table enabling YTD, PYTD calculations

### Data Model

**Star schema** with one-to-many relationships from Plant_Fact to dimension tables, optimized for filtering efficiency and scalable reporting. SQL-based validation ensures data quality through null handling, deduplication, and referential integrity checks.


## Technical Implementation

### DAX Measures & Calculations

**Base Measures:** Foundational metrics aggregating transactional data at any filter context.
```dax
Gross Profit = SUM(Plant_Fact[GrossProfit])
GP% = DIVIDE([Gross Profit], [Sales], 0)
```

**Time Intelligence Measures:** YTD and PYTD calculations enable continuous year-over-year monitoring.
```dax
YTD_GrossProfit = TOTALYTD([Gross Profit], Plant_Fact[Date_Time])
PYTD_GrossProfit = CALCULATE([YTD_GrossProfit], SAMEPERIODLASTYEAR(Plant_Fact[Date_Time]))
```

**Dynamic Measure Selection:** SWITCH function enables metric toggling (Sales, Quantity, Gross Profit) in single visualization, reducing dashboard clutter by 67%.
```dax
S_YTD = SWITCH(SELECTEDVALUE(Slc_Values[Values]),
    "Sales", [YTD_Sales],
    "Quantity", [YTD_Quantity],
    "Gross Profit", [YTD_GrossProfit], BLANK())
```

### Visualization Strategy

**Treemap (Bottom 10 Countries):** Size encoding prioritizes investigation areas—larger tiles = bigger problems requiring urgent attention. Eliminates need to scan tables of numbers.

**Waterfall Chart (YTD vs PYTD Variance):** Shows cumulative contribution by Month-Country-Product. Moves beyond "we're down $265K" to "we're down because of these three specific drivers."

**Scatter Plot (Account Profitability):** Reveals relationship between GP% and Gross Profit. Reference line (GP% = 0.4) creates clear visual threshold for acceptable profitability.


## Key Findings & Insights

### Finding 1: Geographic Performance Disparity
Czech Republic (-$55.44K), Finland (-$53.10K), and Colombia (-$38.77K) drive -$147K YTD underperformance vs PYTD (55% of total negative variance). Waterfall analysis shows consistent monthly declines, indicating structural issues rather than one-off events. **Business Impact:** Enables targeted intervention on three countries instead of broad cost-cutting across all operations.

### Finding 2: High-Performer Identification
Philippines delivers +74.91% YTD growth vs PYTD while maintaining GP% >0.40. Cross-validation across treemap, scatter plot, and waterfall confirms sustained growth with healthy margins (no margin dilution). **Business Impact:** Philippines becomes replication benchmark for demand planning, production scheduling, and commercial execution in similar markets.

### Finding 3: Product Mix Optimization Opportunity
Indoor products sustain 0.42 GP%, while Outdoor products average 0.38 GP% but deliver ~23% higher volume. Current performance reflects volume-over-margin bias. **Business Impact:** Modeled 5% Outdoor price increase with 10% volume tolerance would generate ~+$210K incremental GP annually.


## Recommendations & Business Impact

**1. Address Bottom Performers (Czech Republic, Finland, Colombia):** Conduct operational audits and implement monthly performance reviews using dashboard (escalation threshold: -10K monthly variance). Benchmark against Philippines practices. **Target:** Recovering these three countries to breakeven vs PYTD would add +$147K annually.

**2. Replicate High-Performer Practices (Philippines):** Document key practices (forecasting, scheduling, suppliers, incentives) and pilot replication in 3-5 comparable markets with quarterly benchmarking. **Target:** If 3 countries reach 30% of Philippines' growth rate, incremental GP = +$450K annually.

**3. Optimize Product Mix for Profitability (Outdoor Category):** Test 3-5% price increases in select markets. Identify high-volume Outdoor accounts with GP% <0.35 for pricing actions. Review low-margin Outdoor SKUs for redesign or discontinuation. **Target:** 5% price increase on 50% of Outdoor volume yields +$175K incremental GP annually.


## Future Enhancements

### 1. Forecast vs Actual Integration
Incorporate budget/forecast data to enable Plan vs Actual variance analysis alongside YTD vs PYTD. Add traffic-light indicators (Red/Yellow/Green) based on variance thresholds.

### 2. Profitability Deep-Dive
Integrate operational cost data (COGS, logistics, overhead) to transition from Gross Profit to Net Profit analysis. Build product-level P&L views to identify true bottom-line contributors.

### 3. Predictive Analytics
Add trend-based forecasting using exponential smoothing or regression to project Q4 performance based on YTD actuals. Implement seasonality indexing to improve year-over-year comparisons during promotional periods.


## Project Reflection

This project demonstrates the complete Power BI analytics workflow with focus on translating technical capability into business value. Key design decisions solve real operational challenges: **star schema** enables scalable growth, **SWITCH measures** reduce maintenance burden, **waterfall charts** answer "why" not just "what changed," and **scatter plots** reveal relationships that tabular reports miss.

The dashboard's value isn't in reporting the -$265K variance, it's in immediately answering strategic questions: "Which countries need intervention?" (Czech Republic, Finland, Colombia), "Where should we replicate success?" (Philippines model), "Where can we optimize pricing?" (High-volume, low-GP% accounts in scatter plot).

**Result:** Operations meetings shifted from "What happened?" to "What should we do differently?", exactly the outcome analytics should deliver.
