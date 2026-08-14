# Data Quality Report

## 1. Purpose

This document records the initial data profiling and quality assessment performed on the Online Retail II dataset before any data cleaning or transformation.

The purpose of this assessment is to understand the structure, granularity, completeness, and potential quality issues within the source data before defining transformation rules.

No modifications were made to the original source dataset during this profiling stage.

---

## 2. Dataset Structure

The source workbook contains two worksheets:

* `Year 2009-2010`
* `Year 2010-2011`

The dataset contains the following eight fields:

| Field       | Description                                          |
| ----------- | ---------------------------------------------------- |
| Invoice     | Invoice identifier                                   |
| StockCode   | Product or transaction item identifier               |
| Description | Product or transaction description                   |
| Quantity    | Number of units associated with the transaction line |
| InvoiceDate | Date and time of the transaction                     |
| Price       | Unit price                                           |
| Customer ID | Customer identifier                                  |
| Country     | Customer/transaction country                         |

---

## 3. Dataset Size

### Year 2009-2010

525,461 transaction-line records.

### Year 2010-2011

541,910 transaction-line records.

### Combined Dataset

**1,067,371 transaction-line records.**

---

## 4. Dataset Grain

One row does **not** represent one complete customer order.

Multiple rows can share the same Invoice number because an invoice can contain multiple products.

Therefore, the grain of the transaction dataset is:

> **One row represents one product or transaction line within an invoice.**

This distinction is important when calculating metrics such as order count, average order value, product sales, and customer purchase frequency.

---

## 5. Missing Data

Initial profiling of the `Year 2009-2010` worksheet identified missing values in two fields.

| Field       | Missing Records | Approx. % of Records |
| ----------- | --------------: | -------------------: |
| Customer ID |         107,927 |               20.54% |
| Description |           2,928 |                0.56% |

### Customer ID

Approximately one-fifth of transaction-line records in the first worksheet do not contain a Customer ID.

These records should not automatically be removed.

Transactions without Customer IDs may still contain valid information for company-level sales analysis but cannot reliably be used for customer-level analysis such as RFM segmentation, customer lifetime value, or purchase frequency.

### Description

A smaller number of records contain missing product descriptions.

Further investigation will be required to determine whether these records represent valid transactions, adjustments, or incomplete product information.

---

## 6. Quantity Anomalies

The Quantity field contains both positive and negative values and includes extreme observations.

### Minimum Observed Quantity

**-9,600**

Example record:

* Invoice: `519017`
* StockCode: `22759`
* Description: Missing
* Quantity: `-9,600`
* Price: `0`

The combination of a large negative quantity, zero price, and missing description suggests that this may represent an inventory adjustment, correction, or other non-standard transaction rather than a conventional customer return.

### Maximum Observed Quantity

**19,152**

Example record:

* Invoice: `497946`
* StockCode: `37410`
* Description: `BLACK AND WHITE PAISLEY FLOWER MUG`
* Quantity: `19,152`
* Price: `0.10`
* Customer ID: `13902`
* Country: `Denmark`

Although this quantity is unusually large, the record contains a valid product description, positive price, customer identifier, and country.

Because the dataset includes wholesale transactions, large quantities should not automatically be treated as errors.

---

## 7. Price Anomalies

Extreme values were also identified within the Price field.

### Minimum Observed Price

**-53,594.36**

Example record:

* Invoice: `A506401`
* StockCode: `B`
* Description: `Adjust bad debt`
* Quantity: `1`
* Price: `-53,594.36`
* Customer ID: Missing
* Country: `United Kingdom`

This appears to represent an accounting adjustment rather than a normal merchandise transaction.

### Maximum Observed Price

**25,111.09**

Example record:

* Invoice: `C512770`
* StockCode: `M`
* Description: `Manual`
* Quantity: `-1`
* Price: `25,111.09`
* Customer ID: `17399`
* Country: `United Kingdom`

This record also appears to represent a non-standard transaction rather than a conventional product sale.

---

## 8. Cancellations

Invoice identifiers beginning with `C` indicate cancelled transactions.

Example:

`C512770`

Cancellation records frequently contain negative quantities.

These transactions should not automatically be treated as data errors because they contain meaningful information about returns and cancellations.

A cancellation indicator may be created during transformation to distinguish cancelled transactions from normal sales.

---

## 9. Non-Product Stock Codes

Initial profiling found that not every StockCode represents physical merchandise.

Examples include:

| StockCode | Description     | Potential Type         |
| --------- | --------------- | ---------------------- |
| POST      | POSTAGE         | Postage/service charge |
| M         | Manual          | Manual transaction     |
| B         | Adjust bad debt | Accounting adjustment  |

These records require classification before product-level analysis.

Including them as normal products could distort product rankings, product profitability calculations, and product-level KPIs.

---

## 10. Geographic Data Quality

The dataset contains transactions across more than 30 geographic categories.

Initial profiling identified values requiring further consideration.

### EIRE

`EIRE` refers to Ireland and may require standardization to a consistent country naming convention.

Potential transformation:

`EIRE` → `Ireland`

### Unspecified

`Unspecified` does not represent a specific country and should not automatically be assigned to another geography.

It should be retained as an unknown or unspecified geographic category unless additional evidence allows the location to be reliably determined.

---

## 11. Preliminary Data Quality Principles

The initial profiling suggests that cleaning rules should be based on business context rather than simply deleting unusual records.

Potential principles include:

1. Preserve the original raw dataset unchanged.
2. Do not automatically remove transactions with missing Customer IDs.
3. Exclude unidentified customers only from analyses requiring customer-level identification.
4. Distinguish cancellations from normal sales.
5. Distinguish merchandise from postage, manual transactions, and accounting adjustments.
6. Investigate extreme quantities before treating them as outliers.
7. Investigate zero and negative prices before removing records.
8. Standardize geographic categories where appropriate.
9. Preserve explicitly unknown geographic values rather than inventing missing information.
10. Document all transformation decisions.

---

## 12. Next Steps

The next stage of the project will define formal data-cleaning and transformation rules.

These rules will later be implemented as a reproducible transformation pipeline using Microsoft Excel Power Query.

