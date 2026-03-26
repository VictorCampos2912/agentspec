# Cloud Platforms Quick Reference

> Fast lookup tables. For code examples, see linked files.

## Platform Comparison (2026)

| Feature | Snowflake | Databricks | BigQuery |
|---------|-----------|-----------|----------|
| Architecture | Shared storage, compute isolation | Lakehouse (Delta + Spark) | Serverless, slot-based |
| Storage format | Proprietary (Iceberg support) | Delta Lake (open) | Capacitor (proprietary) |
| Open table formats | Iceberg (read), Polaris catalog | Delta + Iceberg (Unity) | BigLake (external) |
| SQL dialect | ANSI + extensions | Spark SQL + ANSI | GoogleSQL |
| Python support | Snowpark | PySpark / notebooks | BigFrames |
| AI/ML built-in | Cortex AI | Mosaic AI, MLflow | BQML, AI.GENERATE |
| Streaming | Snowpipe Streaming | Structured Streaming | BigQuery sub |
| Serverless | Yes (always) | Yes (serverless compute) | Yes (always) |
| Cost model | Credit-based (per-second) | DBU-based | Slot-based or on-demand |

## AI Features Comparison

| Capability | Snowflake Cortex | Databricks | BigQuery |
|-----------|-----------------|-----------|----------|
| Text-to-SQL | Cortex Analyst | Natural language queries | Duet AI |
| Embeddings | EMBED() function | Mosaic AI | AI.GENERATE with Gemini |
| Classification | CLASSIFY() | Custom models | AI.GENERATE |
| Summarization | COMPLETE() | Foundation models | AI.GENERATE |
| ML Training | Snowpark ML | MLflow + AutoML | BQML CREATE MODEL |
| LLM serving | Cortex (Claude, Mistral) | Model Serving | Vertex AI |

## Pricing Models

| Platform | Compute | Storage | Key Cost Driver |
|----------|---------|---------|-----------------|
| Snowflake | Credits ($2-4/credit) | $23/TB/month | Warehouse size + runtime |
| Databricks | DBUs ($0.07-0.55/DBU) | Cloud storage cost | Cluster size + runtime |
| BigQuery | $6.25/TB scanned (on-demand) | $0.02/GB/month | Query volume or slots |

## Common Pitfalls

| Don't | Do |
|-------|-----|
| Leave warehouses running | Auto-suspend after 1-5 min |
| Scan full tables (BigQuery) | Partition + cluster, use LIMIT in dev |
| Over-provision Databricks clusters | Start small, use autoscaling |
| Ignore cross-region costs | Co-locate storage and compute |
| Vendor lock-in on proprietary features | Use Iceberg/Delta for portability |
