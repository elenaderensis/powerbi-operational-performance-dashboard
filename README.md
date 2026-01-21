# Power BI Operational Performance Dashboard
End-to-end Power BI solution transforming raw operational data into actionable insights for plant-level profitability monitoring and strategic decision-making.


1. **Executive Summary**

Operations lacked real-time, plant-level profitability visibility across 15+ countries. This Power BI dashboard consolidates transactional data, enabling rapid anomaly detection and data-driven resource allocation.
Key Outcomes:
- Identified -$265.29K YTD variance vs PYTD, enabling targeted investigation into underperforming regions
- Revealed 74.91% YTD growth in top-performing country (Philippines), providing model for expansion strategy
- Enabled monthly drill-down analysis across Country-Product-Month dimensions, replacing quarterly manual reporting
- Supported executive decision-making through dynamic KPI monitoring (Gross Profit, Quantity, Sales, GP%)

Project resources:
[Download Power BI Dashboard] (https://github.com/elenaderensis/powerbi-operational-performance-dashboard/blob/main/Power%20Bi%20Project.pbix)

**Dashboard Overview**
<img width="907" height="509" alt="image" src="https://github.com/user-attachments/assets/0519a3ff-ee6e-4253-b074-77dbe73f6b62" />


2. **Business Context and Objectives**

The Challenge:<br>
Plant operations teams were managing performance through disconnected Excel reports, creating delays in identifying trends and making data reconciliation time-consuming. Stakeholders needed a single source of truth for operational metrics with the ability to compare current performance against historical benchmarks.<br>
Project Scope:<br>
This project covers the full analytics lifecycle, from data extraction and validation through final dashboard delivery. The solution consolidates 500K+ transactional records across multiple plants and countries, providing stakeholders with:<br>
- Real-time KPI monitoring (YTD Sales, Gross Profit, Quantity, GP%)<br>
- Year-over-year comparison capabilities (YTD vs PYTD)<br>
- Multi-dimensional analysis (Country, Product Type, Time)<br>
- Profitability segmentation and outlier identification<br>


3. **Data Architecture**

**Data Structure**
- **Plant_Fact** (Fact table): 500K+ transactional records (Gross Profit, Quantity, Sales)
- **Accounts** (Dimension): Entity-level segmentation and profitability classification
- **Plant_Hierarchy** (Dimension): Hierarchical aggregation across plants, regions, countries
- **Date** (Dimension): Custom date table enabling time intelligence functions

**Data Model** <br>
Star schema design with one-to-many relationships from fact to dimensions, optimized for filtering efficiency and scalable reporting.

**Data Quality** <br>
SQL-based validation implemented for null handling, deduplication, referential integrity, and GP% threshold monitoring (0-1 range validation).


4. **Technical Implementation**

**DAX Measures & Calculations**<br>
All calculations are performed using DAX measures rather than calculated columns to optimize performance and reduce model size. Measures are organized into logical groups for maintainability.
**Base Measures**<br>
These foundational metrics aggregate transactional data at any filter context:
- Gross Profit = SUM(Plant_Fact[GrossProfit])
- Quantity = SUM(Plant_Fact[Quantity])
- Sales = SUM(Plant_Fact[Sales])
- GP% = DIVIDE([Gross Profit], [Sales], 0)<br>

Why DIVIDE(): Using the DIVIDE function with a third parameter (0) prevents errors when Sales = 0, ensuring the dashboard doesn't break when filtering to edge cases.

**Time Intelligence Measures**<br>
**Business Context:** Operations teams needed to compare current year performance against prior year at any point in the calendar, not just at fiscal year-end. This enables continuous monitoring rather than waiting for period-end reports.

**Year-to-Date (YTD) Calculations:**
- YTD_GrossProfit = TOTALYTD([Gross Profit], Plant_Fact[Date_Time])
- YTD_Quantity = TOTALYTD([Quantity], Plant_Fact[Date_Time])
- YTD_Sales = TOTALYTD([Sales], Plant_Fact[Date_Time])

**Prior Year-to-Date (PYTD) Calculations:**
- PYTD_GrossProfit = 
CALCULATE([YTD_GrossProfit], SAMEPERIODLASTYEAR(Plant_Fact[Date_Time]))
- PYTD_Quantity = CALCULATE([YTD_Quantity], SAMEPERIODLASTYEAR(Plant_Fact[Date_Time]))
- PYTD_Sales = CALCULATE([YTD_Sales], SAMEPERIODLASTYEAR(Plant_Fact[Date_Time]))<br>

Why this matters: The PYTD vs YTD comparison immediately reveals the -$265.29K variance shown in the dashboard header. By using SAMEPERIODLASTYEAR, we ensure apples-to-apples comparisons—comparing January-March 2023 to January-March 2022, not full-year figures.

**Dynamic Measure Selection with SWITCH**<br>
**Business Context:** Rather than creating separate visuals for Sales, Quantity, and Gross Profit trends, a dynamic slicer approach allows users to toggle between metrics in the same visualization, reducing dashboard clutter and improving user experience.
**Implementation:**
- S_YTD =
VAR selected_value = SELECTEDVALUE(Slc_Values[Values])
VAR result = SWITCH(selected_value,"Sales", [YTD_Sales],"Quantity", [YTD_Quantity],"Gross Profit", [YTD_GrossProfit],BLANK()) RETURN result
- S_PYTD =
VAR selected_value = SELECTEDVALUE(Slc_Values[Values])
VAR result = SWITCH(selected_value,"Sales", [PYTD_Sales],"Quantity", [PYTD_Quantity],"Gross Profit", [PYTD_GrossProfit],BLANK()) RETURN result<br>

**Technical Benefits:** <br>
**Performance optimization:** Using variables (VAR) to store SELECTEDVALUE() calculates it once rather than multiple times within the SWITCH statement<br>
**Measure reduction:** This pattern reduces the total measure count by 67% (from 6 separate YTD/PYTD measures per visual to 2 dynamic measures)<br>
**Maintainability:** Changes to underlying calculations only need to be made in the base measures, not in multiple visual-specific measures<br>
**User Experience:** The slicer labeled "Values" in the top-right of the dashboard (showing Gross Profit, Quantity, Sales options) drives these SWITCH measures, allowing instant metric switching without page navigation.


5. **Visualization Strategy & Design Choices**

Each visualization was selected based on the specific analytical question it answers:

- **Treemap** (Bottom-left: "Bottom 10 YTD vs PYTD | Country") <br>
Purpose: Prioritize investigation areas by visualizing underperforming countries with size representing impact magnitude<br>
Why a treemap: Size encoding enables immediate pattern recognition—larger tiles = bigger problems requiring urgent attention<br>
Business value: Eliminates need to scan tables of numbers; visual hierarchy guides where to focus first

- **Waterfall Chart** (Center: "Gross Profit YTD vs PYTD | Month - Country - Product") <br>
Purpose: Root cause analysis showing which specific months, countries, and products contribute to overall variance<br>
Why a waterfall: Shows cumulative contribution—stakeholders can see that a large drop in one month might be offset by gains in others<br>
Business value: Moves beyond "we're down 265K" to "we're down because of these three specific drivers"

- **Scatter Plot** (Bottom-right: "Account Profitability Segmentation | GP% and Gross Profit") <br>
Purpose: Identify accounts with unfavorable profitability profiles (high volume but low margin, or low volume with low margin)<br>
Why a scatter plot: Reveals the relationship between two continuous variables; the dotted reference line (GP% = 0.4) creates a clear visual threshold for acceptable profitability<br>
Business value: Highlights accounts for pricing review or strategic de-prioritization—accounts below the line and to the left (low GP%, low volume) are prime candidates for intervention


6. **Key Findings & Insights**

**Finding 1: Geographic Performance Disparity**<br>
*Observation:* <br>
Bottom-10 treemap shows Czech Republic (-55.44K), Finland (-53.10K), and Colombia (-38.77K) driving -$147K YTD underperformance vs PYTD (55% of total negative variance).<br>
*Root Cause:* <br>
Waterfall analysis shows consistent monthly declines, indicating structural issues (capacity, competition, operational inefficiencies) rather than one-off events.<br>
*Why this matters:* <br>
Using YTD vs PYTD controls for seasonality, enabling meaningful like-for-like comparisons rather than misleading period-over-period noise.<br>
*Business Impact:* <br>
Enables targeted intervention on three countries instead of broad, inefficient cost-cutting across all operations.

**Finding 2: High-Performer Identification**<br>
*Observation:* <br>
Philippines delivers +74.91% YTD growth vs PYTD while maintaining GP% >0.40.<br>
*Cross validation:* <br>
Treemap: largest positive contributor;<br>
Scatter plot: high growth with healthy margins (no margin dilution);<br>
Waterfall: growth sustained across multiple months.<br>
*Why this matters:* <br>
Scatter plot intentionally distinguishes “good growth” (volume + margin) from “bad growth” (volume at the expense of margin). Philippines sits clearly in the favorable quadrant.<br>
*Business Impact:* <br>
Philippines becomes a replication benchmark for demand planning, production scheduling, and commercial execution in similar markets.

**Finding 3: Product Mix Optimization Opportunity**<br>
*Observation:* <br>
Indoor products sustain 0.42 GP%, while Outdoor products average 0.38 GP% but deliver ~23% higher volume.<br>
*Insight:* <br>
Current performance reflects a volume-over-margin bias. This may be strategic, but it should be deliberate and measured.<br>
*Why this matters:* <br>
Robust GP% calculation (DIVIDE with error handling) ensures reliable margin analysis even in small or filtered segments, supporting confident pricing decisions.<br>
*Business Impact:* <br>
A modeled 5% Outdoor price increase with 10% volume tolerance would generate ~+$210K incremental GP, to be weighed against market share and fixed-cost considerations.


7. **Recommendations & Business Impact**

**Recommendation 1:** Address Bottom Performers Through Targeted Intervention<br>
*Priority Countries:* Czech Republic (-55.44K), Finland (-53.10K), Colombia (-38.77K) <br>
*Recommended Actions:*
- Conduct operational audits to identify root causes (capacity, workforce, competition)
- Implement monthly performance reviews using this dashboard (escalation threshold: -10K monthly variance)
- Benchmark against Philippines operational practices to identify transferable improvements <br>
*How the dashboard enables this:* <br>
Bottom-10 treemap provides immediate visibility, enabling weekly monitoring instead of quarterly reviews.<br>
*Expected Impact:* <br>
Recovering these three countries to breakeven vs PYTD would add +$147K annually (55% of current gap).

**Recommendation 2:** Replicate High-Performer Practices <br>
*Model Country:* Philippines (+74.91% YTD growth, GP% >0.40)<br>
*Recommended Actions:*
- Document key practices (forecasting, scheduling, suppliers, incentives)
- Pilot replication in 3–5 comparable markets
- Run quarterly benchmarking with gap-closure plans vs Philippines<br>
*How the dashboard enables this:* <br>
Country-Product-Month waterfall isolates which product lines drive performance (notably Indoor and Landscape).<br>
*Expected Impact:* <br>
If 3 countries reach 30% of Philippines’ growth rate, incremental GP = +$450K annually.

**Recommendation 3:** Optimize Product Mix for Profitability<br>
*Target:* Outdoor category (0.38 GP% vs 0.42 Indoor)<br>
*Recommended Actions:* <br>
- Test 3–5% price increases in select markets
- Identify high-volume Outdoor accounts with GP% <0.35 for pricing actions
- Review low-margin Outdoor SKUs for redesign or discontinuation<br>
*How the dashboard enables this:* <br>
Scatter plot highlights high-volume, low-GP% accounts where pricing actions have maximum impact.<br>
*Expected Impact:* <br>
A 5% price increase on 50% of Outdoor volume yields +$175K incremental GP annually (conservative case).


8. **Future Enhancements**

- **Forecast vs Actual Integration**

Incorporate budget/forecast data to enable Plan vs Actual variance analysis alongside YTD vs PYTD
Add traffic-light indicators (Red/Yellow/Green) based on variance thresholds

- **Profitability Deep-Dive**

Integrate operational cost data (COGS, logistics, overhead allocation) to transition from Gross Profit to Net Profit analysis
Build product-level P&L views to identify true bottom-line contributors

- **Predictive Analytics**

Add trend-based forecasting using exponential smoothing or regression to project Q4 performance based on YTD actuals
Implement seasonality indexing to improve year-over-year comparisons during promotional periods


9. **Project Reflection**

Why This Dashboard Matters:<br>
This project demonstrates the complete analytics workflow—from raw data extraction through final stakeholder delivery—with a focus on translating technical capability into business value.<br>
Every technical decision (star schema, SWITCH measures, specific visualizations) was made to solve a real business problem:
- Star schema enables scalable growth as data volume increases
- SWITCH measures reduce maintenance burden and improve user experience
- Waterfall charts answer "why" not just "what changed"
- Scatter plots reveal relationships that tabular reports miss

The goal wasn't to showcase every Power BI feature, but to build a tool that operations leadership actually uses weekly to make better decisions. The -$265K variance didn't matter until we could explain it came from three specific countries with fixable issues, not broad market decline.
