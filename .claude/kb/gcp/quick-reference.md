# GCP Serverless Quick Reference

> Fast lookup tables. For code examples, see linked files.
> **MCP Validated**: 2026-02-17

## Core Services

| Service | Python Package | Primary Use |
|---------|---------------|-------------|
| Cloud Run | `google-cloud-run` | Container hosting, HTTP/event handlers |
| Pub/Sub | `google-cloud-pubsub` | Async messaging, event routing |
| GCS | `google-cloud-storage` | Object storage, pipeline triggers |
| BigQuery | `google-cloud-bigquery` | Data warehouse, SQL analytics |
| IAM | `google-cloud-iam` | Access control, service accounts |
| Secret Manager | `google-cloud-secret-manager` | Credentials, API key storage |

## Common gcloud Commands

| Action | Command |
|--------|---------|
| Deploy Cloud Run | `gcloud run deploy SERVICE --image IMAGE --region REGION` |
| Create topic | `gcloud pubsub topics create TOPIC` |
| Create subscription | `gcloud pubsub subscriptions create SUB --topic TOPIC` |
| Create bucket | `gcloud storage buckets create gs://BUCKET` |
| Set GCS notification | `gcloud storage buckets notifications create gs://BUCKET --topic=TOPIC` |
| Query BigQuery | `bq query --use_legacy_sql=false 'SELECT ...'` |
| Access secret | `gcloud secrets versions access latest --secret=NAME` |

## Decision Matrix

| Use Case | Choose |
|----------|--------|
| HTTP API endpoint | Cloud Run (service) |
| Async event processing | Cloud Run + Pub/Sub trigger |
| File arrives -> process | GCS notification -> Pub/Sub -> Cloud Run |
| Scheduled batch job | Cloud Scheduler -> Pub/Sub -> Cloud Run |
| Stream analytics | Pub/Sub -> Dataflow -> BigQuery |
| Store pipeline results | BigQuery (structured) or GCS (files) |

## Environment Variables (Cloud Run)

| Variable | Purpose |
|----------|---------|
| `PORT` | Cloud Run injects, default 8080 |
| `K_SERVICE` | Service name (auto-set) |
| `K_REVISION` | Revision name (auto-set) |
| `K_CONFIGURATION` | Configuration name (auto-set) |
| `GOOGLE_CLOUD_PROJECT` | Set manually for project ID |

## Common Pitfalls

| Don't | Do |
|-------|-----|
| Use `latest` secret alias in prod | Pin to specific version numbers |
| Grant `roles/editor` to service accounts | Use least-privilege predefined roles |
| Hardcode credentials in containers | Use Workload Identity or Secret Manager |
| Use synchronous pulls for high throughput | Use StreamingPull or push subscriptions |
| Ignore message deduplication | Implement idempotent handlers |
| Use `SELECT *` in BigQuery | Query only needed columns |

## Related Documentation

| Topic | Path |
|-------|------|
| Getting Started | `concepts/cloud-run.md` |
| Full Index | `index.md` |
| Pipeline Patterns | `patterns/event-driven-pipeline.md` |
