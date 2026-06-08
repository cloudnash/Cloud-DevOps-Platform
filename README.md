# Cloud-DevOps-Platform


<div align="center">

# ☁️ Cloud DevOps Platform

### Full-Stack Cloud Infrastructure & Automation Platform

[![CI/CD Pipeline](https://github.com/cloudnash/cloud-devops-platform/actions/workflows/ci.yml/badge.svg)](https://github.com/cloudnash/cloud-devops-platform/actions)
[![Python](https://img.shields.io/badge/Python-3.11-blue?logo=python)](https://python.org)
[![Docker](https://img.shields.io/badge/Docker-24.x-blue?logo=docker)](https://docker.com)
[![Kubernetes](https://img.shields.io/badge/Kubernetes-1.28-blue?logo=kubernetes)](https://kubernetes.io)
[![Terraform](https://img.shields.io/badge/Terraform-1.6-purple?logo=terraform)](https://terraform.io)
[![License: MIT](https://img.shields.io/badge/License-MIT-green)](LICENSE)

**A production-grade cloud automation platform demonstrating serverless computing, containerised microservices, API management, caching, DevSecOps, and full CI/CD.**

</div>

---

## 🎯 What This Project Covers

| JD Requirement | Implementation |
|---|---|
| Python & Bash scripting | `scripts/` — 12 automation scripts |
| Containerisation & orchestration | Docker + Kubernetes (GKE-ready) with HPA, probes |
| Serverless & event-driven automation | AWS Lambda functions + S3 event triggers |
| Threat detection & compliance monitoring | OPA policies + Trivy image scanning |
| Serverless querying & data analytics | AWS Athena + S3 data lake queries |
| IaC & automated provisioning | Terraform (AWS) + Ansible playbooks |
| Relational, NoSQL, in-memory databases | PostgreSQL + DynamoDB + Redis |
| Scalable caching | Redis cluster with write-through caching layer |
| API build & management | FastAPI + rate limiting + JWT auth + OpenAPI docs |
| CI/CD pipelines | GitHub Actions — lint, test, scan, build, deploy |

---

## 🏗️ Architecture Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                        GitHub Actions CI/CD                      │
│   lint → unit-test → security-scan → build → push → deploy      │
└─────────────────┬───────────────────────────────────────────────┘
                  │
┌─────────────────▼───────────────────────────────────────────────┐
│                     FastAPI Application Layer                     │
│  /health  /api/v1/items  /api/v1/cache  /metrics  /docs          │
└──────┬─────────────┬────────────────────┬────────────────────────┘
       │             │                    │
┌──────▼──┐   ┌──────▼──────┐   ┌────────▼────────┐
│ PostgreSQL│  │  Redis Cache │   │  AWS Lambda     │
│ (primary) │  │  (write-thru)│   │  (event-driven) │
└──────────┘   └─────────────┘   └────────┬────────┘
                                           │
                                  ┌────────▼────────┐
                                  │   S3 + Athena   │
                                  │  (data lake)    │
                                  └─────────────────┘
```

---

## 📁 Repository Structure

```
cloud-devops-platform/
├── .github/
│   └── workflows/
│       ├── ci.yml                    # Main CI pipeline (lint, test, scan, build)
│       └── deploy.yml                # CD pipeline (deploy to AWS/GKE)
│
├── app/
│   ├── main.py                       # FastAPI entrypoint with OpenAPI docs
│   ├── api/
│   │   ├── routes.py                 # Route definitions (items, cache, health)
│   │   └── middleware.py             # Rate limiting, JWT auth, request logging
│   ├── services/
│   │   ├── cache_service.py          # Redis write-through caching layer
│   │   ├── db_service.py             # PostgreSQL + DynamoDB abstraction
│   │   └── event_service.py          # Event-driven SNS/SQS message dispatch
│   └── models/
│       └── schemas.py                # Pydantic models for API validation
│
├── infrastructure/
│   ├── terraform/
│   │   ├── main.tf                   # AWS provider, VPC, subnets, security groups
│   │   ├── lambda.tf                 # Lambda functions + IAM roles
│   │   ├── rds.tf                    # PostgreSQL RDS instance
│   │   ├── elasticache.tf            # Redis ElastiCache cluster
│   │   ├── s3_athena.tf              # S3 data lake + Athena workgroup
│   │   ├── dynamodb.tf               # DynamoDB tables
│   │   └── variables.tf              # Input variables
│   ├── ansible/
│   │   ├── playbooks/
│   │   │   ├── provision.yml         # Server provisioning (packages, users)
│   │   │   └── harden.yml            # CIS-based OS hardening playbook
│   │   └── inventory/
│   │       └── hosts.ini             # Dynamic inventory
│   └── kubernetes/
│       ├── base/
│       │   ├── deployment.yml        # App deployment with liveness/readiness probes
│       │   ├── service.yml           # ClusterIP + LoadBalancer services
│       │   ├── hpa.yml               # Horizontal Pod Autoscaler (CPU 70%)
│       │   ├── configmap.yml         # Non-secret configuration
│       │   └── secrets.yml           # External Secrets Operator manifest
│       ├── monitoring/
│       │   ├── prometheus-rules.yml  # Alert rules (5xx rate, P99 latency, OOM)
│       │   └── grafana-dashboard.json# Importable Grafana dashboard
│       └── security/
│           ├── network-policy.yml    # Deny-all + allow-listed network policies
│           └── pod-security.yml      # Pod Security Standards (restricted)
│
├── scripts/
│   ├── health/
│   │   ├── health_check.sh           # HTTP health check with retry logic
│   │   ├── system_snapshot.sh        # CPU/mem/disk + pod events snapshot
│   │   └── log_analyzer.py           # Error pattern scanner (timeout/OOM/auth)
│   ├── deploy/
│   │   ├── rolling_deploy.sh         # Zero-downtime Kubernetes rolling update
│   │   └── rollback.sh               # One-command rollback to previous image
│   └── security/
│       ├── opa_validate.sh           # OPA policy check on k8s manifests
│       └── compliance_scan.py        # Python script — CIS compliance checks
│
├── serverless/
│   ├── lambda/
│   │   ├── event_processor.py        # S3-triggered Lambda: process uploaded data
│   │   ├── scheduled_cleanup.py      # CloudWatch Events Lambda: data lifecycle
│   │   └── api_authorizer.py         # Lambda authorizer for API Gateway JWT
│   └── athena/
│       ├── queries/
│       │   ├── daily_analytics.sql   # Serverless query on S3 data lake
│       │   └── error_summary.sql     # Aggregate error rate by endpoint
│       └── run_query.py              # Python runner — execute Athena, poll, export
│
├── cache/
│   ├── redis_client.py               # Redis connection pool + TTL helpers
│   ├── cache_warmup.py               # Pre-warm cache from DB on startup
│   └── cache_invalidation.py         # Pattern-based cache invalidation
│
├── monitoring/
│   ├── prometheus/
│   │   └── alerts.yml                # Production-grade alerting rules
│   └── grafana/
│       └── dashboard.json            # Pre-built dashboard (import to Grafana)
│
├── tests/
│   ├── test_api.py                   # Pytest — API endpoint integration tests
│   ├── test_cache.py                 # Pytest — Redis cache behaviour tests
│   ├── test_lambda.py                # Pytest — Lambda function unit tests
│   └── conftest.py                   # Fixtures (test DB, mock Redis, mock S3)
│
├── docs/
│   ├── ARCHITECTURE.md               # Detailed architecture & decision log
│   ├── RUNBOOK.md                    # Incident response runbook (L1/L2)
│   ├── DEPLOYMENT.md                 # Step-by-step deploy guide (local + AWS)
│   └── POST_INCIDENT_TEMPLATE.md     # Blameless PIR template
│
├── docker-compose.yml                # Local dev stack (app + postgres + redis + prometheus)
├── Dockerfile                        # Multi-stage build (builder → runtime, non-root)
├── requirements.txt                  # Python dependencies
├── .trivyignore                      # Trivy scan exceptions
└── .opa/
    └── policies/
        ├── k8s_security.rego         # OPA: no privileged containers, resource limits required
        └── image_policy.rego         # OPA: only approved registries allowed
```

---

## 🚀 Quick Start

### Prerequisites
- Docker & Docker Compose
- Python 3.11+
- `kubectl` (for Kubernetes)
- `terraform` CLI (for AWS provisioning)
- AWS CLI configured

### 1. Local development (Docker Compose)
```bash
git clone https://github.com/cloudnash/cloud-devops-platform.git
cd cloud-devops-platform

# Start full local stack: API + PostgreSQL + Redis + Prometheus + Grafana
docker-compose up -d

# Verify all services are healthy
./scripts/health/health_check.sh

# API docs available at:
open http://localhost:8000/docs
```

### 2. Run tests
```bash
# Install dependencies
pip install -r requirements.txt

# Run full test suite
pytest tests/ -v --cov=app --cov-report=term-missing
```

### 3. Kubernetes deployment (GKE / local kind)
```bash
# Apply all manifests
kubectl apply -f infrastructure/kubernetes/base/
kubectl apply -f infrastructure/kubernetes/monitoring/
kubectl apply -f infrastructure/kubernetes/security/

# Watch rollout
kubectl rollout status deployment/cloud-devops-platform

# Check HPA status
kubectl get hpa cloud-devops-platform-hpa
```

### 4. AWS infrastructure provisioning
```bash
cd infrastructure/terraform

# Initialise and plan
terraform init
terraform plan -var-file="terraform.tfvars"

# Apply (creates VPC, RDS, ElastiCache, Lambda, S3, Athena, DynamoDB)
terraform apply
```

### 5. OPA policy validation
```bash
# Validate all Kubernetes manifests against security policies
./scripts/security/opa_validate.sh infrastructure/kubernetes/

# Run CIS compliance checks on running cluster
python scripts/security/compliance_scan.py --output report.json
```

---

## 🔒 Security Features

| Feature | Implementation |
|---|---|
| Image scanning | Trivy in CI — blocks builds with CRITICAL CVEs |
| Policy-as-code | OPA Rego policies — no privileged pods, resource limits enforced |
| Network isolation | Kubernetes NetworkPolicy — deny-all with explicit allow-list |
| Secret management | External Secrets Operator (not hardcoded secrets) |
| JWT authentication | Lambda authorizer for API Gateway |
| OS hardening | Ansible CIS playbook on EC2 instances |
| Non-root containers | Dockerfile runs as `appuser` (UID 1000) |

---

## 📊 Observability Stack

The local `docker-compose.yml` spins up:
- **Prometheus** — scrapes `/metrics` endpoint (request rate, latency, error rate)
- **Grafana** — pre-loaded dashboard (import `monitoring/grafana/dashboard.json`)
- **node-exporter** — host CPU/memory/disk metrics

Key alerts in `monitoring/prometheus/alerts.yml`:
- Pod `CrashLoopBackOff`
- HTTP 5xx error rate > 1%
- P99 latency > 500ms
- Redis memory usage > 80%
- Disk space < 15%

---

## ⚡ Serverless Components

### Lambda functions
| Function | Trigger | Purpose |
|---|---|---|
| `event_processor.py` | S3 PutObject | Process CSV uploads, write to DynamoDB |
| `scheduled_cleanup.py` | CloudWatch Events (cron) | Archive old S3 objects, purge stale cache |
| `api_authorizer.py` | API Gateway | JWT validation, claims extraction |

### Athena analytics
```bash
# Run daily analytics query on S3 data lake
python serverless/athena/run_query.py \
  --query serverless/athena/queries/daily_analytics.sql \
  --output s3://my-bucket/results/
```

---

## 🗄️ Database Layer

| Database | Use case | Location |
|---|---|---|
| PostgreSQL (RDS) | Primary relational store | `infrastructure/terraform/rds.tf` |
| DynamoDB | Event logs, session data (NoSQL) | `infrastructure/terraform/dynamodb.tf` |
| Redis (ElastiCache) | API response caching (write-through) | `cache/redis_client.py` |

---

## 📋 CI/CD Pipeline

```yaml
# .github/workflows/ci.yml overview
on: [push, pull_request]

jobs:
  lint:       # flake8 (Python) + ShellCheck (Bash) + yamllint
  test:       # pytest with coverage, fail if < 80%
  scan:       # Trivy image scan — block CRITICAL CVEs
  opa:        # OPA policy check on all k8s manifests
  build:      # docker build + push to ECR/Docker Hub
  deploy:     # kubectl rolling update + post-deploy health check
```

---

## 📖 Documentation

| Doc | Contents |
|---|---|
| [ARCHITECTURE.md](docs/ARCHITECTURE.md) | System design, ADRs, data flows |
| [RUNBOOK.md](docs/RUNBOOK.md) | L1/L2 incident response procedures |
| [DEPLOYMENT.md](docs/DEPLOYMENT.md) | Step-by-step deployment guide |
| [POST_INCIDENT_TEMPLATE.md](docs/POST_INCIDENT_TEMPLATE.md) | Blameless PIR template |

---

## 🛠️ Skills Demonstrated

`Python` `Bash` `FastAPI` `Docker` `Kubernetes` `Helm` `Terraform` `Ansible`
`AWS Lambda` `AWS Athena` `S3` `RDS` `DynamoDB` `ElastiCache/Redis` `PostgreSQL`
`Prometheus` `Grafana` `GitHub Actions` `OPA` `Trivy` `JWT` `CI/CD` `DevSecOps`

---

<div align="center">

**[Nashit Ahmad](https://linkedin.com/in/nashitahmad/) · [GitHub](https://github.com/cloudnash)**

</div>
