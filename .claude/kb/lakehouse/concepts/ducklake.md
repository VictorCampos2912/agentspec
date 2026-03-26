# DuckLake

> **Purpose**: DuckDB-based lakehouse with SQL catalog — sub-TB, single-node, local-first use cases
> **Confidence**: 0.85
> **MCP Validated**: 2026-03-26

## Overview

DuckLake is a lightweight lakehouse format where DuckDB acts as both the catalog and query engine. It stores metadata in a DuckDB database and data in Parquet on object storage. Best for sub-TB datasets, development environments, and local-first analytics where Iceberg/Delta's distributed complexity is overkill.

## The Concept

```sql
-- Install and load the DuckLake extension
INSTALL ducklake;
LOAD ducklake;

-- Attach a DuckLake catalog (metadata in DuckDB, data in S3)
ATTACH 'ducklake:s3://my-bucket/ducklake-catalog.db' AS lakehouse;

-- Create a table (data written as Parquet to S3)
CREATE TABLE lakehouse.analytics.events (
    event_id    VARCHAR,
    user_id     VARCHAR,
    event_type  VARCHAR,
    event_ts    TIMESTAMP,
    payload     JSON
);

-- Insert data (written to Parquet files on S3)
INSERT INTO lakehouse.analytics.events
SELECT * FROM read_parquet('s3://raw-data/events/*.parquet');

-- Query with full SQL (DuckDB engine)
SELECT event_type, COUNT(*) AS cnt, DATE_TRUNC('hour', event_ts) AS hour
FROM lakehouse.analytics.events
WHERE event_ts >= '2026-03-01'
GROUP BY event_type, hour
ORDER BY cnt DESC;

-- Time travel (snapshot-based)
SELECT * FROM lakehouse.analytics.events AT (TIMESTAMP '2026-03-25');
```

## Quick Reference

| Feature | DuckLake | Iceberg | Delta |
|---------|---------|---------|-------|
| Best scale | < 500 GB | TB-PB | TB-PB |
| Catalog | DuckDB file | REST/HMS/Glue | Unity/_delta_log |
| Engine | DuckDB only | Multi-engine | Spark-native, some multi |
| Transactions | ACID | ACID | ACID |
| Time travel | Yes | Yes | Yes |
| Partition evolution | N/A (columnar scan) | Yes | No (liquid clustering instead) |
| Cost | Zero (open source) | Zero + infra | Zero + infra |
| Use case | Dev, CI/CD, local analytics | Production lakehouse | Production lakehouse |

## Common Mistakes

### Wrong

```text
Using DuckLake for a 10 TB production workload with 50 concurrent users.
DuckLake is single-node; use Iceberg/Delta for distributed scale.
```

### Correct

```text
Use DuckLake for:
- Development and CI/CD testing (fast, zero infra)
- Local-first analytics (< 500 GB)
- MotherDuck for cloud sharing of small-medium datasets
- Prototyping before Iceberg/Delta migration
```

## Related

- [iceberg-v3](iceberg-v3.md)
- [delta-lake](delta-lake.md)
- [duckdb concept](../../modern-stack/concepts/duckdb.md)
