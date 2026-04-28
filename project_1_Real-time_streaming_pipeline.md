# Real-Time Streaming Pipeline

> A reference implementation of an end-to-end streaming ingestion and transformation pipeline with exactly-once semantics, schema evolution, and sub-5-second latency from source to lakehouse.

![Streaming](https://img.shields.io/badge/Streaming-Kafka%20%7C%20Spark%20Structured-orange)
![Storage](https://img.shields.io/badge/Storage-Delta%20Lake-blue)
![IaC](https://img.shields.io/badge/IaC-Terraform-purple)
![CI](https://img.shields.io/badge/CI-GitHub%20Actions-black)

---

## Why This Project

Most "streaming" demos stop at "events landed in a topic." The hard parts are everything after: replayable consumers, evolving schemas without breaking downstream, late and out-of-order events, idempotent writes, observability, and not setting your cloud bill on fire. This project is a small but honest implementation of those pieces.

## What It Does

- Ingests JSON / Avro events from a producer simulator into **Apache Kafka**
- Validates and parses with a **Schema Registry** (Avro / JSON Schema)
- Transforms with **Spark Structured Streaming** — windowed aggregations, deduplication, watermarking
- Writes to a **Delta Lake** (or Iceberg) sink on object storage with idempotent merges
- Emits lineage events via **OpenLineage** and metrics to **Prometheus / CloudWatch**
- Replays from a Kafka offset checkpoint for safe backfills

## Architecture

```
 Producers ──▶ Kafka (multi-partition) ──▶ Schema Registry
                       │
                       ▼
               Spark Structured Streaming
              (parsing • watermark • dedupe)
                       │
                       ▼
            Delta / Iceberg (bronze ▶ silver)
                       │
                       ▼
        Downstream consumers • BI • Feature store
```

## Highlights

- **Exactly-once** writes via Spark checkpointing + Delta merge keys
- **Schema evolution** handled through Schema Registry compatibility rules
- **Late events** absorbed with event-time watermarks (configurable)
- **Backfills** without duplicating data using replay-from-offset + idempotent merge
- **Observability** — structured logs, lineage events, lag/latency dashboards
- **CI/CD** — pytest unit tests, integration test against an embedded Kafka, deploy via GitHub Actions

## Tech Stack

`Apache Kafka` · `Spark Structured Streaming (PySpark)` · `Delta Lake` · `Schema Registry` · `OpenLineage` · `Prometheus` · `Terraform` · `Docker Compose` · `GitHub Actions`

## Quick Start

```bash
# spin up Kafka + Schema Registry + MinIO locally
docker compose up -d

# run the producer simulator
python -m producer.simulate --rate 1000

# launch the streaming job
spark-submit --packages io.delta:delta-core_2.12:2.4.0 jobs/stream_to_delta.py
```

Then open the Grafana dashboard at `http://localhost:3000` to watch lag, throughput, and dedupe rates.

## What I Learned

- Idempotency is a property of *the whole pipeline*, not just the writer
- Schema compatibility rules are a contract — treat them like API versions
- The cost of streaming is mostly in the long tail (compaction, small files, state)

## Roadmap

- [ ] Add Apache Flink job as an alternative consumer
- [ ] Demonstrate side-output for late / dead-letter events
- [ ] Add a vector embedding sink (pgvector) for downstream similarity search
- [ ] Compare Delta vs. Iceberg vs. Hudi on the same workload

---

<sub>Built as part of my open data-platform portfolio. Feedback and PRs welcome.</sub>
