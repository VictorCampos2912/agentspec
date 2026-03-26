# Delta Lake

> **Purpose**: Delta Lake 4.x — UniForm, transaction log, liquid clustering, deletion vectors, change data feed
> **Confidence**: 0.90
> **MCP Validated**: 2026-03-26

## Overview

Delta Lake is an open table format built on Parquet with ACID transactions via a JSON-based transaction log (`_delta_log/`). v4.x introduced liquid clustering (replacing Z-ORDER), deletion vectors (faster deletes without rewriting), and UniForm (automatic Iceberg/Hudi metadata generation for cross-engine reads).

## The Concept

```sql
-- Create Delta table with liquid clustering (replaces ZORDER)
CREATE TABLE catalog.analytics.orders (
    order_id        STRING,
    customer_id     STRING,
    order_date      DATE,
    total_amount    DECIMAL(12,2),
    status          STRING,
    region          STRING
)
USING delta
CLUSTER BY (region, order_date)  -- liquid clustering
TBLPROPERTIES (
    'delta.enableDeletionVectors' = 'true',
    'delta.universalFormat.enabledFormats' = 'iceberg',  -- UniForm
    'delta.enableChangeDataFeed' = 'true'
);

-- OPTIMIZE with liquid clustering (no ZORDER needed)
OPTIMIZE catalog.analytics.orders;

-- Change Data Feed: read changes between versions
SELECT * FROM table_changes('catalog.analytics.orders', 5, 10)
WHERE _change_type IN ('insert', 'update_postimage');

-- Deletion vectors: fast logical delete (no rewrite)
DELETE FROM catalog.analytics.orders WHERE status = 'cancelled';
-- Marks rows as deleted in deletion vector file; OPTIMIZE later reclaims space

-- VACUUM: clean up old files
VACUUM catalog.analytics.orders RETAIN 168 HOURS;  -- 7 days
```

## Quick Reference

| Feature | Delta OSS | Delta (Databricks) |
|---------|----------|-------------------|
| Liquid clustering | v3.2+ | DBR 13.3+ |
| Deletion vectors | v3.1+ | DBR 14.1+ |
| UniForm (Iceberg compat) | v3.2+ | DBR 13.3+ |
| Change Data Feed | v2.0+ | All versions |
| Photon acceleration | No | Yes |
| Predictive optimization | No | Yes (auto-OPTIMIZE, auto-VACUUM) |

| Transaction Log | Purpose |
|----------------|---------|
| `_delta_log/` | JSON commit files (0-9 commits) + checkpoint Parquet files |
| Checkpoint | Parquet snapshot every 10 commits (configurable) |
| Commit info | Schema, partition changes, file additions/removals per commit |

## Common Mistakes

### Wrong

```sql
-- Using ZORDER with liquid clustering (conflict)
OPTIMIZE orders ZORDER BY (region, order_date);  -- ERROR if liquid clustering enabled

-- VACUUM with too-short retention
VACUUM orders RETAIN 0 HOURS;  -- breaks time travel, active queries fail
```

### Correct

```sql
-- Liquid clustering: just run OPTIMIZE without ZORDER
OPTIMIZE orders;  -- clustering happens automatically

-- VACUUM with safe retention
VACUUM orders RETAIN 168 HOURS;  -- 7 days minimum
```

## Related

- [iceberg-v3](iceberg-v3.md)
- [catalog-wars](catalog-wars.md)
- [delta-operations pattern](../patterns/delta-operations.md)
