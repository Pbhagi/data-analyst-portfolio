# Healthcare Dashboard Project (CVS)

## Overview

Built a scalable, HIPAA-compliant data platform on GCP to process healthcare data across claims, pharmacy, and clinical systems.

## Key Contributions
Designed and implemented batch + streaming ETL pipelines using Apache Beam (Cloud Dataflow) to ingest multi-format healthcare data (HL7, JSON, Avro, Parquet) from 8+ source systems
Built a centralized data lake on GCS, optimized with partitioning & clustering to support efficient analytics on 3TB+ datasets
Developed real-time streaming pipelines (Pub/Sub + Dataflow + Kafka) achieving sub-5s latency for claims and pharmacy data processing
Migrated legacy ETL workflows (SSIS, Oracle) to cloud-native GCP architecture, reducing operational overhead and improving reliability
Modeled analytical datasets in BigQuery & Snowflake for downstream BI and compliance reporting
Orchestrated workflows using Airflow (Cloud Composer) managing 20+ production pipelines with SLA monitoring
Implemented data quality frameworks using Great Expectations and Dataplex
Enforced HIPAA-compliant security (IAM, KMS, column-level security) across sensitive healthcare data
Delivered curated datasets powering dashboards in Looker & Tableau

## Tech Stack
GCP (Dataflow, Pub/Sub, BigQuery, GCS) | Python | PySpark | Kafka | Airflow | Terraform
