# Lakehouse Quick Reference

> Fast lookup tables. For code examples, see linked files.

## Table Format Comparison (2026)

| Feature | Iceberg v3 | Delta Lake 4.1 | Hudi | DuckLake |
|---------|-----------|----------------|------|----------|
| Format war status | **Winner** | Strong #2 | Niche | Emerging |
| Schema evolution | Full + partition | Full | Full | Limited |
| Partition evolution | Yes (no rewrite) | No (liquid clustering instead) | Limited | N/A |
| Time travel | Snapshot-based | Version-based | Timeline | Version-based |
| Row-level deletes | Position + equality | Deletion vectors | Log-based | Standard SQL |
| Merge-on-read | Yes | Yes (deletion vectors) | Yes | N/A |
| UniForm interop | Native | Reads as Iceberg/Hudi | N/A | N/A |
| Engine support | Spark, Flink, Trino, DuckDB | Spark, Flink, DuckDB | Spark, Flink | DuckDB only |

## Catalog Comparison

| Feature | Unity Catalog | Apache Gravitino | Nessie | Polaris |
|---------|--------------|-----------------|--------|---------|
| Owner | Databricks | Apache (TLP) | Dremio/Community | Snowflake |
| Multi-format | Delta + Iceberg | Any format | Iceberg | Iceberg |
| Multi-engine | Spark, DuckDB | Any engine | Spark, Flink | Any (REST) |
| Git-like branching | No | No | Yes | No |
| Open source | Yes (limited) | Yes (full) | Yes | Yes |
| ABAC/RBAC | ABAC + tags | Basic | Basic | RBAC |
| Production readiness | High | Medium (v1.2) | Medium | Medium |

## When to Use What

| Scenario | Recommended |
|----------|-------------|
| Greenfield lakehouse | Iceberg v3 + Gravitino |
| Databricks ecosystem | Delta Lake + Unity Catalog |
| Multi-engine (Spark + Flink + Trino) | Iceberg + REST catalog |
| Single-node analytics (<500GB) | DuckLake or DuckDB + Parquet |
| Need format interop | Delta Lake with UniForm |
| Need git-like data versioning | Iceberg + Nessie |

## Common Pitfalls

| Don't | Do |
|-------|-----|
| Skip compaction schedule | OPTIMIZE/rewrite_data_files weekly |
| Unlimited snapshot retention | Set retention period (7-30 days) |
| Ignore small files | Monitor file count, compact when >1000 per partition |
| Choose format by hype | Choose by engine compatibility and team expertise |
