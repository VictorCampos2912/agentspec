# DuckDB

> **Purpose:** In-process columnar OLAP database for analytical workloads without infrastructure overhead
> **Confidence:** 0.90
> **MCP Validated:** 2026-03-26

## Overview

DuckDB is an in-process analytical database that runs inside your application -- no server, no daemon, no cluster. It uses a columnar-vectorized execution engine optimized for OLAP queries over Parquet, CSV, JSON, and Iceberg files. Since v1.0 (2024) and through v1.5, DuckDB has matured into a production-grade engine that replaces Spark for many sub-terabyte analytical workloads.

DuckDB follows the SQLite model: a single library linked into your process. But where SQLite targets OLTP row-by-row operations, DuckDB targets analytical queries that scan millions of rows across columns.

## Key Concepts

### Columnar Execution Engine

DuckDB stores and processes data in columnar format using a vectorized pipeline. Queries operate on compressed column chunks rather than row-at-a-time, delivering order-of-magnitude speedups for analytical aggregations, filters, and joins compared to row-oriented databases.

### Extension System

DuckDB ships lean and extends via loadable extensions:

- **httpfs** -- Query files on S3, GCS, Azure Blob, or HTTP endpoints directly
- **iceberg** -- Read Apache Iceberg tables with snapshot time-travel
- **spatial** -- GEOMETRY type with ST_ functions for geospatial analytics
- **json** -- Structured JSON parsing and querying
- **delta** -- Read Delta Lake tables
- **postgres_scanner / mysql_scanner** -- Attach and query remote Postgres/MySQL

Extensions are installed with `INSTALL httpfs; LOAD httpfs;` or auto-loaded when needed.

### VARIANT Type

Introduced for semi-structured data, VARIANT stores heterogeneous nested data (similar to Snowflake's VARIANT). Enables schema-on-read for JSON-heavy workloads without pre-defining column types.

### Multi-File Queries (Glob Patterns)

DuckDB queries multiple files as a single table using glob patterns:

```sql
SELECT * FROM read_parquet('s3://bucket/events/year=2025/**/*.parquet');
SELECT * FROM read_csv('data/sales_*.csv', union_by_name=true);
SELECT * FROM read_json('logs/*.json');
```

### Zero-Copy Apache Arrow

DuckDB integrates with Apache Arrow via zero-copy transfers. DataFrames from Polars, Pandas, or any Arrow-producing library can be queried without serialization. Results flow back as Arrow record batches for downstream processing.

### MCP Server Integration

DuckDB can serve as the analytical backend for MCP-based AI agents. An MCP server wrapping DuckDB lets LLM agents query local files, data lakes, and attached databases through natural language translated to SQL.

## When to Use

- **Ad-hoc analytics** on Parquet, CSV, JSON, or Iceberg files without spinning up a warehouse
- **CI/CD data testing** -- replace warehouse queries with local DuckDB assertions
- **Local development** -- prototype pipelines that will run on Spark/warehouse in production
- **Embedded analytics** -- power dashboards (Evidence.dev, Observable) with zero infrastructure
- **Sub-500GB workloads** where Spark cluster overhead is unjustified
- **Data quality checks** running inside Python test suites or CLI scripts

## Trade-offs

| Strength | Limitation |
|----------|------------|
| Zero infrastructure | Single-node only, no distributed execution |
| Reads Parquet/Iceberg/Delta natively | Write support for lakehouse formats is limited |
| Sub-second queries on GB-scale data | Multi-TB datasets need Spark or a warehouse |
| Embeds in Python, Node, R, Java, Rust | Concurrent write access is restricted |
| Extension ecosystem growing fast | Some extensions are community-maintained |
| Free and open source (MIT) | No managed service (by design) |

## See Also

- [DuckDB Patterns](../patterns/duckdb-patterns.md) -- SQL recipes for Parquet, S3, Iceberg, CI/CD
- [Local-First Analytics](../patterns/local-first-analytics.md) -- DuckDB + Evidence.dev stack
- [Polars](polars.md) -- DataFrame library with Arrow interop to DuckDB
- [Lakehouse Architecture](../../lakehouse/) -- Iceberg/Delta context for DuckDB integration
