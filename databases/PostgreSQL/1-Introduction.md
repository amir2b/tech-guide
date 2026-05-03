# PostgreSQL

## What is a Database?

A database is a structured system for storing and managing transactional data (OLTP), designed to enable easy access, updating, and organization of information.

### Types of Databases

- Relational Databases (RDBMS)
- NoSQL - Document Databases
- NoSQL - Key-Value Databases
- NoSQL - Graph Databases
- NoSQL - Wide-Column Databases

### CAP Theorem

- Consistency (C): Every read returns the most recent write
- Availability (A): Every request receives a (non-error) response, even if it's not the most recent
- Partition Tolerance (P): The system continues to operate despite network failures or delays

### What is ACID?

- Atomicity (All or Nothing)
- Consistency (Rules Cannot Be Broken)
- Isolation (Mind Your Own Business)
- Durability (Permanent Record)

### OLTP vs OLAP Systems

- OnLine Transactional Processing (OLTP): MySQL, PostgreSQL, Microsoft SQL Server
- OnLine Analytical Processing (OLAP): ClickHouse, StarRocks, Apache Druid

### Categories of SQL Commands

- DDL (Data Definition Language): CREATE, ALTER, DROP, TRUNCATE, RENAME
- DML (Data Manipulation Language): SELECT, INSERT, UPDATE, DELETE
- DCL (Data Control Language): GRANT, REVOKE
- TCL (Transaction Control Language): COMMIT, ROLLBACK, SAVEPOINT, BEGIN TRANSACTION

## What is a Data Warehouse?

A data warehouse is a centralized system designed to store, integrate, and analyze large volumes of historical data from multiple sources for business intelligence and decision-making.

### Key Concepts of Data Modeling

- Fact Tables: Store measurable, quantitative data (e.g., sales, orders)
- Dimension Tables: Store descriptive attributes related to facts (e.g., time, location, product)
- Granularity: Level of detail stored in fact tables — affects query detail and performance
- Common Schema Designs:
  - Star Schema: Central fact table connected to dimension tables — simple and fast queries
  - Snowflake Schema: Extension of star schema with normalized dimension tables — saves storage, more complex
  - Flat Table (denormalized table): A single table containing all data attributes, often with repeated values.

### Data Mart

A data mart is a smaller, focused subset of a data warehouse that serves the specific needs of a business unit, function, or department. It acts as an access layer, containing a targeted portion of the warehouse data optimized for quick retrieval and analysis, often for client-facing or specialized reporting.

## What is a Data Lake?

A data lake is a centralized repository that stores vast amounts of raw data in its native format — structured, semi-structured, or unstructured — enabling flexible, large-scale analytics and data exploration.

### Data Lake Architecture

- Data Ingestion: Tools like Apache Kafka, AWS Kinesis, Flume
- Storage Layer: Amazon S3 (Minio), Azure Data Lake, HDFS
- Processing Layer: Apache Spark, Hive, Presto, Trino
- Catalog & Metadata: Hive Metastore, Apache Atlas, AWS Glue
- Access Layer: BI tools, notebooks, ML pipelines

### What is a Data Lakehouse?

A data lakehouse is a modern data architecture that combines the low-cost, flexible storage of a data lake with the structured, high-performance querying capabilities of a data warehouse.

Popular Technologies: Apache Iceberg, Delta Lake, Apache Hudi
