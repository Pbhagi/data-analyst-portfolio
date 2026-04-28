# Infrastructure-as-Code Data Platform

> A reproducible, auditable cloud data platform defined entirely in code: networking, storage, warehouse, orchestration, observability, and CI/CD. Spin it up in one environment, promote to the next with the same commit.

![IaC](https://img.shields.io/badge/IaC-Terraform-purple)
![Containers](https://img.shields.io/badge/Containers-Kubernetes%20%7C%20Helm-blue)
![CI/CD](https://img.shields.io/badge/CI%2FCD-GitHub%20Actions-black)

---

## Why This Project

A data platform isn't done when the pipeline runs once — it's done when *anyone* can recreate it cleanly in dev, staging, and prod from the same commit. This project is a small but honest example of that discipline.

## What It Does

- Provisions cloud foundations (network, IAM, KMS, buckets) with **Terraform**
- Deploys a managed warehouse (BigQuery / Snowflake / Redshift) and orchestration (Airflow / Composer)
- Stands up **Kubernetes (GKE / EKS)** for custom data jobs and self-hosted services
- Packages services with **Helm** charts
- Wires **CI/CD** for both infra and pipelines through **GitHub Actions**
- Adds **observability**: metrics, logs, alerts, and lineage out of the box

## Architecture

```
   GitHub  ──▶  Actions  ──▶  Terraform  ──▶   Cloud foundations
                                                   │
                                                   ▼
                                       Networking · IAM · KMS · Buckets
                                                   │
                                                   ▼
                                  Warehouse · Orchestrator · Kubernetes
                                                   │
                                                   ▼
                                    Helm-deployed services & jobs
                                                   │
                                                   ▼
                              Logs · Metrics · Alerts · Lineage · Cost
```

## Highlights

- **Modular Terraform** — environments differ only by tfvars, not by hand-edits
- **Least-privilege IAM** baked into modules (no wildcard roles)
- **Encryption everywhere** — KMS-managed keys for buckets, warehouses, secrets
- **Network hardening** — private endpoints, no public buckets, audit logs on
- **CI gates** — `terraform plan`, `tflint`, `checkov`, and policy-as-code on every PR
- **Cost visibility** — labels / tags applied via Terraform, surfaced in a dashboard

## Tech Stack

`Terraform` · `Helm` · `Kubernetes (GKE / EKS / AKS)` · `GitHub Actions` · `Airflow / Composer` · `BigQuery / Snowflake` · `Prometheus / CloudWatch` · `OpenLineage`

## Project Layout

```
.
├── terraform/
│   ├── modules/
│   │   ├── network/
│   │   ├── iam/
│   │   ├── warehouse/
│   │   ├── kubernetes/
│   │   └── observability/
│   └── envs/
│       ├── dev.tfvars
│       ├── staging.tfvars
│       └── prod.tfvars
├── helm/                  # service charts
├── .github/workflows/
└── docs/architecture.md
```

## Quick Start

```bash
cd terraform
terraform init
terraform workspace new dev
terraform apply -var-file=envs/dev.tfvars

# deploy services
helm upgrade --install airflow charts/airflow -n data --create-namespace
```

## What I Learned

- Drift is the silent killer; periodic `terraform plan` in CI is non-negotiable
- Modules should be opinionated and small — a hundred-input module is a lie
- Security defaults (private, encrypted, least-privileged) save weeks of audit cleanup later

## Roadmap

- [ ] Add OPA / Conftest policies for cost and security guardrails
- [ ] Add disaster-recovery runbooks + automated backup checks
- [ ] Multi-region warehouse failover example

---

<sub>Part of my open data-platform portfolio.</sub>
