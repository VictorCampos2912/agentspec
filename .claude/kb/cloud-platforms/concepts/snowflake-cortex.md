# Snowflake Cortex & AI Platform

> **Purpose:** Snowflake AI capabilities -- Cortex AI functions, Snowpark, dynamic tables, Snowpipe, and Iceberg Tables
> **MCP Validated:** 2026-03-26

## Overview

Snowflake has evolved from a cloud data warehouse into an AI data platform. Cortex AI brings LLM-powered functions directly into SQL. Snowpark enables Python/Java/Scala DataFrames that execute natively on Snowflake compute. Dynamic tables replace complex task/stream pipelines with declarative transformations. Snowpipe and Snowpipe Streaming handle continuous ingestion. Iceberg Tables provide open-format interoperability.

## Key Concepts

### Cortex AI Functions

Built-in SQL functions that call hosted LLMs without infrastructure management:

| Function | Purpose | Example Use Case |
|----------|---------|------------------|
| `COMPLETE` | Text generation/completion | Summarization, Q&A, translation |
| `EXTRACT` | Structured data from text | Pull entities from unstructured docs |
| `CLASSIFY` | Text classification | Sentiment buckets, topic tagging |
| `EMBED` | Vector embeddings | Semantic search, RAG pipelines |
| `SENTIMENT` | Sentiment scoring | Customer feedback analysis |

Cortex runs models like Mistral, Llama, and Arctic. No data leaves Snowflake's security perimeter.

### Snowpark

Server-side execution framework for Python, Java, and Scala:

- **DataFrames** push computation to Snowflake warehouse (no data egress)
- **UDFs/UDTFs** deploy custom Python functions as SQL-callable objects
- **Stored Procedures** run complex logic with full Snowpark session access
- **ML** via Snowpark ML for preprocessing, training, and model registry

### Dynamic Tables

Declarative pipeline layer replacing imperative task/stream chains:

- Define the transformation SQL; Snowflake manages refresh automatically
- `TARGET_LAG` controls freshness (seconds to hours)
- Dependency graph built automatically across dynamic table chains
- Replaces: staging task -> stream -> transform task -> target table

### Snowpipe & Snowpipe Streaming

| Feature | Snowpipe | Snowpipe Streaming |
|---------|----------|--------------------|
| Trigger | Event-driven (S3/GCS/Azure notification) | Client SDK push (Java/Python) |
| Latency | 1-2 minutes | Sub-second |
| Format | Files (Parquet, CSV, JSON) | Row-level inserts |
| Cost | Per-file serverless compute | Per-row serverless compute |

### Iceberg Tables

Snowflake-managed tables using Apache Iceberg format:

- Data stored in Parquet on customer's cloud storage
- Queryable from Spark, Trino, Flink without Snowflake compute
- Supports time travel, schema evolution, partition evolution
- Catalog interop via Snowflake Open Catalog (Polaris)

## When to Use

- **Cortex AI** -- You need LLM inference on warehouse data without MLOps overhead
- **Snowpark** -- Python/ML workloads that must stay within Snowflake governance
- **Dynamic Tables** -- Multi-step ELT pipelines where declarative refresh simplifies ops
- **Snowpipe Streaming** -- Real-time ingestion from applications or IoT devices
- **Iceberg Tables** -- Multi-engine analytics or avoiding vendor lock-in

## Trade-offs

### Cortex AI vs BQML vs Mosaic AI

| Dimension | Cortex AI | BQML | Mosaic AI |
|-----------|-----------|------|-----------|
| Interface | SQL functions | SQL CREATE MODEL | Python SDK + UI |
| Model hosting | Managed (Arctic, Mistral, Llama) | Managed (Gemini, PaLM) | Managed + custom fine-tune |
| Custom models | Limited (Cortex Fine-Tuning) | Import TensorFlow/ONNX | Full MLflow lifecycle |
| Vector search | Cortex Search | Vector Search index | Vector Search (Mosaic) |
| GPU access | Abstracted | Abstracted | Direct (GPU clusters) |
| Strength | Simplicity, zero-MLOps | SQL-native ML training | Full ML/AI lifecycle |

### Platform Considerations

- Cortex functions consume credits per token; cost scales with prompt size
- Dynamic tables lack fine-grained scheduling (no cron); rely on `TARGET_LAG`
- Snowpark Python UDFs add cold-start latency on first invocation
- Iceberg Tables have write-throughput limits vs native Snowflake tables

## See Also

- [Snowflake Patterns](../patterns/snowflake-patterns.md) -- SQL implementation patterns
- [Cross-Platform Patterns](cross-platform-patterns.md) -- SQL dialect differences
- [Cost Optimization](../patterns/cost-optimization.md) -- Warehouse sizing and auto-suspend
