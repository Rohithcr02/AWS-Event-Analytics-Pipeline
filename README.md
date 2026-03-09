# AWS-Event-Analytics-Pipeline
A production-grade data engineering project that transforms streaming e-commerce events into a query-ready analytical dataset using AWS serverless technologies.

# Problem Statement
The core objective of this capstone project was to design and build a production-grade data analytics pipeline capable of reliably ingesting and processing a continuous stream of high-volume e-commerce event data. Every five minutes, the system generates between 500,000 and 750,000 user interaction events including page views, searches, add-to-cart actions, and purchases,mirroring the scale and complexity of real-world product analytics systems. 
The challenge was not only to capture this data efficiently but also to engineer the end-to-end AWS infrastructure required to transform raw, semi-structured JSON logs into clean, structured, query-ready datasets. The pipeline needed to support incremental processing, optimized storage formats, and fast analytical queries that could answer key business questions

Technical Requirements
The project had three core requirements:
1. Extend the provided CloudFormation template with pipeline infrastructure (Glue jobs, databases, output buckets)
2. Handle incremental data - New events arrive every 5 minutes; the pipeline must process only new data without reprocessing everything
3. Support 5 specific analytical queries - The dataset must enable fast, cost-effective answers to key business questions

# Architecture
After looking the requirements, I decided to use Medallion Architecture,  formulated by Databricks.
·       Bronze: Raw Lambda events in JSONL.gz
·       Silver: Cleaned, typed, timestamp-derived, partitioned Parquet
·       Gold: Pre-aggregated KPI table (I built for one use case: hourly revenue)
This allowed me to keep ingestion, cleaning, partitioning, and aggregation in separate, traceable layers. It also made debugging plus business validation so much easier. Additionally, This separation gave me confidence throughout the build, because I always knew which layer to inspect when something went wrong.

# Designing the pipeline
## Bronze Layer
The pipeline starts with an EventBridge rule that triggers a Lambda function every five minutes. Lambda generates around half a million synthetic e-commerce events and stores them in an S3 bucket using Hive-style partitioning. I understood that this layer is intentionally messy, similar to real life scenarios.
I extended the starter CloudFormation template step by step instead of adding all my resources at once.Each AWS service were added incrementally so I could understand exactly how it fit into the pipeline and verify that it deployed correctly before moving on.  This approach also mirrors real-world data engineering workflows, where infrastructure is expanded gradually to reduce blast radius and ensure the system remains stable.
## Silver Layer
From there, an AWS Glue ETL job reads only new files, cleans the schema, parses timestamps, derives year/month/day/hour columns, and writes Parquet into a separate S3 bucket, the Silver layer. This Silver dataset is what Athena queries directly.
Finally, I used Athena CTAS to generate a Gold table that aggregates hourly revenue. This table can be used as a data mart table for downstream consumption, including but not limited to reporting, visualization, source of another data process.
## Gold Layer
Although the project only required Silver tables to answer queries, I wanted to demonstrate a proper Gold layer because that’s what companies expect from a polished data model. I created a Gold table for hourly revenue using Athena CTAS and stored the results in another S3 prefix.

# Dataset Statistics
94.4M events processed across 152 files
1.87 GB Parquet output (Snappy compressed)
12.7 hours of event data
Event distribution: 50% views, 20% cart adds, 10% purchases, 10% removals, 10% searches

# Key Engineering Decisions
Parquet and Snappy over JSON + GZIP: Traded 14% larger files for 5-10x faster queries
DynamicFrame Sandwich: Leverages Glue bookmarks while using full PySpark capabilities
Partition Discovery: MSCK REPAIR TABLE synchronizes Glue catalog with S3 partitions

# Results
First Run (validation):
15 files, 8.5M events, 254 seconds
Second Run (14 hours later):

137 NEW files, 86M events, 1,710 seconds
Original 15 files not reprocessed (bookmark success)
Cost: $0.42 to process 94M events

This table reduces the cost of heavy analytical queries,Supports dashboard updates far more efficiently and makes the architecture feel complete.
Including a Gold layer strengthened the story and aligned with what data engineering teams actually do.
