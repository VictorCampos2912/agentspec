# One Big Table (OBT)

> **Purpose**: Denormalized analytics table — when to use, materialization strategy, trade-offs
> **Confidence**: 0.90
> **MCP Validated**: 2026-03-26

## Overview

The One Big Table (OBT) pattern pre-joins all dimensions into a single wide table optimized for BI dashboards. Zero joins at query time means fast queries, but high storage and complex refresh logic. Best for specific dashboard use cases, not as a general warehouse strategy.

## The Pattern

```sql
-- ============================================================
-- OBT: Pre-joined analytics table for e-commerce dashboard
-- Grain: one row = one order line item (fully denormalized)
-- ============================================================

CREATE TABLE obt_order_analytics AS
WITH current_customers AS (
    SELECT * FROM dim_customer WHERE is_current = TRUE
)
SELECT
    -- Fact measures
    f.order_item_sk,
    f.order_id,
    f.quantity,
    f.unit_price,
    f.discount_pct,
    f.net_amount,
    f.tax_amount,
    f.shipping_cost,
    f.net_amount + f.tax_amount + f.shipping_cost AS gross_amount,

    -- Date attributes (denormalized)
    d.full_date        AS order_date,
    d.day_of_week,
    d.month_name,
    d.quarter,
    d.year,
    d.is_weekend,

    -- Customer attributes (denormalized)
    c.customer_id,
    c.customer_name,
    c.segment          AS customer_segment,
    c.region           AS customer_region,
    c.country          AS customer_country,

    -- Product attributes (denormalized)
    p.product_id,
    p.product_name,
    p.category         AS product_category,
    p.subcategory      AS product_subcategory,
    p.brand            AS product_brand,

    -- Computed metrics
    f.net_amount - (f.quantity * p.unit_cost) AS margin

FROM fact_order_items f
JOIN dim_date d ON f.date_sk = d.date_sk
JOIN current_customers c ON f.customer_sk = c.customer_sk
JOIN dim_product p ON f.product_sk = p.product_sk;

-- Incremental refresh strategy (for large tables)
-- Use MERGE or DELETE+INSERT for the changed date partition
DELETE FROM obt_order_analytics WHERE order_date >= CURRENT_DATE - INTERVAL '3 days';
INSERT INTO obt_order_analytics
SELECT /* same query as above */
WHERE d.full_date >= CURRENT_DATE - INTERVAL '3 days';
```

## Quick Reference

| Factor | Star Schema | OBT |
|--------|------------|-----|
| Query complexity | 3-6 JOINs | Zero JOINs |
| Storage | Lower (normalized dims) | Higher (repeated attrs) |
| Refresh complexity | Update dims independently | Must rebuild/merge entire table |
| Flexibility | Ad-hoc joins possible | Fixed set of attributes |
| Best for | Warehouse layer | Specific dashboard / BI tool |
| dbt materialization | table/incremental | table (full refresh) or incremental |

## Common Mistakes

### Wrong

```sql
-- OBT as the only layer — no way to add new dimensions without full rebuild
-- All analytics queries hit one massive table even for simple lookups
SELECT DISTINCT customer_segment FROM obt_order_analytics;  -- scans entire OBT
```

### Correct

```sql
-- OBT as a mart layer, star schema as the foundation
-- Simple lookups still use dimension tables
SELECT DISTINCT segment FROM dim_customer WHERE is_current = TRUE;

-- OBT only for dashboard-specific heavy queries
SELECT customer_segment, SUM(net_amount) FROM obt_order_analytics
WHERE year = 2026 GROUP BY customer_segment;
```

## Related

- [star-schema](star-schema.md)
- [dimensional-modeling](../concepts/dimensional-modeling.md)
- [normalization](../concepts/normalization.md)
