# Azure Car Sales Data Engineering Platform

An end-to-end cloud data engineering project that ingests car sales data, processes it through a Medallion architecture, and publishes analytics-ready fact and dimension tables for reporting.

> **Project status:** In progress — built incrementally as a learning and portfolio project.

## Overview

This project simulates a car sales business with data about branches, dealers, car models, sales dates, units sold, and revenue.

The goal is to build a maintainable data pipeline that can:

- Ingest source data into Azure SQL Database.
- Incrementally copy new data into Azure Data Lake Storage Gen2 using Azure Data Factory.
- Transform and organize data with Azure Databricks.
- Apply the Bronze, Silver, and Gold Medallion architecture.
- Model analytics data as a star schema with fact and dimension tables.
- Handle dimension updates using Slowly Changing Dimension (SCD) Type 1.
- Apply data governance and access controls with Unity Catalog.
- Make curated Gold data available for reporting in Power BI.

## Architecture

```text
Sample Sales Data (CSV / GitHub)
              |
              v
     Azure SQL Database
              |
              v
     Azure Data Factory
  (incremental ingestion / watermark)
              |
              v
 Azure Data Lake Storage Gen2
              |
       Bronze layer
              |
              v
       Azure Databricks
   (cleaning / transformations)
              |
       Silver layer
              |
              v
       Star-schema modeling
   (facts / dimensions / SCD Type 1)
              |
        Gold layer (Delta)
              |
              v
            Power BI
       (reports / dashboards)

Governance and access control: Unity Catalog
```

The diagram is the intended target architecture. Components will be implemented and verified as the project progresses.

## Technology Stack

| Technology | Purpose |
|---|---|
| Azure SQL Database | Relational source database for sales data |
| Azure Data Factory (ADF) | Orchestrates ingestion and incremental copy pipelines |
| Azure Data Lake Storage Gen2 | Stores data across Bronze, Silver, and Gold layers |
| Azure Databricks | Performs data transformations and dimensional modeling |
| Apache Parquet | Columnar file format for efficient analytical storage |
| Delta Lake | Reliable lakehouse tables, transactions, and table history |
| Unity Catalog | Data discovery, permissions, governance, and lineage |
| Power BI | Reporting and dashboards on curated data |
| Git and GitHub | Source control, documentation, and project history |

## Data Model

The source dataset contains fields related to:

- Branches
- Dealers
- Car models
- Dates
- Units sold
- Revenue

The Gold layer will organize these into a star schema. The expected model includes a sales fact table and relevant dimensions such as branch, dealer, model, and date. Exact table definitions and column names will be documented alongside the implementation.

### Fact table

The sales fact table will contain measurable business events, such as units sold and revenue, plus keys that reference the dimensions.

### Dimension tables

Dimension tables will hold descriptive attributes for branches, dealers, car models, and dates. Surrogate keys will be generated where appropriate and used to link facts to dimensions.

## Incremental Ingestion

The initial load brings the existing source data into the pipeline. Subsequent runs should process only eligible new or changed records instead of blindly reloading the full source.

The planned Azure Data Factory flow uses:

1. A watermark table to store the last successful processing boundary.
2. Lookup activities to retrieve the previous boundary and determine the current upper boundary.
3. A parameterized copy activity to extract the eligible source range.
4. A stored procedure to update the watermark after a successful copy.

**Important:** A watermark-based pipeline is not automatically the same as database-native Change Data Capture (CDC). The chosen source change indicator must reliably capture the changes the pipeline is expected to process. Handling updates to old records, late-arriving data, retries, and duplicate delivery will be considered and tested.

## Medallion Layers

| Layer | Purpose | Expected contents |
|---|---|---|
| Bronze | Preserve ingested source data | Raw or minimally processed data |
| Silver | Clean and standardize | Validated, consistently typed data |
| Gold | Serve analytics | Delta tables organized for business reporting |

## Slowly Changing Dimensions

The project will implement **SCD Type 1** for applicable dimensions. When a tracked attribute changes, the existing dimension record is updated rather than preserving a historical version of the previous value.

The implementation will also address how dimension surrogate keys are resolved when loading fact records.

## Governance and Reliability

Planned engineering practices include:

- Unity Catalog permissions and governed storage access.
- Managed identities where supported and appropriate.
- External locations for governed access to cloud storage.
- Data validation and quality checks.
- Pipeline execution monitoring and error investigation.
- Parameterized configuration rather than unnecessary hardcoding.
- Validation of record counts and incremental-load boundaries.

These features will be documented as they are implemented; their presence in this README does not imply that every control is already complete.

## Repository Structure

The repository will grow as the project is implemented. A proposed starting structure is:

```text
azure-car-sales-data-engineering/
├── README.md
├── docs/
│   ├── architecture.md
│   └── data-model.md
├── data/
│   └── README.md
├── sql/
│   ├── source_schema.sql
│   └── watermark.sql
├── adf/
│   └── README.md
├── databricks/
│   ├── bronze_to_silver/
│   └── silver_to_gold/
├── tests/
│   └── README.md
└── .gitignore
```

The structure is a starting proposal. Files and folders will be added when they contain real, tested project artifacts.

## Implementation Roadmap

- [ ] Create the GitHub repository and establish the project structure.
- [ ] Document the architecture and source dataset.
- [ ] Provision Azure resources with cost controls.
- [ ] Load the sample sales data into Azure SQL Database.
- [ ] Build and validate the initial ADF ingestion pipeline.
- [ ] Implement watermark-based incremental ingestion.
- [ ] Store ingested data in the Bronze layer.
- [ ] Configure Databricks, Unity Catalog, and governed storage access.
- [ ] Transform and validate data in the Silver layer.
- [ ] Build Gold fact and dimension tables with surrogate keys.
- [ ] Implement and test SCD Type 1 updates.
- [ ] Add data quality checks, monitoring, and recovery scenarios.
- [ ] Connect Power BI to the Gold layer and create a sample report.
- [ ] Document test results, design decisions, and screenshots.

## Getting Started

This project is being developed in stages. The exact setup instructions will be added as the Azure resources and pipeline are implemented.

### Prerequisites

- A GitHub account and Git installed locally.
- A Microsoft Azure subscription with access to the required services.
- An Azure Databricks workspace with the capabilities needed for the chosen Unity Catalog setup.
- Familiarity with SQL and basic data engineering concepts.

### Local repository setup

After creating the GitHub repository, clone it and enter the project directory:

```bash
git clone https://github.com/<YOUR_GITHUB_USERNAME>/azure-car-sales-data-engineering.git
cd azure-car-sales-data-engineering
```

Replace `<YOUR_GITHUB_USERNAME>` with your GitHub username.

## Cost and Security Notes

Azure services can incur charges, and free credits or trial availability may vary. Before deploying resources:

- Set a budget and cost alerts in Azure.
- Use the smallest suitable resource configurations.
- Stop or delete billable resources when they are not needed.
- Never commit passwords, access tokens, connection strings, service-principal secrets, or other credentials.
- Use environment-specific configuration and secure secret storage for sensitive values.

## Learning Outcomes

By completing the project, the intended outcomes are practical experience with:

- Cloud data ingestion and orchestration.
- Incremental loading and watermark management.
- Lakehouse storage with Parquet and Delta Lake.
- Data transformation with Databricks.
- Dimensional modeling and star schemas.
- Surrogate keys and SCD Type 1.
- Data governance and access control.
- Data quality, pipeline monitoring, and troubleshooting.
- Delivering curated data for BI reporting.

## Project Notes

This is an educational portfolio project. Architecture, configuration, limitations, and validation evidence will be updated as implementation progresses. A successful tutorial run alone should not be treated as proof of production readiness.

## License

A license has not been selected yet. Add a `LICENSE` file before presenting the repository as open source.
