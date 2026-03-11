# Atliq Data Pipeline (AWS + Databricks + Delta Lake)

This project was developed as part of a Data Engineering Virtual Internship at Codebasics, where I worked on a simulated industry use case for Atliq. The objective was to design and implement a scalable, automated data pipeline capable of ingesting, validating, transforming, and delivering reliable datasets for business analytics.

The solution follows a modern cloud data architecture built on AWS and Databricks using the Medallion architecture (Bronze → Silver) to ensure data quality, traceability, and analytics readiness.

---

# Problem Statement

Atliq receives finance and sales datasets from multiple operational systems. These datasets often arrive with inconsistencies such as missing fields, incorrect schemas, null values, and varying formats.

Without an automated pipeline:
- Data quality issues can propagate into analytics
- Manual ingestion slows down reporting workflows
- Lack of metadata logging makes troubleshooting difficult
- Business teams cannot reliably track data freshness or lineage

To address this, a **serverless and scalable data engineering pipeline** was required to automatically validate incoming data, maintain ingestion logs, and transform raw datasets into analytics-ready tables.

---

# Architecture Overview

The pipeline combines AWS serverless services with Databricks to build a reliable and scalable data platform.

![Data Pipeline Architecture](https://github.com/AnshumanB08/Atliq-Data-Engineering-Pipeline/blob/main/Atliq%20architecture%20diagram.png)

---

# Project Workflow

## 1. Data Ingestion Layer

A structured **data lake** was created in Amazon S3 to organize datasets.
Raw datasets are uploaded to their respective folders before being processed.

---

## 2. Automated Validation Pipeline

An AWS Glue job written in **PySpark** processes incoming datasets and performs multiple validation checks:

- Schema validation
- Required column checks
- Null validation for critical fields
- Row count verification

Records failing validation are automatically stored in the **rejected folder**, while valid records are written to the **Bronze layer**.
The pipeline is **event-driven**, triggered automatically via **AWS Lambda whenever a new file is uploaded to S3**.

---

## 3. Metadata Logging and Observability

To improve monitoring and traceability, ingestion metadata is stored in **Amazon DynamoDB**.

Each ingestion run records:

- Glue Job Run ID
- Source folder
- File name
- Total records processed
- Rejected record count
- Processing timestamp

The metadata is also exported to **Parquet files in Amazon S3**, registered in the **AWS Glue Data Catalog**, and made queryable using **Amazon Athena** for SQL-based monitoring of pipeline performance.

---

## 4. Data Transformation (Bronze → Silver)

Validated datasets from the Bronze layer are processed in **Databricks using Apache Spark**.

Key transformations include:

- Standardizing date formats to `yyyy-MM-dd`
- Handling missing region values
- Currency normalization (USD → INR)
- Joining finance and sales datasets
- Creating derived metrics such as **profit margin**
- Classifying transactions as **domestic or international**

The transformed data is stored as a **Delta Lake table in the Silver layer**, enabling:

- Schema enforcement
- Versioning
- Change Data Feed
- Reliable incremental processing

---

# Silver Layer Delta Table in Databricks

Databricks Catalog view of the Delta Lake Silver table **finance_sales_delta**, showing curated finance and sales records with standardized fields and derived metrics ready for analytics.

![Delta Lake Silver table](https://github.com/AnshumanB08/Atliq-Data-Engineering-Pipeline/blob/main/Sliver%20layer%20delta%20table.png)

---

# Technologies Used

| Category | Tools |
|--------|------|
| Cloud Platform | AWS |
| Storage | Amazon S3 |
| Processing | AWS Glue (PySpark) |
| Event Trigger | AWS Lambda |
| Metadata Logging | Amazon DynamoDB |
| Query Engine | Amazon Athena |
| Data Catalog | AWS Glue Data Catalog |
| Data Processing | Databricks (Apache Spark) |
| Storage Format | Parquet, Delta Lake |

---

# Key Learnings

This project provided practical experience in building production-style data pipelines and solving common data engineering challenges.

- **Handling inconsistent schemas:**
  Incoming files occasionally contained missing columns or structural inconsistencies. Schema validation logic ensured invalid records were isolated rather than breaking the pipeline.

- **Ensuring ingestion traceability:**
  Tracking ingestion runs across distributed systems can be difficult. Implementing metadata logging using DynamoDB combined with Parquet-based analytical logs enabled both operational monitoring and SQL-based auditing.

- **Data standardization during transformation:**
  Datasets contained inconsistent date formats and currency representations. These were standardized within Spark transformations to ensure reliable downstream analytics.

---

# Impact

The implemented pipeline improves the overall reliability of the analytics ecosystem by:

- Enforcing **data quality validation at ingestion**
- Maintaining **complete ingestion lineage**
- Delivering **clean analytics-ready datasets**
- Enabling **SQL-based monitoring of pipeline health**

Business teams can now confidently use the curated datasets for reporting and analytics.

---

# Pipeline Metrics

The pipeline captures operational metrics for each ingestion run:

- Total records processed
- Number of rejected records
- Validation failures
- Processing timestamps
- Glue Job Run IDs
- File-level lineage tracking

These metrics provide visibility into **data freshness, pipeline reliability, and ingestion performance**.

---

# Future Enhancements

Possible improvements to further scale the platform include:

- CI/CD pipelines for automated deployment
- Advanced monitoring with Amazon CloudWatch
- Automated data quality alerts
- Centralized data governance and cataloging
- Implementation of a **Gold layer** for BI dashboards
- Automated schema evolution handling

---

# Conclusion

This project simulates real-world data engineering responsibilities by implementing an **end-to-end cloud-native pipeline** that automates ingestion, enforces data quality, maintains metadata logging, and delivers analytics-ready datasets.

It demonstrates practical experience with **AWS serverless data engineering, Databricks-based transformations, and Delta Lake architecture**, closely mirroring production data platforms used in modern organizations.
