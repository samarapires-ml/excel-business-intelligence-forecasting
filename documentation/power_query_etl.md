# Power Query ETL Pipeline

## Nexora Commerce --- Excel Business Intelligence & Forecasting Platform

This document describes the **Power Query ETL pipeline** built for the
Nexora Commerce Excel Business Intelligence & Forecasting project. The
pipeline prepares the UCI **Online Retail II** dataset for downstream
data modeling, DAX, business analysis, forecasting, optimization, and
dashboarding.

## Pipeline Summary

  Item                       Result
  -------------------------- ----------------------
  Source                     UCI Online Retail II
  2009--2010 rows            525,461
  2010--2011 rows            541,910
  Final fact-table rows      **1,067,371**
  Primary analytical table   `fact_Transactions`
  Target                     Excel Data Model
  Refresh status             **Successful**

The final pipeline is refreshable:

``` text
Raw Online Retail II workbook
        |
        +-- Year 2009-2010 --> src_2009_2010 --+
        |                                      |
        +-- Year 2010-2011 --> src_2010_2011 --+--> fact_Transactions
                                                       |
                                                       +--> QA queries
                                                       |
                                                       +--> Excel Data Model
```

## 1. Extract

The source workbook contains two yearly transaction sheets:

-   `Year 2009-2010` --- 525,461 rows
-   `Year 2010-2011` --- 541,910 rows

Both were connected through **Data → Get Data → From File → From
Workbook** and opened using **Transform Data** so that preparation
occurred in Power Query rather than through manual worksheet edits.

Power Query stores the extraction and transformation logic as sequential
**Applied Steps**, making the process repeatable and auditable.

## 2. Schema Standardization

Data types were selected according to the **business meaning** of each
field rather than simply how the values looked.

  -----------------------------------------------------------------------
  Column                  Data Type               Rationale
  ----------------------- ----------------------- -----------------------
  `Invoice`               Text                    Identifier;
                                                  cancellation invoices
                                                  can begin with `C`

  `StockCode`             Text                    Identifier; includes
                                                  alphanumeric/special
                                                  codes

  `Description`           Text                    Product/transaction
                                                  description

  `Quantity`              Whole Number            Unit count; can be
                                                  negative

  `InvoiceDate`           Date/Time               Preserves transaction
                                                  date and time

  `Price`                 Decimal Number          Unit price

  `Customer ID`           Text                    Identifier rather than
                                                  arithmetic measure

  `Country`               Text                    Geographic category
  -----------------------------------------------------------------------

A key design principle was that **numeric-looking identifiers are not
necessarily numeric measures**. For example, Customer ID is stored as
text because summing or averaging customer identifiers has no analytical
meaning.

## 3. Combining the Source Tables

The two yearly source tables share the same schema, so they were
combined using **Append Queries as New**.

The resulting analytical query was named:

`fact_Transactions`

Append was appropriate because the requirement was to **stack rows
vertically**. Conceptually, Power Query Append is similar to SQL
`UNION ALL`. Power Query Merge, by contrast, is used to join related
tables horizontally using matching keys.

The resulting fact table contains **1,067,371 transaction-line
records**.

### Grain

The grain of `fact_Transactions` is:

> **One product transaction line within an invoice.**

An invoice can therefore appear across multiple rows. This grain was
preserved when evaluating duplicates and designing downstream
calculations.

## 4. Cleaning and Standardization

The cleaning strategy prioritized **preserving business meaning** rather
than aggressively deleting unusual records.

### Text cleaning

Relevant text fields were trimmed to remove leading/trailing whitespace.
`Description` and `Country` were also cleaned to remove
non-printable/control characters.

### Missing Customer IDs

Transactions with missing Customer IDs were retained.

These rows remain useful for:

-   revenue analysis
-   product performance
-   geographic analysis
-   seasonality
-   forecasting

They will be excluded only from analyses that specifically require
customer identity, such as RFM, retention, repeat-purchase behavior, or
customer lifetime value.

### Missing Descriptions

Rows with missing descriptions were retained because they may represent
operational adjustments or other meaningful non-standard records.

### Country standardization

`EIRE` was standardized to `Ireland`.

`Unspecified` was deliberately preserved rather than assigned to a
guessed country. The pipeline avoids fabricating geographic information
when the source does not provide sufficient evidence.

### Duplicate handling

Duplicates were **not blindly removed**. Because the dataset is
transaction-line level, apparently repeated rows may represent
legitimate repeated purchases. Without a reliable unique
transaction-line identifier, indiscriminate deduplication could remove
valid business activity.

## 5. Transaction Classification

Rather than deleting cancellations, negative quantities, and known
non-product records, the pipeline classifies them.

### `InvoiceType`

``` text
Invoice begins with C --> Cancellation
Otherwise             --> Regular
```

The source documentation identifies C-prefixed invoices as
cancellations.

### `QuantityType`

``` text
Quantity < 0 --> Negative
Quantity = 0 --> Zero
Quantity > 0 --> Positive
```

Quantity direction is deliberately modeled independently from invoice
type.

### `RecordType`

Known special StockCodes were classified as:

``` text
POST --> Postage
M    --> Manual Adjustment
B    --> Bad Debt Adjustment
Else --> Product
```

This prevents known operational/accounting records from automatically
being treated as standard product transactions.

### `AnalysisStatus`

A row is considered a core sale only when all three conditions are
satisfied:

``` powerquery
if [RecordType] = "Product"
    and [InvoiceType] = "Regular"
    and [QuantityType] = "Positive"
then "Core Sale"
else "Review / Non-Core"
```

`Review / Non-Core` does **not** mean bad data. It preserves
cancellations, negative adjustments, postage, manual adjustments,
bad-debt records, and other non-standard activity for separate analysis.

### `RevenueType`

``` text
Revenue < 0 --> Negative
Revenue = 0 --> Zero
Revenue > 0 --> Positive
```

This provides a simple signed-value classification for QA and downstream
analysis.

## 6. Feature Engineering

The pipeline creates analysis-ready **row-level features** while leaving
aggregate KPIs for the DAX layer.

### Revenue

``` text
Revenue = Quantity × Price
```

The sign is preserved. Negative quantities can therefore produce
negative transaction values instead of being artificially converted to
positive amounts.

### Time features

The following fields were derived from `InvoiceDate`:

  -----------------------------------------------------------------------
  Feature                             Purpose
  ----------------------------------- -----------------------------------
  `Year`                              Annual analysis

  `Quarter`                           Quarterly performance and
                                      seasonality

  `Month`                             Chronological month sorting

  `MonthName`                         Human-readable month label

  `DayName`                           Weekday analysis

  `Hour`                              Time-of-day analysis

  `YearMonth`                         Monthly trend/forecasting key

  `TransactionDate`                   Date-only field for future
                                      Date-dimension relationship
  -----------------------------------------------------------------------

`YearMonth` was created using:

``` powerquery
Text.From([Year]) & "-" & Text.PadStart(Text.From([Month]), 2, "0")
```

Zero-padding produces values such as `2010-01`, `2010-02`, and
`2010-10`, allowing reliable chronological ordering when the field is
represented as text.

Aggregate metrics such as Total Revenue, Average Order Value,
Cancellation Rate, and growth measures are intentionally deferred to
**DAX measures** rather than stored as additional columns across more
than one million fact rows.

## 7. Quality Assurance

Dedicated QA queries reference the final fact table so that validation
can occur without modifying `fact_Transactions`.

  -----------------------------------------------------------------------
  QA Query                            Purpose
  ----------------------------------- -----------------------------------
  `qa_StockCodes`                     Targeted inspection of unique
                                      StockCodes

  `qa_RowCount`                       Validate final fact-table row count

  `qa_AnalysisStatus`                 Validate Core Sale vs Review /
                                      Non-Core classification

  `qa_TransactionTypes`               Cross-check InvoiceType ×
                                      QuantityType combinations
  -----------------------------------------------------------------------

### Row-count validation

Expected source total:

``` text
525,461 + 541,910 = 1,067,371
```

`qa_RowCount` returned:

**1,067,371 --- PASS**

No rows were unintentionally lost during the ETL process.

### AnalysisStatus validation

  Analysis Status                Rows
  ------------------- ---------------
  Core Sale                 1,041,638
  Review / Non-Core            25,733
  **Total**             **1,067,371**

Every transaction received an AnalysisStatus classification.

### InvoiceType × QuantityType validation

  Invoice Type   Quantity Type          Rows
  -------------- --------------- -----------
  Regular        Positive          1,044,420
  Cancellation   Negative             19,493
  Regular        Negative              3,457
  Cancellation   Positive                  1

This validation produced an important finding: **3,457 negative-quantity
rows occur on regular invoices**. Therefore, negative quantity cannot
safely be treated as synonymous with cancellation.

The single Cancellation + Positive record was preserved for
investigation rather than silently removed.

## 8. Query Architecture and Naming

The final Power Query inventory is:

  Query                   Role                    Load Strategy
  ----------------------- ----------------------- ------------------
  `src_2009_2010`         Source/staging          Connection Only
  `src_2010_2011`         Source/staging          Connection Only
  `fact_Transactions`     Analytical fact table   Excel Data Model
  `qa_StockCodes`         QA                      Connection Only
  `qa_RowCount`           QA                      Connection Only
  `qa_AnalysisStatus`     QA                      Connection Only
  `qa_TransactionTypes`   QA                      Connection Only

Naming conventions communicate each query's role:

-   `src_` --- source/staging
-   `fact_` --- transaction/event fact table
-   `qa_` --- quality assurance
-   `dim_` --- dimension table (introduced in the modeling stage)

QA queries were created using **Reference** rather than Duplicate so
that they remain downstream of the transformed fact table.

## 9. Loading to the Excel Data Model

A standard Excel worksheet supports a maximum of **1,048,576 rows**.

`fact_Transactions` contains **1,067,371 rows**, exceeding that limit by
18,795 rows.

The final fact table was therefore configured as:

``` text
Only Create Connection
+
Add this data to the Data Model
```

Source and QA queries remain connection-only because they are
staging/helper objects rather than analytical model tables.

The Data Model provides the foundation for:

-   Power Pivot
-   relationships
-   DAX measures
-   large PivotTables
-   dimensional modeling
-   downstream dashboard analysis

## 10. Refresh Validation

After the load configuration was completed, **Data → Refresh All** was
executed successfully.

The refresh verifies that Excel can:

1.  reconnect to the raw workbook,
2.  read both yearly source sheets,
3.  reapply schema and cleaning rules,
4.  append the source tables,
5.  recreate classification fields,
6.  recreate engineered features,
7.  recompute QA queries, and
8.  reload `fact_Transactions` into the Excel Data Model.

The project therefore contains a **repeatable and refreshable ETL
pipeline**, rather than a one-time manually cleaned dataset.

## 11. Key Design Principles

The ETL layer demonstrates several core analytics/data-engineering
practices:

-   Understand table grain before deduplication or aggregation.
-   Preserve unusual records until their business meaning is understood.
-   Treat identifiers according to semantics rather than visual format.
-   Separate source/staging, analytical, and QA layers.
-   Prefer classification over destructive deletion when records may
    have future analytical value.
-   Validate transformation logic with explicit row counts and category
    cross-checks.
-   Use connection-only helper queries to avoid unnecessary
    worksheet/model clutter.
-   Reserve dynamic aggregate KPIs for the DAX layer.
-   Build repeatable transformations that can be rerun through refresh.

## 12. Outcome

The completed Power Query layer transforms two raw yearly transaction
datasets into a validated **1.067-million-row analytical fact table**
stored in the Excel Data Model.

This ETL foundation is ready for the next stage of the project:

**Data Modeling → Dimension Tables → Relationships → Power Pivot → DAX
KPI Engine**

------------------------------------------------------------------------

### Portfolio Summary

> Built a refreshable Power Query ETL pipeline processing 1.067 million
> retail transaction records, combining multi-year source data,
> standardizing schema and categorical values, preserving and
> classifying cancellations and operational adjustments, engineering
> temporal and financial features, implementing QA validation queries,
> and loading the final transaction fact table into the Excel Data Model
> for downstream Power Pivot and DAX analysis.
