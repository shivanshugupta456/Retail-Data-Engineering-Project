# RetailQ Project - End-to-End Data Engineering Project

> Built for Data Engineering  | Medallion Architecture | Unity Catalog | PySpark

RetailQ is an end-to-end Data Engineering project built on Databricks.
It ingests data from multiple sources like Postgres, Salesforce and Blob,
and transforms it using Medallion Architecture (Bronze -> Silver -> Gold)
to build a Retail Sales Performance Dashboard.

**Live Project:** `retail_q` Catalog on Databricks Unity Catalog

## Architecture

**Sources -> Bronze -> Silver -> Gold -> Semantic -> Dashboard**

- **Bronze:** Raw data as it comes from source, no cleaning
- **Silver:** Cleaned, filtered and deduplicated data
- **Gold:** Business-ready aggregated data for reporting
- **Semantic:** Metric Views for consistent Dashboard metrics

## Tech Stack

- **Platform:** Databricks, Unity Catalog
- **Language:** SQL, Python (PySpark)
- **Sources:** Postgres, Salesforce, Blob Storage
- **Catalog:** `retail_q`
- **Dashboard:** Databricks Lakeview Dashboard

## Catalog Structure
retail_q
├── blob_bronze
├── postgres_bronze
├── salesforce_bronze
├── retail_silver
├── retail_gold
└── retail_semanticjavascript
## Project Structure
17_RetailQ_Project/
├── 00_Setup_Create_Catalog.sql
├── 01_Source_data/
│   ├── 01_postgres_product_history.sql
│   ├── 02_postgres_inventory_history.sql
│   ├── 03_salesforce_accounts_history.sql
│   ├── 04_salesforce_opportunities_history.sql
│   ├── 05_blob_transactions_history.sql
│   └── 06_blob_transactions_incremental.sql
├── 02_Notebooks/
│   ├── 01_blob_to_bronze.py
│   ├── 02_Gold_Views.sql
│   ├── 03_calendar.py
│   └── 04_Metric View.py
├── 03_ETL_Pipeline/
│   ├── bronze_to_silver/
│   │   ├── account.py
│   │   ├── inventory.py
│   │   ├── opportunity.py
│   │   ├── product_catalog.py
│   │   └── transactions.py
│   └── silver_to_gold/
│       └── fact_sales.py
└── 05_Dashboard/
    └── Retail Sales Performance Dashboard.lvdash.jsonjavascript
## ETL Pipeline Flow

1. **Ingestion:** Data from Postgres, Salesforce and Blob -> Bronze Schemas
2. **Bronze to Silver:** Cleaning, deduplication, data quality checks
3. **Silver to Gold:** Joins and aggregations to create `fact_sales`
4. **Semantic Layer:** Created `retail_metrics` Metric View in `retail_semantic`
5. **Dashboard:** Retail Sales Performance Dashboard built on Gold + Semantic

## Key Highlights

- Ingested 6 source tables/files from 3 different sources into Unity Catalog
- Handled both Full Load (History) and Incremental Load for Transactions
- Created `fact_sales` Gold table by joining Product, Inventory, Accounts and Transactions
- Built Semantic Layer to ensure one definition of Sales metrics across all Dashboards
- End-to-end pipeline is reproducible from this repo

## Dashboard

Retail Sales Performance Dashboard shows:
- Total Sales, Total Orders
- Sales by Category / Region
- Top Products
- Monthly Sales Trend

## Author

Built as a Data Engineering portfolio project to demonstrate Medallion Architecture, Unity Catalog and PySpark ETL on Databricks.
