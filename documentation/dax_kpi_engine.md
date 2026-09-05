\# DAX KPI Engine



\## Overview



This stage builds the analytical calculation layer for the Nexora Commerce Business Intelligence model using DAX in Power Pivot.



The KPI engine sits on top of the star schema created in the Data Model and provides reusable measures for revenue, orders, customers, products, quantity, operational quality, and time-based performance analysis.



\---



\## Core DAX Concepts



\### Measures vs Calculated Columns



Calculated columns evaluate logic row by row and store a value for every row.



Measures calculate aggregated results dynamically based on the current filter context.



The transaction-level Revenue field was created earlier in Power Query because it is a row-level calculation.



Business KPIs such as Total Revenue, Core Orders, Average Order Value, and YoY Growth were implemented as DAX measures because they must respond dynamically to filters.



\---



\## Filter Context



DAX measures automatically recalculate based on the active filter context.



Examples of filter context include:



\- Year

\- Country

\- Product

\- Customer

\- combinations of multiple dimensions



For example, Core Sales Revenue can return:



\- total revenue across the full dataset

\- revenue for a single year

\- revenue for a single country

\- revenue for a specific country and year

\- revenue for a selected product



The dimension tables filter the central fact table through the model relationships.



\---



\## CALCULATE



`CALCULATE()` evaluates an expression under a modified filter context.



It is one of the most important DAX functions used in the model.



Example:



```DAX

Core Sales Revenue:=

CALCULATE(

&#x20;   SUM(fact\_Transactions\[Revenue]),

&#x20;   fact\_Transactions\[AnalysisStatus] = "Core Sale"

)

