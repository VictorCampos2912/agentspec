# Polars

> **Purpose:** High-performance DataFrame library with lazy evaluation and native Arrow backend
> **Confidence:** 0.90
> **MCP Validated:** 2026-03-26

## Overview

Polars is a DataFrame library written in Rust, designed to replace Pandas for data engineering and analytical workloads. It uses Apache Arrow as its in-memory format, supports lazy evaluation with a query optimizer, and handles larger-than-RAM datasets through streaming execution. Polars operates on a single node but routinely outperforms Spark on datasets that fit within one machine's resources.

The library exposes two APIs: an eager API for interactive exploration (similar to Pandas) and a lazy API that builds a logical plan and optimizes it before execution.

## Key Concepts

### Lazy vs Eager Evaluation

- **Eager mode** (`pl.DataFrame`) -- Operations execute immediately. Good for exploration, prototyping, and small datasets. Familiar to Pandas users.
- **Lazy mode** (`pl.LazyFrame`) -- Operations build a logical plan. Calling `.collect()` triggers the query optimizer, which applies predicate pushdown, projection pushdown, and common subexpression elimination before executing. Always prefer lazy for production pipelines.

```python
# Eager: executes each step immediately
df = pl.read_parquet("events.parquet")
result = df.filter(pl.col("status") == "active").group_by("region").agg(pl.col("revenue").sum())

# Lazy: builds plan, optimizes, then executes
result = (
    pl.scan_parquet("events.parquet")
    .filter(pl.col("status") == "active")
    .group_by("region")
    .agg(pl.col("revenue").sum())
    .collect()
)
```

### Expression API

Polars replaces method chaining on columns with a composable expression system. Expressions are declarative, parallelizable, and optimizable:

```python
pl.col("price") * pl.col("quantity")          # arithmetic
pl.col("name").str.to_lowercase()             # string ops
pl.col("timestamp").dt.year()                 # datetime ops
pl.when(pl.col("score") > 90).then("A")      # conditional
```

### Query Optimizer

The lazy engine applies optimizations automatically:
- **Predicate pushdown** -- filters move to the earliest possible point, reducing I/O
- **Projection pushdown** -- only columns needed downstream are read from disk
- **Slice pushdown** -- LIMIT operations propagate to source scans
- **Common subexpression elimination** -- shared computations are computed once

### Streaming Mode

For datasets larger than available RAM, Polars processes data in streaming batches:

```python
result = pl.scan_parquet("huge_dataset/*.parquet").filter(...).collect(streaming=True)
```

### Polars Cloud

Polars Cloud extends the single-node library to distributed execution. It takes the same Polars code and runs it across a cluster, targeting workloads between 100GB and multi-TB where a single machine is insufficient but Spark is overkill.

### Arrow Interop

Polars uses Arrow as its native memory format, enabling zero-copy data exchange with DuckDB, PyArrow, and any Arrow-compatible system. Convert freely between ecosystems without serialization overhead.

## When to Use

- **Replacing Pandas** in pipelines where performance or memory is a concern
- **Single-node data engineering** on datasets from MBs to hundreds of GBs
- **ETL pipelines** where lazy evaluation reduces I/O and compute
- **Feature engineering** in ML pipelines needing fast group-by and window operations
- **Interop layer** between DuckDB (SQL) and Python processing (expressions)
- **Streaming ingestion** of larger-than-RAM files without resorting to Spark

## Trade-offs

| Strength | Limitation |
|----------|------------|
| 5-50x faster than Pandas on typical workloads | Smaller ecosystem of plugins vs Pandas |
| Lazy evaluation with automatic optimization | Learning curve for expression API |
| Native Arrow format, zero-copy interop | Not a drop-in Pandas replacement (different API) |
| Streaming mode for larger-than-RAM data | True distributed execution requires Polars Cloud |
| Rust core with Python, Node, R bindings | Some niche Pandas functions not yet ported |
| Memory-efficient columnar processing | Community smaller (but growing fast) |

### Polars vs Pandas

| Dimension | Pandas | Polars |
|-----------|--------|--------|
| Execution | Eager only | Eager + Lazy |
| Backend | NumPy (row-ish) | Arrow (columnar) |
| Parallelism | Single-threaded | Multi-threaded by default |
| Memory | 2-10x dataset size | ~1x dataset size |
| API style | Method chaining on Series | Expression-based |
| Larger-than-RAM | No (without Dask) | Yes (streaming mode) |

## See Also

- [Polars Patterns](../patterns/polars-patterns.md) -- Lazy pipelines, expressions, Arrow interop
- [DuckDB](duckdb.md) -- Complementary SQL engine with Arrow exchange
- [Local-First Analytics](../patterns/local-first-analytics.md) -- Polars in local-first stacks
