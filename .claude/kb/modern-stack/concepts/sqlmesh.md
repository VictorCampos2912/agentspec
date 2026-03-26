# SQLMesh

> **Purpose**: SQLMesh virtual environments, change categories, automatic backfills, column-level lineage, plan/apply workflow
> **MCP Validated**: 2026-03-26

## Overview

SQLMesh is a data transformation framework that improves on dbt with virtual environments (no data duplication for dev/staging), automatic change classification, built-in scheduling, and column-level lineage. It can also run dbt projects via its compatibility layer.

## Key Concepts

### Virtual Environments

Unlike dbt (which creates full schema copies for dev), SQLMesh uses virtual environments that point to the same physical tables via views. This means:
- Zero data duplication for dev/staging environments
- Instant environment creation
- Safe testing against production data without copies

### Change Categories

When you modify a model, SQLMesh classifies the change:

| Category | Description | Action |
|----------|-------------|--------|
| **Breaking** | Column removed, type changed, logic altered | Full backfill of model + all downstream |
| **Non-breaking** | New column added, comment changed | Forward-only (no backfill needed) |
| **Metadata-only** | Description, tags, owner changed | No data operation |

### Plan/Apply Workflow

```bash
# Preview what will change (like terraform plan)
sqlmesh plan dev

# Apply changes to dev environment
sqlmesh apply dev

# Promote dev → prod
sqlmesh plan prod
sqlmesh apply prod
```

### Column-Level Lineage

SQLMesh tracks lineage at the column level, not just the model level:

```bash
# Show which upstream columns feed into a specific column
sqlmesh lineage mart_revenue.total_amount

# Output:
# mart_revenue.total_amount
#   ← int_orders.net_amount
#     ← stg_orders.quantity × stg_orders.unit_price
```

### Built-in Scheduler

SQLMesh includes a scheduler (no Airflow required for simple setups):

```bash
# Run all models that need updating
sqlmesh run

# Run with Airflow integration
sqlmesh plan --enable-airflow
```

## SQLMesh vs dbt Comparison

| Feature | SQLMesh | dbt Core |
|---------|---------|----------|
| Environments | Virtual (no data copy) | Full schema clone |
| Change detection | Automatic (breaking/non-breaking) | Manual (full refresh or incremental) |
| Backfill | Automatic, minimal | Manual via --full-refresh |
| Lineage | Column-level | Model-level |
| Scheduler | Built-in | Requires Airflow/Dagster |
| Testing | Built-in audits + tests | schema.yml tests |
| dbt compatibility | Yes (sqlmesh init -t dbt) | Native |
| IDE | Built-in web UI | dbt Cloud or third-party |

## When to Use

- **Choose SQLMesh** when: column-level lineage matters, you want zero-cost dev environments, you need automatic change classification, or you're starting fresh
- **Choose dbt** when: team already knows dbt, ecosystem packages are critical (dbt_utils, dbt_expectations), or dbt Cloud features needed (CI/CD, IDE, semantic layer)

## Trade-offs

| Pros | Cons |
|------|------|
| Virtual environments save storage/compute | Smaller community than dbt |
| Automatic change detection reduces errors | Fewer third-party packages |
| Column-level lineage for impact analysis | Learning curve for dbt users |
| Built-in scheduler simplifies stack | Less mature cloud offering |

## See Also

- [sqlmesh-workflow](../patterns/sqlmesh-workflow.md)
- [analytics-engineering](../concepts/analytics-engineering.md)
- [duckdb](../concepts/duckdb.md)
