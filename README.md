# Welcome to Restaurant Customer Analytics Project!

## 📖 Overview

This project implements an end-to-end AWS data pipeline for analyzing restaurant customer behavior, sales performance, loyalty activity, discounts, and restaurant performance.

The solution extracts transactional data from SQL Server hosted on Amazon RDS, processes the data using AWS Glue and PySpark, stores it in Amazon S3 using Delta Lake, and follows a Bronze → Silver → Gold Medallion Architecture.

The Gold layer uses a dimensional data model with fact and dimension tables to support customer lifetime value, RFM segmentation, churn indicators, loyalty analysis, sales trends, discount analysis, and restaurant performance reporting through Amazon Athena and Streamlit.

---

## 🎯 Business Objective

* Build a centralized view of restaurant customer and sales activity.
* Identify high-value customers using Customer Lifetime Value.
* Analyze customer behavior using RFM segmentation.
* Identify customers showing signs of inactivity or churn risk.
* Compare loyalty and non-loyalty customer behavior.
* Analyze sales trends across restaurant locations and ordering platforms.
* Measure the impact of discounts and item options on revenue.
* Identify top-performing restaurant locations.

---

## 🚀 Project Highlights

* SQL Server / Amazon RDS source ingestion.
* Incremental data ingestion using `updated_at` watermark logic.
* AWS Glue and PySpark-based processing.
* Medallion Architecture — Bronze → Silver → Gold.
* Amazon S3 with Delta Lake storage.
* Delta Lake MERGE for incremental and idempotent processing.
* Data-quality validation and duplicate handling.
* S3 quarantine area for invalid records.
* SCD Type 1 and SCD Type 2 dimensional processing.
* Star-schema Gold data model.
* AWS Glue Workflow for orchestration.
* EventBridge and SNS for job-failure notifications.
* Amazon Athena for serverless analytics.
* Interactive Streamlit dashboard.
* GitHub Actions-based CI/CD for Glue scripts and configuration deployment.

---

## 🏛️ Architecture

This project implements a batch-oriented Medallion Architecture using AWS serverless services and Delta Lake storage.

Add your project architecture diagram here:

```html
<img width="YOUR_WIDTH" alt="Restaurant Customer Analytics Architecture"
src="YOUR_GITHUB_IMAGE_URL" />

## 🏗️ Bronze → Silver → Gold Architecture
## 🟫 Bronze Layer – Incremental Source Ingestion
- Extracts restaurant transactional data from SQL Server / Amazon RDS using AWS Glue.
- Processes source tables such as:
  - order_items
  - order_item_options
  - date_dim
- Uses the updated_at column as a watermark to identify new or modified records.
- Stores the last successful processing checkpoint for incremental loads.
- Preserves source data with minimal transformation for traceability.
- Uses Delta Lake MERGE logic to support reliable reruns and prevent duplicate ingestion.
- Sends invalid records to an Amazon S3 quarantine area for investigation.

## ⬜ Silver Layer – Cleansed & Trusted Data
- Cleans and standardizes source data using AWS Glue and PySpark.
- Standardizes timestamps, numeric values, nulls, text values, and data types.
- Applies table-specific data-quality validation rules.
- Handles duplicate records based on business keys and relevant attributes.
- Separates invalid records from trusted datasets.
- Maintains clean and current source-level datasets for downstream Gold processing.
- Uses Delta Lake for reliable updates, schema control, and incremental processing.

## 🟨 Gold Layer – Business & Analytics Data
The Gold layer converts trusted Silver data into an analytics-ready dimensional model.
Dimensions
- DIM_DATE
- DIM_ITEM
- DIM_APP
- DIM_RESTAURANT
- DIM_CUSTOMER
DIM_CUSTOMER uses SCD Type 2 to preserve historical changes in customer attributes such as loyalty status.
Fact Tables
- FACT_ORDER_ITEMS
- FACT_ORDER_ITEM_OPTIONS
- FACT_CUSTOMER_DAILY
The Gold layer supports business metrics such as:
- Customer Lifetime Value — CLV
- RFM segmentation
- Customer churn / inactivity indicators
- Daily customer activity
- Sales and revenue trends
- Loyalty vs non-loyalty analysis
- Restaurant performance
- Average Order Value
- Discount effectiveness
- Item and category performance

## 🔄 Incremental Processing
Since native SQL Server CDC was not available in the selected SQL Server setup, incremental extraction was implemented using an updated_at column.
For each source table:
1. The pipeline reads the previous successful watermark.
2. AWS Glue extracts records where updated_at is greater than the stored watermark.
3. Data-quality checks are applied.
4. Valid records are written to Delta Lake.
5. Invalid records are stored in the quarantine area.
6. The checkpoint is updated only after successful processing.
This reduces unnecessary full-table processing while supporting inserts and updates from the source system.

## 🧹 Data Quality Framework
The pipeline performs table-specific validation before data moves into trusted analytical layers.
Key checks include:
- Missing business-key validation.
- Null-value validation.
- Duplicate detection.
- Quantity validation.
- Price validation.
- Data-type validation.
- Referential validation.
- Schema validation.
- Invalid record quarantine.
Valid records continue through the pipeline while problematic records are retained separately for investigation instead of being silently discarded.

## 🧩 Dimensional Modeling & SCD
A star-schema dimensional model is used in the Gold layer to simplify reporting and analytical queries.
Surrogate keys are generated for major dimensions such as:
- Customer
- Item
- Restaurant
- Ordering application
DIM_CUSTOMER uses SCD Type 2 to maintain customer history.
Historical fields include:
- Effective Start Date
- Effective End Date
- Current Record Flag
This allows historical transactions to be associated with the customer attributes that were valid at the time of the transaction.

## 📊 Customer Analytics
The pipeline supports customer-focused metrics including:
Customer Lifetime Value
Measures total historical revenue generated by each customer and groups customers into value segments.
RFM Analysis
Customers are evaluated using:
- Recency — days since the last order.
- Frequency — number of orders within a defined period.
- Monetary — customer spending within the defined period.
Churn Indicators
The pipeline calculates:
- Days since last order.
- Average number of days between orders.
- Recent spending changes.
- Customer inactivity status.
These metrics help identify customers who may require retention or marketing attention.

## 💰 Sales & Revenue Analytics
The Gold layer calculates transaction-level measures including:
- Item Amount
- Option Amount
- Gross Amount
- Discount Amount
- Revenue
- Order Count
- Average Order Value
Negative option values can be analyzed separately as discounts, allowing the business to compare discounted and non-discounted transactions.

## ⚙️ Orchestration
AWS Glue Workflows manage dependencies across the Medallion pipeline.
Typical execution flow:
Amazon RDS
    ↓
Bronze Glue Jobs
    ↓
Silver Glue Jobs
    ↓
Gold Glue Jobs
    ↓
Glue Data Catalog
    ↓
Amazon Athena
    ↓
Streamlit Dashboard

Conditional job dependencies ensure that downstream processing begins only after required upstream jobs complete successfully.

## 🔔 Monitoring & Error Handling
The pipeline includes operational monitoring and failure notification.
- AWS Glue logs job execution details.
- Amazon EventBridge captures Glue job-state changes.
- Amazon SNS sends failure notifications.
- Invalid business records are written to an S3 quarantine area.
- Modular Glue jobs allow failed processing stages to be rerun independently.

## 🔐 Security
The architecture follows AWS security practices including:
- IAM roles for AWS Glue service access.
- Controlled access to Amazon S3 buckets.
- Secure connectivity between AWS Glue and Amazon RDS.
- IAM-based permissions for Athena and Glue Data Catalog.
- GitHub Actions authentication to AWS using IAM/OIDC instead of long-lived AWS credentials.

## 🚀 CI/CD
GitHub and GitHub Actions are used to manage and deploy pipeline code.
The CI/CD workflow includes:
- Python code validation.
- JSON configuration validation.
- Version control for Glue scripts and configurations.
- Automated deployment of Glue scripts to Amazon S3.
- Updating AWS Glue job deployments from version-controlled source.
- Secure AWS authentication using GitHub OIDC.
This reduces manual deployment effort and provides consistent code changes across the project.

## 📈 Analytics Layer
Amazon Athena
Amazon Athena provides serverless SQL querying of curated Gold datasets without maintaining dedicated database infrastructure.
Streamlit & Plotly
The Streamlit dashboard provides interactive analysis for:
- Revenue and order KPIs.
- Customer Lifetime Value.
- RFM customer segmentation.
- Churn indicators.
- Loyalty analysis.
- Sales trends.
- Restaurant performance.
- Discount effectiveness.

## 🛠️ Technologies Used
- SQL Server / Amazon RDS
- AWS Glue
- PySpark
- Amazon S3
- Delta Lake
- AWS Glue Workflows
- AWS Glue Crawlers
- AWS Glue Data Catalog
- Amazon Athena
- Amazon EventBridge
- Amazon SNS
- Streamlit
- Plotly
- Python
- Pandas
- Git
- GitHub
- GitHub Actions

## 🚀 Key Engineering Challenges Solved
- Implemented incremental ingestion without native CDC.
- Handled duplicate transactional records.
- Built table-specific data-quality validations.
- Managed valid and invalid null values correctly.
- Created quarantine processing for failed records.
- Implemented idempotent Delta Lake MERGE processing.
- Preserved customer history using SCD Type 2.
- Generated and maintained surrogate keys.
- Built analytics-ready fact and dimension tables.
- Prevented duplicate Gold records during pipeline reruns.
- Implemented modular Glue Workflow orchestration.
- Added monitoring, failure notification, and CI/CD automation.


💻 Author
Gowtham Kethineni
