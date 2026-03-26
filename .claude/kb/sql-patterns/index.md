# SQL Patterns Knowledge Base

> **Purpose**: Cross-dialect SQL — window functions, CTEs, deduplication, DuckDB/Snowflake/BigQuery/Spark
> **MCP Validated**: 2026-03-26

## Quick Navigation

### Concepts (< 150 lines each)

| File | Purpose |
|------|---------|
| [concepts/window-functions.md](concepts/window-functions.md) | ROW_NUMBER, RANK, LAG/LEAD, QUALIFY |
| [concepts/cte-patterns.md](concepts/cte-patterns.md) | Recursive, chained, materialized CTEs |
| [concepts/set-operations.md](concepts/set-operations.md) | UNION, LATERAL, UNNEST across dialects |

### Patterns (< 200 lines each)

| File | Purpose |
|------|---------|
| [patterns/deduplication.md](patterns/deduplication.md) | Exact, fuzzy, SCD-aware dedup |
| [patterns/gap-and-island.md](patterns/gap-and-island.md) | Date gaps, session detection |
| [patterns/pivot-unpivot.md](patterns/pivot-unpivot.md) | Cross-dialect PIVOT/UNPIVOT |
| [patterns/cross-dialect.md](patterns/cross-dialect.md) | DuckDB specifics, dialect translation |

---

## Quick Reference

- [quick-reference.md](quick-reference.md) - Fast lookup tables

## Agent Usage

| Agent | Primary Files | Use Case |
|-------|---------------|----------|
| sql-optimizer | All files | Query optimization, dialect translation |
| code-reviewer | patterns/cross-dialect.md | SQL review |
| dbt-specialist | patterns/deduplication.md | Model SQL patterns |
