# Retail Data Engineering Project

A Databricks lakehouse project that brings together retail transaction, product, inventory, and CRM-style data for analytics. It demonstrates a Medallion architecture, data-quality expectations, a Gold sales fact table, a semantic metric view, and a sales-performance dashboard.

## Project Overview

The project is organized around a three-layer data flow:

```text
CSV / PostgreSQL-style sample data / Salesforce-style sample data
                              |
                              v
          Bronze: source-aligned tables in retail_q
                              |
                              v
     Silver: standardized tables and quality expectations
                              |
                              v
 Gold: sales fact, dimensions, inventory view, calendar
                              |
                              v
       Semantic metric view and Databricks dashboard
```

The Databricks catalog used in the project is `retail_q`. Its schemas separate source data, curated data, and analytics assets.

## Repository Structure

```text
.
├── 01_Source_data/
│   ├── 01_postgres_product_history.sql
│   ├── 02_postgres_inventory_history.sql
│   ├── 03_salesforce_accounts_history.csv
│   ├── 04_salesforce_opportunities_history.csv
│   ├── 05_blob_transactions_history.csv
│   └── 06_blob_transactions_incremental_100.csv
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
└── 04_Dashboard/
    └── Retail Sales Performance Dashboard.lvdash.json
```

## Data Layers

| Layer | Schema(s) | Purpose |
|---|---|---|
| Bronze | `blob_bronze`, `postgres_bronze`, `salesforce_bronze` | Source-aligned transaction, product, inventory, account, and opportunity data |
| Silver | `retail_silver` | Standardized account, inventory, opportunity, product, and transaction datasets with data-quality expectations |
| Gold | `retail_gold` | Sales fact, customer and product dimensions, inventory view, and calendar data |
| Semantic | `retail_semantic` | Metric view used as the dashboard's dataset |

### Implemented transformations

- The Blob transactions notebook uses Auto Loader (`cloudFiles`) to ingest CSV files into `retail_q.blob_bronze.transactions`.
- Silver pipeline modules clean and select fields from Bronze tables. Product/account records also derive active-record flags from CDC columns.
- The Gold sales pipeline joins transactions to opportunities by normalized opportunity name and creates `retail_q.retail_gold.fact_sales`.
- Gold SQL defines customer, product, and inventory views.
- A calendar notebook generates date attributes for analytics.
- The metric view defines transaction, revenue, quantity, discount, average transaction value, and unique-customer measures.
- The dashboard JSON contains KPI counters and charts for trends and breakdowns by category, brand, customer, location, channel, payment mode, and opportunity stage.

## Data Included

The repository contains sample source files, including:

- 1,000 historical transaction rows and 100 incremental transaction rows.
- Account and opportunity CSV extracts.
- PostgreSQL-style product and inventory table scripts.

The SQL scripts are source-data examples; they are not Databricks catalog or schema deployment scripts. The repository does not include credentials or a complete ingestion setup for every source system.

## Requirements

- A Databricks workspace with Unity Catalog enabled.
- A catalog named `retail_q`, with the schemas used by the notebooks and pipelines.
- A Databricks compute environment that supports Auto Loader and Lakeflow Declarative Pipelines (`pyspark.pipelines`).
- A Unity Catalog Volume accessible to the Auto Loader notebook, or updated source, schema, and checkpoint paths.
- Source Bronze tables populated with columns expected by the Silver pipeline modules.
- Permissions to read source data and create or replace tables, views, and metric views in the target schemas.

## Running in Databricks

1. Review the catalog, schema, Volume, schema-tracking, and checkpoint paths in the notebooks. The Auto Loader example currently points to `/Volumes/retail_q/volumes/blob_source/...`.
2. Load or create the source data and Bronze tables. Configure ingestion and column mappings for each source; only the Blob Auto Loader example is included here.
3. Configure and run the Bronze-to-Silver pipeline using the modules in `03_ETL_Pipeline/bronze_to_silver/`.
4. Run the calendar and Gold SQL notebooks, and configure the Silver-to-Gold pipeline for `fact_sales`.
5. Create the semantic metric view and import or deploy the dashboard JSON in the workspace.
6. Validate table names, metric definitions, currency formatting, and dashboard results against the data before publishing.

Execution order and pipeline configuration can vary by workspace. This repository does not include workspace-specific deployment configuration.

## Validation Notes

Before using this project with another workspace or presenting dashboard figures as business results, check the following:

- The calendar notebook creates `retail_q.retail_gold.calendar`, while the metric view references `retail_q.retail_gold.dim_calendar`. Use a consistent object name or provide the corresponding dimension view.
- The Silver transaction module calculates `gross_amount`, but the Gold fact and metric view currently aggregate the opportunity `amount`. Confirm the intended definition of revenue and use transaction-level sales amounts if that is the business definition.
- Pipeline expectations record data-quality results, but most are `@dp.expect`, which do not drop failing rows. Choose the appropriate expectation behavior for invalid records.
- The opportunity stage expectation currently lists only three stages; compare it with the stages in the supplied CSV before relying on its quality metrics.
- The metric view formats currency as USD. Confirm the source currency and update the display format if needed.
- The SQL sample scripts include invalid-record examples. Review their constraints and data types before executing them in PostgreSQL.
- Account and opportunity CSV headers differ from the Salesforce-style column names selected by the Silver modules. Add or configure the required mapping if those CSVs are the actual Bronze inputs.

## Skills Demonstrated

- Databricks lakehouse and Medallion architecture
- Apache Spark / PySpark transformations
- Auto Loader and streaming ingestion patterns
- Lakeflow Declarative Pipelines and data-quality expectations
- SQL views, dimensional modeling, and semantic metrics
- Dashboard-oriented analytics and source-data profiling

## Security

Do not commit workspace tokens, passwords, private connection details, or other credentials. Use Databricks secrets or an approved identity-based authentication method for real deployments.
