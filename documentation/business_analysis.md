# Business Performance Analysis

## Overview

This stage of the Nexora Commerce Business Intelligence & Forecasting project uses the Power Pivot star schema and DAX KPI engine to analyze business performance across time, geography, products, and customers.

The objective is not simply to report metrics, but to understand the business drivers behind those metrics and identify actionable opportunities, risks, and data-quality considerations.

---

## 1. Revenue and Growth Analysis

Monthly business performance was analyzed using:

- Core Sales Revenue
- Core Orders
- Average Order Value (AOV)
- Month-over-Month (MoM) Revenue Growth
- Year-over-Year (YoY) Revenue Growth

### Key Finding: Late-Year Revenue Seasonality

Monthly revenue shows a strong increase during the later months of the year, particularly around September through November.

The recurrence of this pattern across available years suggests that the business experiences meaningful late-year seasonality.

### Business Implication

Inventory requirements, order-processing capacity, and commercial activity may need to increase ahead of the late-year demand period.

### Recommendation

Use historical seasonal patterns as an input into inventory planning, demand forecasting, staffing, and promotional planning.

### Important Time-Coverage Caveat

The dataset does not contain equal full-year periods:

- 2009 begins on December 1.
- 2010 contains a full calendar year.
- 2011 ends on December 9.

Therefore, annual and YoY comparisons must be interpreted carefully.

For example, the apparent decline in December 2011 does not represent a full-month business decline because only the first nine days of December are available.

Similarly, comparisons between full-year 2010 and partial-year 2009 are not directly comparable.

---

## 2. Geographic Performance

Country-level performance was analyzed using Core Sales Revenue, Core Orders, Average Order Value, and Unique Customers.

### Key Finding: Strong UK Revenue Concentration

The United Kingdom generates approximately **17.60 million** in Core Sales Revenue compared with approximately:

- Ireland: 0.64 million
- Netherlands: 0.55 million
- Germany: 0.43 million
- France: 0.36 million

Total Core Sales Revenue across the dataset is approximately **20.49 million**.

The United Kingdom therefore contributes approximately **86% of total Core Sales Revenue**.

The Top 10 countries collectively contribute approximately **97.7%** of Core Sales Revenue.

### Business Implication

The business has substantial geographic concentration.

The UK represents the core commercial market, while international markets currently contribute a relatively small proportion of revenue.

This concentration provides a strong domestic base but also creates geographic dependency.

### Recommendation

Protect the UK customer and revenue base while investigating whether high-performing international markets such as Ireland, the Netherlands, Germany, and France offer scalable expansion opportunities.

Expansion decisions should consider profitability, customer acquisition cost, logistics, and market potential rather than revenue alone.

---

## 3. Product Performance

Product performance was evaluated using:

- Core Sales Revenue
- Core Units Sold
- Core Orders

Separate rankings were used for revenue and unit volume because a "best-selling product" can have different meanings.

### Key Finding: Revenue Leadership and Volume Leadership Differ

High unit volume does not necessarily imply the highest revenue contribution.

For example, some products appear across thousands of customer orders, while others generate unusually large unit volumes through a very small number of transactions.

A notable example was **PAPER CRAFT, LITTLE BIRDIE**, which generated approximately 80,995 units from only one Core Order.

This represents bulk-order concentration rather than broad customer popularity.

### Business Implication

Product performance should not be evaluated using a single metric.

Revenue, units sold, and order frequency provide different information:

- Revenue measures financial contribution.
- Units measure volume.
- Orders provide an indication of how broadly/frequently a product is purchased.

### Recommendation

Use multiple product-performance metrics when making merchandising, inventory, and demand-planning decisions.

High-revenue products with broad order frequency may deserve particularly strong inventory protection, while bulk-order-driven products should be evaluated separately.

---

## 4. Analytical QA Finding: DOTCOM POSTAGE

During product analysis, **DOTCOM POSTAGE** appeared among the highest-revenue "products" despite the Product filter being applied.

Investigation identified its StockCode as:

`DOT`

The existing ETL classification recognized `POST` as Postage but did not classify `DOT`.

As a result, DOTCOM POSTAGE had incorrectly inherited the default `Product` classification.

### ETL Correction

The Power Query business rule was updated so that:

- `POST` → Postage
- `DOT` → Postage
- `M` → Manual Adjustment
- `B` → Bad Debt Adjustment
- Other StockCodes → Product

The rule was corrected in both:

- `fact_Transactions`
- `dim_Product`

After refreshing the Data Model, DOTCOM POSTAGE correctly disappeared from Product-only rankings.

### Key Lesson

Downstream analytics can reveal upstream data-quality and business-rule issues.

Rather than manually excluding DOTCOM POSTAGE from a report, the classification logic was corrected at the transformation layer so that all downstream analyses inherit the correct business definition.

---

## 5. Customer Performance

Customer analysis evaluated:

- Core Sales Revenue
- Core Orders
- Average Order Value
- Core Units Sold

### Key Finding: Customer Value Is Generated in Different Ways

High-value customers do not all exhibit the same purchasing behavior.

Some customers generate high revenue through frequent purchasing, while others generate substantial revenue from a small number of very large orders.

For example, one high-value customer generated revenue through hundreds of orders, while another generated substantial revenue from only a few transactions with an exceptionally high Average Order Value.

### Business Implication

Treating all high-value customers as one homogeneous segment may hide important behavioral differences.

### Recommendation

Future customer segmentation should distinguish between groups such as:

- High-frequency customers
- High-ticket customers
- High-frequency/high-value customers
- Lower-engagement customers

These segments could support differentiated retention, account-management, and marketing strategies.

---

## 6. Missing Customer IDs

A significant amount of transaction activity has no Customer ID.

When Customer ID is used in PivotTable rankings, these transactions appear together as `(blank)`.

This aggregated blank category must **not** be interpreted as a single customer.

### Business Implication

Customer-level analysis only represents identifiable customers.

Revenue associated with missing Customer IDs cannot reliably be attributed to individual customers.

### Recommendation

Known-customer and unidentified-customer activity should be distinguished explicitly in customer analytics.

The missing-customer population should also be considered when interpreting customer counts, concentration, and segmentation results.

---

## 7. Weekday Performance

Performance was compared across days of the week using:

- Core Sales Revenue
- Core Orders
- Average Order Value

### Key Findings

**Thursday** generated the highest Core Sales Revenue at approximately **4.12 million**, supported by the highest order volume of approximately **8,522 Core Orders**.

However, Thursday did not have the highest Average Order Value.

**Monday** produced an Average Order Value of approximately **524**, while Tuesday was also relatively high at approximately **522**.

This demonstrates that revenue performance is influenced by both:

1. Order frequency
2. Order value

### Saturday Anomaly

Saturday showed extremely low activity, with only approximately **30 Core Orders** across the available dataset.

This should not automatically be interpreted as weak Saturday customer demand.

Possible explanations could include operating schedules, transaction-processing practices, or other business conditions not represented in the dataset.

### Recommendation

Investigate operational context before making decisions based on the Saturday pattern.

For higher-volume weekdays such as Thursday, ensure inventory and operational capacity are aligned with demand.

---

## 8. Analytical Principles Applied

Several important analytical principles emerged during this stage.

### Correlation Is Not Causation

The analysis identifies patterns in historical data but does not automatically establish why those patterns occurred.

For example:

- High Thursday revenue does not prove that Thursday itself causes higher spending.
- Low Saturday activity does not prove that customers dislike shopping on Saturdays.
- Strong UK revenue does not prove that international expansion will succeed.

Additional business context would be required to establish causality.

### Finding → Interpretation → Hypothesis → Recommendation

Business analysis should distinguish between:

1. **Finding** — What does the data show?
2. **Interpretation** — What might the pattern mean?
3. **Hypothesis** — What could explain it?
4. **Recommendation** — What should the business investigate or do next?

This prevents technically correct analysis from turning into unsupported business conclusions.

---

## 9. Business Recommendations

Based on the analysis, Nexora Commerce should consider the following:

1. **Prepare for late-year demand peaks** by incorporating seasonality into inventory, staffing, and forecasting decisions.

2. **Protect the UK revenue base** because approximately 86% of Core Sales Revenue originates from the United Kingdom.

3. **Investigate international growth opportunities** in existing higher-performing markets while considering profitability and operational feasibility.

4. **Evaluate products using multiple KPIs**, particularly Revenue, Units Sold, and Order Frequency.

5. **Separate bulk-order anomalies from broad product popularity** when interpreting high-volume products.

6. **Segment high-value customers by purchasing behavior**, particularly high-frequency versus high-ticket customers.

7. **Distinguish unidentified customer activity from known-customer performance** in future customer reporting.

8. **Investigate weekday operational patterns**, particularly the extremely low Saturday transaction activity.

9. **Use matched-period comparisons** when analyzing growth because the dataset contains partial years.

---

## 10. Excel Analysis Outputs

The Business Intelligence workbook contains dedicated analytical worksheets including:

- `04_COUNTRY_ANALYSIS` — geographic performance
- `05_KPI_SUMMARY` — company-level KPI summary
- `06_REVENUE_ANALYSIS` — monthly revenue and growth analysis
- `07_PRODUCT_ANALYSIS` — product revenue and volume analysis
- `08_CUSTOMER_ANALYSIS` — top-customer analysis
- `09_CUSTOMER_VALUE` — customer value and purchasing behavior
- `10_TIME_PATTERNS` — weekday performance analysis

These analyses are powered by the same Power Pivot Data Model and reusable DAX KPI layer.

---

## 11. Step 8 Outcome

Step 8 transformed the technical BI model into a business-analysis layer.

The project now supports analysis across:

**Time → Geography → Products → Customers → Purchasing Behavior**

The analytical outputs will serve as inputs for the next stages of the project:

- Forecasting
- Scenario analysis
- Optimization
- Executive dashboard development