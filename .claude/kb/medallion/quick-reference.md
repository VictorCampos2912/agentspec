# Medallion Architecture Quick Reference

> Fast lookup tables. For code examples, see linked files.
> **MCP Validated**: 2026-02-17

## Layer Comparison

| Property | Bronze | Silver | Gold |
|----------|--------|--------|------|
| **Data Quality** | Raw, as-is | Cleansed, validated | Business-ready |
| **Schema** | Schema-on-read | Schema-on-write | Star/snowflake |
| **Duplicates** | Allowed | Deduplicated | Aggregated |
| **Format** | Delta (append) | Delta (merge) | Delta (overwrite/merge) |
| **Consumers** | Data engineers | Analysts, ML engineers | Business users, dashboards |
| **Retention** | Long (years) | Medium (months) | Short-medium (refreshed) |

## Naming Convention

| Layer | Database Pattern | Table Pattern |
|-------|-----------------|---------------|
| Bronze | `bronze_{domain}` | `raw_{source}_{entity}` |
| Silver | `silver_{domain}` | `cleansed_{entity}` |
| Gold | `gold_{domain}` | `dim_{entity}`, `fact_{entity}`, `agg_{metric}` |

## Decision Matrix

| Use Case | Choose |
|----------|--------|
| Raw event ingestion | Bronze append-only with metadata |
| Deduplication + cleansing | Silver MERGE with dedup window |
| Business KPIs / dashboards | Gold pre-aggregated tables |
| ML feature engineering | Silver or dedicated feature layer |
| Data quality enforcement | Quality gates between Bronze-Silver |
| Schema changes from source | Schema evolution at Bronze, migrate Silver |
| Historical tracking (SCD2) | Silver layer with valid_from/valid_to |
| Real-time + batch combined | Bronze streaming, Silver/Gold batch |

## Common Pitfalls

| Don't | Do |
|-------|-----|
| Transform in Bronze | Keep Bronze raw, add only metadata columns |
| Skip deduplication in Silver | Always deduplicate using business keys |
| Build Gold directly from Bronze | Always go Bronze -> Silver -> Gold |
| One monolithic Gold table | Create purpose-specific Gold aggregates |
| Ignore schema evolution | Use Delta Lake schema evolution features |
| Hard-delete in Bronze | Soft-delete or retain all raw records |
| Store secrets in table properties | Use secret scopes or environment variables |

## Essential Delta Lake Commands

| Operation | SQL |
|-----------|-----|
| Create Bronze table | `CREATE TABLE IF NOT EXISTS bronze.raw USING DELTA` |
| Merge into Silver | `MERGE INTO silver.t USING src ON key WHEN MATCHED ... WHEN NOT MATCHED ...` |
| Schema evolution | `ALTER TABLE t SET TBLPROPERTIES ('delta.columnMapping.mode' = 'name')` |
| Optimize | `OPTIMIZE silver.t ZORDER BY (partition_col)` |
| Vacuum | `VACUUM bronze.t RETAIN 168 HOURS` |
| Time travel | `SELECT * FROM silver.t VERSION AS OF 5` |

## Related Documentation

| Topic | Path |
|-------|------|
| Layer Transitions | `patterns/layer-transitions.md` |
| Quality Gates | `patterns/data-quality-gates.md` |
| Full Index | `index.md` |
