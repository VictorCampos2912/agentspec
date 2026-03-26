# GCP Serverless Data Engineering Knowledge Base

> **Purpose**: Google Cloud Platform serverless services for event-driven data pipelines
> **MCP Validated**: 2026-02-17

## Quick Navigation

### Concepts (< 150 lines each)

| File | Purpose |
|------|---------|
| [concepts/cloud-run.md](concepts/cloud-run.md) | Cloud Run containers, scaling, environment variables |
| [concepts/pubsub.md](concepts/pubsub.md) | Pub/Sub messaging, topics, subscriptions |
| [concepts/gcs.md](concepts/gcs.md) | Cloud Storage buckets, lifecycle, event triggers |
| [concepts/bigquery.md](concepts/bigquery.md) | BigQuery tables, data loading, querying |
| [concepts/iam.md](concepts/iam.md) | IAM roles, service accounts, policies |
| [concepts/secret-manager.md](concepts/secret-manager.md) | Secret Manager versioning, access patterns |

### Patterns (< 200 lines each)

| File | Purpose |
|------|---------|
| [patterns/event-driven-pipeline.md](patterns/event-driven-pipeline.md) | GCS -> Pub/Sub -> Cloud Run pipeline |
| [patterns/multi-bucket-pipeline.md](patterns/multi-bucket-pipeline.md) | Multi-stage bucket workflow |
| [patterns/pubsub-fanout.md](patterns/pubsub-fanout.md) | Fan-out message distribution pattern |
| [patterns/cloud-run-scaling.md](patterns/cloud-run-scaling.md) | Auto-scaling and concurrency tuning |
| [patterns/gcs-triggered-workflow.md](patterns/gcs-triggered-workflow.md) | GCS event notification pattern |

### Specs (Machine-Readable)

| File | Purpose |
|------|---------|
| [specs/gcp-services.yaml](specs/gcp-services.yaml) | Service configuration reference |

---

## Quick Reference

- [quick-reference.md](quick-reference.md) - Fast lookup tables for all GCP services

---

## Key Concepts

| Concept | Description |
|---------|-------------|
| **Cloud Run** | Fully managed serverless container platform with auto-scaling |
| **Pub/Sub** | Asynchronous messaging service for event-driven architectures |
| **GCS** | Object storage with event notifications for pipeline triggers |
| **BigQuery** | Serverless data warehouse for analytics at scale |
| **IAM** | Identity and access management with least-privilege service accounts |
| **Secret Manager** | Secure storage and versioned access for API keys and credentials |

---

## Learning Path

| Level | Files |
|-------|-------|
| **Beginner** | concepts/cloud-run.md, concepts/gcs.md, concepts/pubsub.md |
| **Intermediate** | patterns/event-driven-pipeline.md, patterns/gcs-triggered-workflow.md |
| **Advanced** | patterns/pubsub-fanout.md, patterns/multi-bucket-pipeline.md |

---

## Agent Usage

| Agent | Primary Files | Use Case |
|-------|---------------|----------|
| ai-data-engineer-gcp | All concepts + patterns | Building serverless data pipelines |
| ai-prompt-specialist-gcp | concepts/cloud-run.md, concepts/iam.md | Deploying LLM services on GCP |
