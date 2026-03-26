# Incremental Strategies

> **Purpose**: Choose the right incremental materialization strategy for your data shape
> **Confidence**: 0.95
> **MCP Validated**: 2026-03-26

## Overview

Incremental models process only new or changed data instead of rebuilding entire tables. dbt supports four strategies: **append** (insert-only), **merge** (upsert), **delete+insert** (replace matching rows), and **insert_overwrite** (replace partitions). Strategy selection depends on data mutability, volume, and warehouse capabilities.

## The Concept

```sql
-- Merge strategy: upsert rows by unique key
{{
    config(
        materialized='incremental',
        unique_key='order_id',
        incremental_strategy='merge',
        on_schema_change='append_new_columns'
    )
}}

select
    order_id,
    customer_id,
    order_total,
    status,
    updated_at
from {{ ref('stg_orders') }}

{% if is_incremental() %}
    -- Only process rows newer than the latest in target
    where updated_at > (select max(updated_at) from {{ this }})
{% endif %}
```

## Quick Reference

| Strategy | Mutability | Unique Key | Best Warehouse | Volume Threshold |
|----------|-----------|------------|---------------|-----------------|
| `append` | Immutable events | Not needed | Any | Any |
| `merge` | Mutable rows | Required | Snowflake, BigQuery | < 100M rows |
| `delete+insert` | Mutable rows | Required | Redshift, Postgres | > 100M rows |
| `insert_overwrite` | Partition-level | Partition key | BigQuery, Spark | Any (partition) |

## Common Mistakes

### Wrong

```sql
-- No is_incremental() guard = full table scan every run
{{
    config(materialized='incremental', unique_key='event_id')
}}
select * from {{ ref('stg_events') }}
-- Missing WHERE clause for incremental filter!
```

### Correct

```sql
{{
    config(materialized='incremental', unique_key='event_id')
}}
select * from {{ ref('stg_events') }}
{% if is_incremental() %}
    where event_timestamp > (select max(event_timestamp) from {{ this }})
{% endif %}
```

## Related

- [model-types](../concepts/model-types.md)
- [incremental-model](../patterns/incremental-model.md)
