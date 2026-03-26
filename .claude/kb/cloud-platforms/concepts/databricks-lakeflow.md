# Databricks LakeFlow & AI Platform

> **Purpose:** Databricks managed ingestion, declarative pipelines, Unity Catalog governance, and Mosaic AI serving
> **Confidence:** 0.90
> **MCP Validated:** 2026-03-26

## Overview

Databricks has consolidated its data engineering surface into LakeFlow (managed ingestion + orchestration), Declarative Pipelines (the evolution of Delta Live Tables), and Designer (visual ETL). Unity Catalog provides three-level namespace governance across all assets. Mosaic AI handles model serving, fine-tuning, and AI gateway routing.

## The Concept

### LakeFlow Connect

Managed connectors that ingest from SaaS sources (Salesforce, SAP, Workday, databases) directly into Delta tables. No custom code required -- configure source, destination, and schedule.

### Declarative Pipelines (formerly DLT)

Define transformations as expectations-annotated Python or SQL. The runtime manages orchestration, retries, and data quality enforcement automatically.

```python
# Declarative Pipeline -- Bronze to Silver with expectations
import dlt
from pyspark.sql.functions import col, current_timestamp

@dlt.table(
    name="orders_bronze",
    comment="Raw orders ingested from source"
)
def orders_bronze():
    return (
        spark.readStream
        .format("cloudFiles")
        .option("cloudFiles.format", "json")
        .load("/mnt/landing/orders/")
    )

@dlt.table(
    name="orders_silver",
    comment="Cleaned orders with quality enforcement"
)
@dlt.expect_or_drop("valid_amount", "order_amount > 0")
@dlt.expect_or_fail("valid_id", "order_id IS NOT NULL")
@dlt.expect("has_customer", "customer_id IS NOT NULL")
def orders_silver():
    return (
        dlt.read_stream("orders_bronze")
        .withColumn("ingested_at", current_timestamp())
        .where(col("status") != "TEST")
    )

@dlt.table(
    name="daily_revenue_gold",
    comment="Aggregated daily revenue"
)
def daily_revenue_gold():
    return (
        dlt.read("orders_silver")
        .groupBy("order_date")
        .agg({"order_amount": "sum", "order_id": "count"})
        .withColumnRenamed("sum(order_amount)", "total_revenue")
        .withColumnRenamed("count(order_id)", "order_count")
    )
```

### Designer

Visual drag-and-drop ETL interface that generates Declarative Pipeline code. Targets citizen data engineers who need pipeline creation without writing Python/SQL.

### Unity Catalog

Three-level namespace: `catalog.schema.table`. Governs tables, volumes, models, functions, and connections. Provides column-level lineage, row/column security, and attribute-based access control.

### Mosaic AI

| Component | Purpose |
|-----------|---------|
| Model Serving | Real-time endpoints for custom and foundation models |
| AI Gateway | Unified proxy to OpenAI, Anthropic, Google with rate limiting |
| Vector Search | Managed vector index for RAG over Delta tables |
| Feature Serving | Online feature store for ML inference |
| Fine-Tuning | Supervised fine-tuning on foundation models |

## Quick Reference

| Feature | What It Replaces | Key Benefit |
|---------|-----------------|-------------|
| LakeFlow Connect | Fivetran/Airbyte | Native, governed ingestion |
| Declarative Pipelines | DLT (renamed) | Built-in expectations, auto-scaling |
| Designer | Manual pipeline code | Visual ETL for non-coders |
| Unity Catalog | Hive Metastore | Cross-workspace governance |
| Mosaic AI | External ML platforms | Unified model lifecycle |

## Common Mistakes

### Wrong
```python
# Using legacy Hive metastore paths
spark.sql("CREATE TABLE hive_metastore.default.orders ...")
```

### Correct
```python
# Using Unity Catalog three-level namespace
spark.sql("CREATE TABLE prod_catalog.sales.orders ...")
```

## Related

- [Databricks Patterns](../patterns/databricks-patterns.md) -- DLT, Jobs API, Unity Catalog setup
- [Cross-Platform Patterns](cross-platform-patterns.md) -- SQL dialect differences
- [Cost Optimization](../patterns/cost-optimization.md) -- Compute sizing and DBU management
