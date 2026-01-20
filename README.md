# Power BI Operational Performance Dashboard
End-to-end Power BI dashboard designed to support operational monitoring and performance reporting through structured, validated, and consolidated data.

1. Background and Overview
This project was developed to support operational monitoring and performance reporting through a consolidated, reliable, and stakeholder-focused Power BI dashboard. The objective was to transform raw operational data into meaningful insights that enable decision-makers to track performance, identify trends, and monitor key operational indicators.
The project covered the full analytics lifecycle, including data extraction, structuring, validation, and transformation using SQL, followed by data modeling and visualization in Power BI. A strong emphasis was placed on data quality, consistency, and usability to ensure the dashboard could be confidently used for ongoing operational and performance reporting.

Project resources:
Power BI dashboard can be downloaded here: https://github.com/elenaderensis/powerbi-operational-performance-dashboard/blob/main/Power%20Bi%20Project.pbix

2. Data Structure Overview
The dataset consists of three core tables used for operational analysis:
Fact Table: Plant Fact — contains transactional metrics such as gross profit, quantity, and sales.
Dimension Tables: Accounts — provides entity-level context and segmentation.
Plant Hierarchy — enables hierarchical reporting and aggregation across plants.

Data Model
A star-schema-inspired model was implemented in Power BI, connecting the fact table to the dimension tables through one-to-many relationships. This design supports efficient filtering, consistent calculations, and scalable reporting.

Measures & Calculations
All calculations are performed in Power BI using DAX measures, including:
Base Measures: Gross Profit, Quantity, Sales
Percentage Metrics: GP%
Time-Based Measures: Prior Year to Date (PYTD) metrics for Gross Profit, Quantity, and Sales
Year to Date (YTD) metrics for Gross Profit, Quantity, and Sales

Example DAX Measures
Switch for PYTD (S_PYTD)
S_PYTD =  
VAR selected_value = SELECTEDVALUE(Slc_Values[Values])
VAR result = SWITCH(
    selected_value,
    "Sales", [PYTD_Sales],
    "Quantity", [PYTD_Quantity],
    "Gross Profit", [PYTD_GrossProfit],
    BLANK()
)
RETURN result

Switch for YTD (S_YTD)
S_YTD =  
VAR selected_value = SELECTEDVALUE(Slc_Values[Values])
VAR result = SWITCH(
    selected_value,
    "Sales", [YTD_Sales],
    "Quantity", [YTD_Quantity],
    "Gross Profit", [YTD_GrossProfit],
    BLANK()
)
RETURN result

YTD Example for Gross Profit
YTD_GrossProfit = TOTALYTD([Gross Profit], Fact_Sales[Date_Time])

This measure-based approach allows flexible reporting and dynamic comparisons of YTD vs PYTD across different metrics in Power BI.

3. Executive Summary
The final Power BI dashboard provides a consolidated view of operational performance, enabling stakeholders to:
Monitor key performance indicators at a glance
Track trends over time
Identify areas of improvement or operational risk
Support data-driven decision-making

The analysis revealed clear performance patterns across operational dimensions, highlighting periods of high activity, efficiency fluctuations, and areas where targeted improvements could drive better outcomes.

Final Dashboard Overview
<img width="872" height="490" alt="image" src="https://github.com/user-attachments/assets/a4163cd4-5f3a-4426-81b5-201a9a6ccffb" />

4. Insights Deep Dive
Trend 1: Overall Performance Evolution
The data shows a clear trend in operational performance over time, with noticeable fluctuations during specific periods. These changes align with variations in workload volume and resource allocation, suggesting a direct relationship between demand and performance outcomes.

Trend 2: Category / Segment Comparison
Certain categories consistently outperform others across key metrics. The gap between top- and bottom-performing segments indicates structural differences in processes, workload complexity, or operational maturity.

Trend 3: Efficiency and Throughput
Efficiency metrics highlight periods where throughput increased without a corresponding drop in quality, suggesting optimal operational conditions. Conversely, specific periods show declining efficiency, pointing to potential bottlenecks or capacity constraints.
Each insight was validated through carefully designed DAX calculations and cross-checked against source data to ensure consistency and reliability.

5. Recommendations
This analysis matters because it translates operational data into actionable intelligence. Based on the findings:
Focus improvement efforts on underperforming segments identified through comparative analysis.
Monitor workload spikes during critical periods to prevent performance degradation.
Standardize best-performing processes observed in high-efficiency segments.
Continue tracking KPIs using the implemented calculations to ensure consistency and comparability over time.

The chosen calculations and metrics were deliberately selected to balance clarity, accuracy, and business relevance. They allow stakeholders to move beyond raw numbers and understand why performance changes occur, not just what changed.
