# Data Model & Star Schema

## Overview
This stage of the **Nexora Commerce — Excel Business Intelligence & Forecasting Platform** converts the cleaned Power Query output into a relational analytical model using Excel's Data Model and Power Pivot.

The model follows a **star schema**: a central transaction fact table is surrounded by descriptive dimension tables for date, product, customer, and country. This supports efficient filtering, aggregation, PivotTable analysis, and the DAX KPI layer developed next.

## Model Architecture

```text
                         dim_Date
                            1
                            |
                            *
                       fact_Transactions
                      /       |        \
                     *        *         *
                     |        |         |
                     1        1         1
              dim_Product dim_Customer dim_Country
```

| Table | Role | Validated Rows | Purpose |
|---|---|---:|---|
| `fact_Transactions` | Fact | 1,067,371 | Transaction-line events and measures |
| `dim_Date` | Dimension | 739 | Calendar/time analysis |
| `dim_Product` | Dimension | 5,131 | Product identification and description |
| `dim_Customer` | Dimension | 5,942 | Customer filtering |
| `dim_Country` | Dimension | 43 | Geographic filtering |

The grain of `fact_Transactions` is **one transaction line per row**.

## Fact Table — `fact_Transactions`
The fact table contains the business events being analyzed, including transaction identifiers, relationship keys, classifications, engineered time fields, quantities, prices, and revenue.

Important fields include `Invoice`, `StockCode`, `Description`, `Quantity`, `InvoiceDate`, `TransactionDate`, `Price`, `Customer ID`, `Country`, `Revenue`, `InvoiceType`, `QuantityType`, `RecordType`, `AnalysisStatus`, and `RevenueType`.

The table contains **1,067,371 transaction-line records** and is stored in the Data Model because the dataset exceeds Excel's worksheet row limit.

## Dimension Tables

### `dim_Date`
Validated rows: **739**

Key fields include `Date`, `Year`, `Quarter`, `MonthNumber`, `MonthName`, `Day`, `DayName`, and `YearMonth`.

`Date` is unique and acts as the dimension key. The table was marked as the official **Date Table** in Power Pivot using the `Date` column, preparing the model for time-intelligence DAX.

### `dim_Product`
Validated rows: **5,131**

Fields: `StockCode`, `Description`, `DescriptionCount`, `RecordType`.

`StockCode` is the unique dimension key. Because a StockCode can have different descriptions across transaction history, the dimension selects a deterministic representative description: the most frequently occurring nonblank description, with description text as a tie-breaker.

Special StockCodes are classified as:
- `POST` → Postage
- `M` → Manual Adjustment
- `B` → Bad Debt Adjustment
- Other codes → Product

### `dim_Customer`
Validated rows: **5,942**

Key: `Customer ID`.

Missing customer identifiers remain in the fact data rather than inventing customer identities. The dimension contains valid unique customer keys used for filtering.

### `dim_Country`
Validated rows: **43**

Key: `Country`.

Country standardization from ETL is inherited by the model, including `EIRE` → `Ireland`. Explicit unknown geography remains `Unspecified`.

## Relationships

Four active **one-to-many (`1:*`)** relationships connect dimensions to the fact table:

| Dimension — unique key (`1`) | Fact — foreign key (`*`) |
|---|---|
| `dim_Date[Date]` | `fact_Transactions[TransactionDate]` |
| `dim_Product[StockCode]` | `fact_Transactions[StockCode]` |
| `dim_Customer[Customer ID]` | `fact_Transactions[Customer ID]` |
| `dim_Country[Country]` | `fact_Transactions[Country]` |

Each dimension key occurs once on the `1` side, while its corresponding fact foreign key can repeat.

## Cardinality, Keys & Filter Propagation
The model uses **one-to-many cardinality**. For example, one StockCode appears once in `dim_Product`, but may occur on many transaction lines in `fact_Transactions`.

```text
dim_Product[StockCode]   1 ───── *   fact_Transactions[StockCode]
```

Dimension keys are the unique/primary-side keys. The corresponding fields in `fact_Transactions` are foreign keys.

Filters propagate from dimensions toward the fact table. For example, selecting Canada in `dim_Country` filters `fact_Transactions` to Canadian transaction lines before Revenue is aggregated. Multiple dimensions can filter the fact table simultaneously.

## Product-Key Modeling Issue & Resolution
Power Pivot initially rejected the Product relationship and identified it as **many-to-many**.

Initial Power Query duplicate checks returned zero duplicate StockCodes, so the issue was investigated rather than deleting records blindly. Checks covered ordinary duplicates, null/blank keys, case differences, whitespace, hidden/control characters, and refresh state.

The critical issue was **transformation order**. StockCodes had originally been grouped before the key was fully standardized. Logically equivalent textual keys could therefore be treated as separate groups during dimension construction and later compare as equivalent in the Data Model.

The Product dimension was redesigned so key canonicalization occurs **before grouping**:

```text
Source StockCode
      ↓
Trim
      ↓
Clean
      ↓
Uppercase
      ↓
Remove invalid blank/null keys
      ↓
Group product records
      ↓
Select representative description
      ↓
One row per canonical StockCode
```

After rebuilding the dimension, duplicate-key QA returned **0 rows**, the Data Model was refreshed, and Power Pivot successfully created the Product relationship as `1:*`.

### Key lesson
**Transformation order matters.** Identifier standardization should occur before grouping, deduplication, joins, and relationship construction.

This demonstrates **key canonicalization** and **ETL-to-model validation**: ETL output must also satisfy the constraints of the downstream analytical model.

## Relationship Integrity QA
A PivotTable was created on the `Model_QA` worksheet with:

- **Rows:** `dim_Country[Country]`
- **Columns:** `dim_Date[Year]`
- **Values:** Sum of `fact_Transactions[Revenue]`

It successfully returned revenue by country across 2009, 2010, and 2011. This validated that `dim_Country` and `dim_Date` both filter `fact_Transactions`, multiple dimensions can filter the fact simultaneously, and Revenue aggregates through the model relationships.

The QA worksheet is retained as evidence of model functionality.

## Model Organization
Diagram View was organized into a readable star layout:
- `fact_Transactions` in the center
- `dim_Date` above
- `dim_Product` left
- `dim_Customer` below
- `dim_Country` right

This improves maintainability and communication without changing model behavior.

## Refresh Validation
A final **Refresh All** completed successfully after the model was built and validated:

```text
Raw Online Retail II Data
        ↓
Power Query ETL
        ↓
Fact + Dimension Queries
        ↓
Excel Data Model / Power Pivot
        ↓
1:* Star-Schema Relationships
        ↓
Model_QA PivotTable
```

The workbook was saved with the refreshed model.

## Concepts Demonstrated
- Relational data modeling
- Star schemas
- Fact and dimension tables
- Data grain
- Unique/primary-side keys
- Foreign keys
- One-to-many cardinality
- Relationship integrity
- Filter propagation
- Date dimensions
- Power Pivot and Excel Data Model
- Key canonicalization
- Transformation sequencing
- ETL-to-model validation
- Query dependencies and refresh behavior
- Model QA and analytical PivotTables

## Step 6 Outcome
The project now has a validated relational analytical layer capable of supporting DAX measures, KPI calculations, customer/product analysis, time intelligence, forecasting, and executive reporting.

**Step 6 — Data Modeling + Power Pivot: Complete**

Next: **Step 7 — DAX + KPI Engine**
