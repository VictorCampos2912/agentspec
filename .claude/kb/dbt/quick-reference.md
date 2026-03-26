# dbt Quick Reference

> Fast lookup tables. For code examples, see linked files.

## Model Types

| Prefix | Layer | Materialization | Purpose |
|--------|-------|----------------|---------|
| `stg_` | Staging | view | 1:1 with source, rename + cast only |
| `int_` | Intermediate | ephemeral/view | Business logic transformations |
| `fct_` | Marts (facts) | table/incremental | Measurable events at a grain |
| `dim_` | Marts (dimensions) | table | Descriptive attributes |
| `snap_` | Snapshots | snapshot | SCD Type 2 history tracking |

## Incremental Strategy Decision Matrix

| Strategy | Best For | Unique Key | Volume |
|----------|---------|------------|--------|
| `append` | Event logs, immutable data | Not needed | Any |
| `merge` | Dimensions, updatable facts | Required | < 100M rows |
| `delete+insert` | Large facts with updates | Required | > 100M rows |
| `insert_overwrite` | Partitioned tables | Partition key | Any (partition-level) |

## Materialization Decision Tree

| Question | Answer | Materialization |
|----------|--------|----------------|
| Referenced by many models? | Yes | `table` |
| Large and needs updates? | Yes | `incremental` |
| Small, used by one model? | Yes | `ephemeral` |
| Queried directly by BI? | Yes | `table` |
| Default / unsure? | — | `view` |

## CLI Cheat Sheet

| Command | Purpose |
|---------|---------|
| `dbt run` | Execute models |
| `dbt test` | Run all tests |
| `dbt build` | run + test + snapshot in DAG order |
| `dbt compile` | Generate SQL without executing |
| `dbt source freshness` | Check source data staleness |
| `dbt docs generate && dbt docs serve` | Generate and view docs |
| `dbt run --select tag:daily` | Run models with specific tag |
| `dbt run --select +model_name` | Run model and all ancestors |

## Common Jinja Patterns

| Pattern | Example |
|---------|---------|
| Conditional | `{% if is_incremental() %} WHERE ... {% endif %}` |
| Loop | `{% for col in columns %} {{ col }} {% endfor %}` |
| Set variable | `{% set my_var = 'value' %}` |
| Whitespace control | `{%- set x = 1 -%}` (strips surrounding whitespace) |
| Cross-db function | `{{ dbt.date_trunc('month', 'created_at') }}` |

## Fusion Engine vs Core

| Feature | dbt Core | dbt Fusion |
|---------|----------|-----------|
| Parse speed (10K models) | 30-60s | < 1s |
| Column-level lineage | No | Built-in |
| IDE autocomplete | Basic | Full LSP |
| Language | Python | Rust (via SDF) |
| Status | GA | Beta (GA expected 2026) |

## Common Pitfalls

| Don't | Do |
|-------|-----|
| `SELECT *` in staging | Explicit column list with rename |
| Hardcode schema names | Use `{{ target.schema }}` or `generate_schema_name` |
| Skip tests on incremental | Test after every `dbt build` |
| ref() to source tables | Use `source()` for raw data |

## Related Documentation

| Topic | Path |
|-------|------|
| Getting Started | `concepts/model-types.md` |
| Full Index | `index.md` |
