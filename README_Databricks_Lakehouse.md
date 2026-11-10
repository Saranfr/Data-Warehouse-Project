# End-to-End Data Warehouse & Lakehouse

A learning project implementing a data warehouse / lakehouse pipeline with **Databricks, PySpark, Spark SQL and Delta Lake**, using CRM and ERP source datasets.

> **Learning-project attribution:** This implementation follows the Databricks Bootcamp 2026 by [Data With Baraa](https://www.youtube.com/playlist?list=PLNcg_FV9n7qZoxVkw-KPhcmgLWjHWVUc9). The project structure, workflow and transformation approach are based on that tutorial. This repository is shared for learning and portfolio purposes, with credit to the original creator.

## Project Overview

The project combines data from CRM and ERP CSV source files and processes them through a three-layer **Medallion Architecture**:

- **Bronze:** Ingests the raw CRM and ERP CSV files into Delta tables.
- **Silver:** Cleans and standardizes the data using PySpark, including trimming text, normalizing values, handling invalid or missing values, converting dates and renaming columns.
- **Gold:** Combines the transformed data into analytics-ready customer and product dimensions and a sales fact table using Spark SQL.

## Architecture

```text
CRM source CSVs                  ERP source CSVs
      |                                |
      +---------------+----------------+
                      |
                      v
              BRONZE LAYER
        Raw data ingested as Delta tables
                      |
                      v
              SILVER LAYER
       PySpark cleaning and standardization
       - CRM customer, product and sales
       - ERP customer, location and category
                      |
                      v
               GOLD LAYER
          Dimensional data model
       - dim_customers
       - dim_products
       - fact_sales
                      |
                      v
           Analytics-ready tables
```

## Technology Stack

- Databricks
- Apache Spark
- PySpark
- Spark SQL
- Delta Lake
- Unity Catalog concepts (catalogs, schemas and volumes)

## Source Datasets

The project uses the CRM and ERP datasets supplied with the tutorial:

**CRM**
- `cust_info.csv`
- `prd_info.csv`
- `sales_details.csv`

**ERP**
- `CUST_AZ12.csv`
- `LOC_A101.csv`
- `PX_CAT_G1V2.csv`

The repository also contains analyst-oriented HR and sales datasets from the tutorial. The engineering pipeline described above uses the CRM and ERP source files.

## Repository Structure

```text
.
├── datasets/
│   └── engineering/
│       ├── source_crm/
│       └── source_erp/
├── script/
│   ├── init_lakehouse.ipynb
│   ├── bronze/
│   │   ├── bronze_layer(basic).ipynb
│   │   └── bronze_layer_(improved).ipynb
│   ├── silver/
│   │   ├── crm/
│   │   ├── erp/
│   │   └── silver_orchestration.ipynb
│   └── gold/
│       ├── gold_dim_customers.ipynb
│       ├── gold_dim_products.ipynb
│       ├── gold_fact_sales.ipynb
│       └── gold_orchestration.ipynb
└── README.md
```

## Notebook Workflow

1. **Initialize the lakehouse** – select the `workspace` catalog and create the `bronze`, `silver` and `gold` schemas, along with a volume for raw source files.
2. **Load the Bronze layer** – read the six CRM and ERP CSV files and save them as tables. The repository includes both a basic ingestion notebook and a configuration-driven improved notebook.
3. **Transform the Silver layer** – use PySpark to clean CRM and ERP data and write the transformed results as Delta tables.
4. **Build the Gold layer** – use Spark SQL joins and transformations to create customer and product dimensions and the sales fact table.
5. **Orchestrate notebooks** – run the Silver notebooks and Gold notebooks in sequence using `dbutils.notebook.run`.

## Key Transformations

Examples of transformations implemented in the notebooks include:

- Trimming whitespace from string columns.
- Standardizing customer marital status, gender, country and product-line values.
- Removing records with missing customer IDs.
- Parsing product and customer identifiers.
- Handling missing product costs and correcting invalid sales prices where possible.
- Converting date fields and handling invalid date values.
- Renaming source columns into descriptive column names.
- Joining CRM and ERP data to construct customer and product dimensions.
- Linking sales records to dimension keys to create the sales fact table.

## Running the Project

This project is designed to run in a Databricks workspace. The notebooks use catalog, schema and volume paths such as `workspace.bronze.raw_sources`, so update the catalog and file paths if your workspace uses different names.

1. Import or upload the notebooks and datasets into your Databricks workspace.
2. Create the catalog, schemas and volume by running `script/init_lakehouse.ipynb` (or adapt it to your workspace permissions and setup).
3. Upload the CRM and ERP CSV files into the corresponding folders under the raw-source volume:
   - `source_crm/`
   - `source_erp/`
4. Run the improved Bronze ingestion notebook.
5. Run `silver_orchestration.ipynb`.
6. Run `gold_orchestration.ipynb`.
7. Query the resulting tables in the `workspace.gold` schema.

The exact setup may vary depending on your Databricks workspace, catalog configuration and permissions.

## Current Scope

This repository documents the implementation present in the notebooks. It demonstrates batch CSV ingestion, PySpark-based cleaning and standardization, Delta table writes, Spark SQL dimensional modelling and notebook orchestration.

It does **not** currently claim implementation of incremental loading, SCD Type 2, streaming ingestion or production monitoring.

## Acknowledgement

The project is based on the **Databricks Bootcamp 2026** by **Data With Baraa**. Please refer to the original [YouTube bootcamp playlist](https://www.youtube.com/playlist?list=PLNcg_FV9n7qZoxVkw-KPhcmgLWjHWVUc9) for the tutorial and explanations. The original project repository includes its own license; retain the supplied `LICENSE` file and review its terms when redistributing or modifying the source materials.
