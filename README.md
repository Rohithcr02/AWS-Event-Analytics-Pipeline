# AWS-Event-Analytics-Pipeline
A production-grade data engineering project that transforms streaming e-commerce events into a query-ready analytical dataset using AWS serverless technologies.

#Problem Statement
The core objective of this capstone project was to design and build a production-grade data analytics pipeline capable of reliably ingesting and processing a continuous stream of high-volume e-commerce event data. Every five minutes, the system generates between 500,000 and 750,000 user interaction events including page views, searches, add-to-cart actions, and purchases,mirroring the scale and complexity of real-world product analytics systems. 
The challenge was not only to capture this data efficiently but also to engineer the end-to-end AWS infrastructure required to transform raw, semi-structured JSON logs into clean, structured, query-ready datasets. The pipeline needed to support incremental processing, optimized storage formats, and fast analytical queries that could answer key business questions

Technical Requirements
The project had three core requirements:
1. Extend the provided CloudFormation template with pipeline infrastructure (Glue jobs, databases, output buckets)
2. Handle incremental data - New events arrive every 5 minutes; the pipeline must process only new data without reprocessing everything
3. Support 5 specific analytical queries - The dataset must enable fast, cost-effective answers to key business questions
