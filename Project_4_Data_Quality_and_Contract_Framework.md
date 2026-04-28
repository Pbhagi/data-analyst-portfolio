# Data Quality & Contract Framework

> A drop-in framework for adding schema validation, expectations, freshness checks, drift detection, and lineage to any pipeline — with CI integration and alerting.

![Quality](https://img.shields.io/badge/Quality-Great%20Expectations%20%7C%20dbt%20tests-brightgreen)
![Lineage](https://img.shields.io/badge/Lineage-OpenLineage-blue)
![CI](https://img.shields.io/badge/CI-GitHub%20Actions-black)

---

## Why This Project

Every team I've worked with has had the same conversation eventually: "the dashboard is wrong." The fix is rarely one bug — it's the absence of *contracts*. This project bundles the lightweight building blocks that catch most issues before they reach a stakeholder.

## What It Does

- Validates **schemas** at ingestion and at every transformation boundary
- Runs **expectation suites** (Great Expectations + dbt tests) per dataset
- Monitors **freshness** and **row-count drift** with configurable thresholds
- Emits **lineage events** (OpenLineage) so impact analysis is one query away
- Integrates with **CI** so a failing expectation blocks the merge, not the dashboard
- Routes alerts to **Slack / email / PagerDuty** with severity tiers

## Components

```
   Raw datasets
        │
        ▼
   Schema check  ─▶  Expectations  ─▶  Freshness  ─▶  Drift detection
                                          │
                                          ▼
                              Alerts (Slack / Email)
                                          │
                                          ▼
                       OpenLineage event store + dashboard
```

## Highlights

- **Config-driven** — add a YAML file, get a tested dataset
- **Severity tiers** — block, warn, log; not every check should page you at 2am
- **CI gate** — pull requests run a slim expectation set on changed models
- **Lineage-aware alerts** — when a check fails, the alert links to downstream impact
- **Pluggable** — works with dbt, Spark, Airflow, plain SQL

## Tech Stack

`Great Expectations` · `dbt tests` · `Soda Core` · `OpenLineage` · `Marquez` · `GitHub Actions` · `Python` · `Slack API`

## Quick Start

```bash
pip install -r requirements.txt

# describe your dataset contract
cp examples/contracts/orders.example.yml contracts/orders.yml

# run all checks locally
python -m dq run --contract contracts/orders.yml

# wire into CI
cp .github/workflows/dq.example.yml .github/workflows/dq.yml
```

## Sample Contract

```yaml
dataset: orders.silver.orders
owner: data-platform
sla:
  freshness: 1h
  row_count_min: 10000
schema:
  - name: order_id
    type: string
    not_null: true
    unique: true
  - name: order_total
    type: decimal(12,2)
    min: 0
expectations:
  - expect_column_values_to_match_regex:
      column: email
      regex: "^[^@]+@[^@]+\\.[^@]+$"
```

## What I Learned

- Loud-and-fast > quiet-and-perfect: a noisy alert you can tune beats a silent failure
- Contracts are *social*, not technical — owners and SLAs matter more than tools
- Drift is the canary; treat row-count + null-rate changes as first-class signals

## Roadmap

- [ ] Native Iceberg + Delta sources
- [ ] Auto-suggest expectations from profiling
- [ ] Streaming-friendly checks (windowed freshness, late-arrival rates)

---

<sub>Part of my open data-platform portfolio.</sub>
