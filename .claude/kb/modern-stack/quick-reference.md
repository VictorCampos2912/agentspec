# Modern Stack Quick Reference

> Fast lookup tables. For code examples, see linked files.

## "Do I Need Spark?" Decision Tree

| Question | If Yes | If No |
|----------|--------|-------|
| Data > 500GB per job? | Consider Spark | DuckDB or Polars |
| Need distributed compute? | Spark or Flink | DuckDB (single node) |
| Real-time streaming? | Spark Streaming or Flink | DuckDB (batch) |
| Team knows PySpark? | Spark | Polars (similar API, faster) |
| CI/CD data testing? | DuckDB (instant startup) | — |
| Analytics on laptop? | DuckDB or Polars | — |

## DuckDB 1.5 Feature Highlights

| Feature | Description |
|---------|-------------|
| VARIANT type | Schema-flexible column (JSON-like) |
| GEOMETRY type | Native spatial data support |
| Iceberg support | Read + write via extension |
| httpfs extension | Query S3/GCS/Azure directly |
| MCP server | AI agent integration |
| AES-256 encryption | Encrypted database files |
| MERGE INTO | Upsert support (since 1.4) |

## Polars vs Pandas

| Feature | Polars | Pandas |
|---------|--------|--------|
| Speed (10GB groupby) | ~2s | ~30s |
| Memory efficiency | Columnar (Arrow) | Row-oriented |
| Lazy evaluation | Yes (query optimizer) | No |
| Multi-threaded | Yes (automatic) | No (GIL) |
| Null handling | Proper null type | NaN (float-based) |
| API style | Expression-based | Method chaining |
| Cloud support | Polars Cloud (2025) | None |

## SQLMesh vs dbt

| Feature | SQLMesh | dbt Core | dbt Cloud |
|---------|---------|----------|-----------|
| Virtual environments | Yes (no data copy) | No | Cloud IDE only |
| Change detection | Content hash | Timestamp | Timestamp |
| Automatic backfills | Yes (affected only) | No (manual) | No |
| Built-in scheduler | Yes | No (needs Airflow) | Yes |
| Column-level lineage | Yes | Fusion only | Fusion only |
| dbt compatibility | Yes (adapter) | Native | Native |
| Price | Free (OSS) | Free (OSS) | Paid |

## Common Pitfalls

| Don't | Do |
|-------|-----|
| Default to Spark for everything | Start with DuckDB, scale up if needed |
| Ignore Polars for pandas workloads | Benchmark — often 10-15x faster |
| Skip SQLMesh evaluation | Virtual envs alone may justify switching |
| Build BI dashboards from scratch | Use Evidence.dev (Markdown + SQL) |
