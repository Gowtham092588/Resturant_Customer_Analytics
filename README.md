# Restaurant Customer Analytics Project

## 📖 Overview

This project implements an end-to-end AWS data pipeline for restaurant customer and sales analytics.
The pipeline extracts transactional data from SQL Server/Amazon RDS, processes it using AWS Glue and PySpark, stores it in Amazon S3 with Delta Lake, and follows a Bronze → Silver → Gold Medallion Architecture.
The Gold layer provides analytics for customer lifetime value, RFM segmentation, churn indicators, loyalty behavior, sales trends, discounts, and restaurant performance using Amazon Athena and Streamlit.

---

## 🎯 Business Objective

* Build a centralized view of customer and sales activity.
* Identify high-value customers using CLV.
* Analyze customer behavior using RFM.
* Identify customers showing signs of churn.
* Compare loyalty and non-loyalty customers.
* Analyze sales, discounts, and restaurant performance.

---

## 🚀 Project Highlights

* Batch-based Medallion Architecture.
* Incremental ingestion from SQL Server using `updated_at` watermark logic.
* AWS Glue and PySpark for ingestion and transformation.
* Amazon S3 with Delta Lake for scalable storage and reliable updates.
* Data-quality validation, duplicate handling, and S3 quarantine.
* SCD Type 1 and SCD Type 2 dimensional processing.
* Star-schema Gold data model.
* AWS Glue Workflows for orchestration.
* EventBridge and SNS for job-failure notifications.
* Athena and Streamlit for analytics and reporting.
* GitHub Actions for CI/CD.

---

## 🏛️ Architecture

Add the project architecture diagram here.

---

## 🏗️ Bronze → Silver → Gold

### 🟫 Bronze Layer

* Extracts `order_items`, `order_item_options`, and `date_dim` from SQL Server/Amazon RDS.
* Uses `updated_at` watermark logic for incremental ingestion.
* Preserves source data and ingestion metadata.
* Uses Delta Lake MERGE for reliable reruns.
* Sends invalid records to an S3 quarantine area.

### ⬜ Silver Layer

* Cleans and standardizes source data.
* Handles nulls, duplicates, data types, and data-quality validation.
* Produces trusted datasets for downstream processing.
* Uses Delta Lake for schema control and incremental updates.

### 🟨 Gold Layer

* Builds analytics-ready fact and dimension tables.
* Implements SCD Type 2 for customer history.
* Supports CLV, RFM, churn, loyalty, revenue, discount, and restaurant performance analysis.

---

## 🧩 Gold Data Model

### Dimensions

* `DIM_DATE`
* `DIM_ITEM`
* `DIM_APP`
* `DIM_RESTAURANT`
* `DIM_CUSTOMER`

### Facts

* `FACT_ORDER_ITEMS`
* `FACT_ORDER_ITEM_OPTIONS`
* `FACT_CUSTOMER_DAILY`

`DIM_CUSTOMER` uses SCD Type 2 to preserve historical customer attribute changes.

---

## 📊 Key Analytics

* Customer Lifetime Value
* RFM Segmentation
* Churn Indicators
* Revenue and Sales Trends
* Loyalty vs Non-Loyalty Analysis
* Restaurant Performance
* Average Order Value
* Discount Effectiveness

---

## ⚙️ Orchestration & Monitoring

* AWS Glue Workflows manage Bronze → Silver → Gold dependencies.
* EventBridge monitors Glue job failures.
* SNS sends failure notifications.
* Failed data-quality records are stored in S3 quarantine.

---

## 🚀 CI/CD

GitHub Actions is used to:

* Validate Python and JSON files.
* Deploy Glue scripts and configuration files to Amazon S3.
* Update Glue job deployments.
* Authenticate securely with AWS using IAM/OIDC.

---

## 🛠️ Technologies Used

* SQL Server / Amazon RDS
* AWS Glue & PySpark
* Amazon S3 & Delta Lake
* AWS Glue Workflows & Data Catalog
* Amazon Athena
* EventBridge & SNS
* Streamlit & Plotly
* Python & Pandas
* Git, GitHub & GitHub Actions

---

## 🚀 Key Engineering Challenges Solved

* Implemented incremental ingestion without native CDC.
* Resolved duplicate and data-quality issues.
* Built quarantine handling for invalid records.
* Implemented SCD Type 2 customer history.
* Maintained surrogate keys during incremental updates.
* Prevented duplicate processing during reruns.
* Built modular orchestration and failure monitoring.

---

## 💻 Author

**Gowtham Kethineni**

[LinkedIn](https://www.linkedin.com/in/gowtham-kethineni)
