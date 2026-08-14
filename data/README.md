# Data

This directory contains the datasets used in the Excel Business Intelligence & Forecasting Platform.

## Raw Data

The primary transaction dataset is **Online Retail II** from the UCI Machine Learning Repository.

### Source

UCI Machine Learning Repository  
Dataset: Online Retail II

https://archive.ics.uci.edu/dataset/502/online+retail+ii

### Dataset Description

Online Retail II contains transaction data from a UK-based non-store online retailer.

The dataset covers transactions between December 2009 and December 2011 and contains over one million transaction records.

### Original Fields

- Invoice
- StockCode
- Description
- Quantity
- InvoiceDate
- Price
- Customer ID
- Country

### License

The dataset is distributed under the Creative Commons Attribution 4.0 International (CC BY 4.0) license.

### Data Handling

The original source dataset is preserved unchanged in the local `data/raw/` directory.

Data cleaning and transformation will be performed using Microsoft Excel Power Query to maintain a reproducible transformation pipeline.

Additional synthetic datasets will be created later in the project for customer, product, marketing, inventory, and operational analysis.
