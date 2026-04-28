# Cloud Lakehouse with Medallion Architecture

> A reference lakehouse on the cloud with bronze / silver / gold layers, dimensional modeling, dbt transformations, and partitioning + clustering strategies tuned for query cost and performance.

![Warehouse](https://img.shields.io/badge/Warehouse-BigQuery%20%7C%20Snowflake-blue)
![Transform](https://img.shields.io/badge/Transform-dbt-orange)
![Orchestrate](https://img.shields.io/badge/Orchestrate-Airflow-red)
![Format](https://img.shields.io/badge/Format-Iceberg-brightgreen)

---

## Why This Project

Lakehouses are easy to draw on a whiteboard and surprisingly hard to keep tidy. This repo shows a small but realistic implementation: raw data lands in **bronze**, gets cleaned and conformed in **silver**, and is modeled into business-grade facts and dimensions in **gold** — all with clear contracts, tests, and lineage.

## What It Does

- Ingests sample multi-source data (JSON, Avro, Parquet) into a cloud object store
- Promotes data through bronze ▶ silver ▶ gold layers with dbt models
- Implements **Kimball dimensional models** — conformed dimensions, slowly changing dimensions (SCD2), fact tables
- Applies partitioning and clustering on the warehouse for query efficiency
- Exposes a semantic layer (dbt + LookML / Cube) for self-serve analytics

## Architecture

```
   Source files (S3 / GCS) ─▶ Bronze (raw, append-only)
                                  │
                                  ▼
                       Silver (cleaned, conformed)
                                  │
                                  ▼
                  Gold (facts, dims, semantic layer)
                                  │
                                  ▼
                       BI (Looker / Tableau / Power BI)
```

## Highlights

- **Idempotent loads** with merge keys and watermark columns
- **SCD Type 2** dimensions implemented with dbt snapshots
- **Partition + cluster** keys chosen to match real query patterns (with cost benchmarks)
- **dbt tests** for primary keys, referential integrity, accepted values, custom assertions
- **Lineage** auto-generated and published via OpenLineage / dbt docs
- **Cost dashboards** showing query cost-per-model and cost-per-stakeholder

## Tech Stack

`BigQuery` *(or Snowflake)* · `dbt` · `Apache Airflow / Cloud Composer` · `Apache Iceberg` · `Looker / Cube / Metabase` · `Terraform`

## Project Layout

```
.
├── ingest/                # bronze ingestion jobs (Python / Beam)
├── dbt/
│   ├── models/
│   │   ├── bronze/
│   │   ├── silver/
│   │   └── gold/
│   ├── tests/
│   └── snapshots/         # SCD2 sources
├── airflow/dags/
├── infra/                 # Terraform for warehouse + buckets + IAM
└── docs/architecture.md
```

## Quick Start

```bash
# provision warehouse + bucket
cd infra && terraform init && terraform apply

# load sample raw files
python ingest/load_samples.py

# build the lakehouse
cd dbt && dbt build
```

Browse `dbt docs serve` for full lineage, model docs, and column-level descriptions.

## What I Learned

- Most "data quality" issues are really *contract* issues — and dbt tests + schema docs catch 80% of them early
- A good partition key is worth more than three more nodes of compute
- The Gold layer is a product — name things in business language, not engineering language

## Roadmap

- [ ] Add Iceberg-format silver layer with table maintenance jobs
- [ ] Wire up Soda + Monte Carlo for freshness + anomaly checks
- [ ] Publish a small self-serve data portal (datasets + owners + SLAs)

---

<sub>Part of my open data-platform portfolio.</sub>
