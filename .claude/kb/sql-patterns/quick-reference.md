# SQL Patterns Quick Reference

> Fast lookup tables. For code examples, see linked files.

## Window Function Syntax by Dialect

| Function | Snowflake | BigQuery | DuckDB | Spark SQL |
|----------|-----------|----------|--------|-----------|
| `ROW_NUMBER()` | Yes | Yes | Yes | Yes |
| `QUALIFY` | Yes | Yes | Yes | No |
| `NTILE(n)` | Yes | Yes | Yes | Yes |
| `FIRST_VALUE` | Yes | Yes | Yes | Yes |
| `NTH_VALUE` | Yes | Yes | Yes | Yes |

## CTE Support Matrix

| Feature | Snowflake | BigQuery | DuckDB | Postgres | Spark SQL |
|---------|-----------|----------|--------|----------|-----------|
| Standard CTE | Yes | Yes | Yes | Yes | Yes |
| Recursive CTE | Yes | Yes | Yes | Yes | No |
| Materialized CTE | No | No | No | Yes | No |
| CTE in DML | Yes | Yes | Yes | Yes | Limited |

## QUALIFY Clause (Filter Window Results)

| Dialect | Support | Alternative |
|---------|---------|-------------|
| Snowflake | Native | — |
| BigQuery | Native | — |
| DuckDB | Native | — |
| Spark SQL | No | Subquery + WHERE |
| Postgres | No | Subquery + WHERE |

## Cross-Dialect Type Mapping

| Concept | Snowflake | BigQuery | DuckDB | Postgres |
|---------|-----------|----------|--------|----------|
| Array | `ARRAY` | `ARRAY<T>` | `T[]` | `T[]` |
| Struct | `OBJECT` | `STRUCT<>` | `STRUCT` | Composite type |
| JSON | `VARIANT` | `JSON` | `JSON` | `jsonb` |
| Flatten | `FLATTEN()` | `UNNEST()` | `unnest()` | `unnest()` |
| Date trunc | `DATE_TRUNC('month', d)` | `DATE_TRUNC(d, MONTH)` | `DATE_TRUNC('month', d)` | `DATE_TRUNC('month', d)` |

## Anti-Pattern Checklist

| Anti-Pattern | Fix |
|-------------|-----|
| `SELECT *` | Explicit column list |
| `DISTINCT` masking duplication | Fix upstream JOIN |
| Correlated subquery | Rewrite as JOIN or window |
| Implicit type cast | Explicit `CAST(col AS type)` |
| `ORDER BY` without `LIMIT` | Add LIMIT or remove ORDER BY |
| `NOT IN (subquery with NULLs)` | Use `NOT EXISTS` instead |
