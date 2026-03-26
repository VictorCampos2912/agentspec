# Data Modeling Quick Reference

> Fast lookup tables. For code examples, see linked files.

## Modeling Approach Decision Matrix

| Approach | Best For | Complexity | Query Speed |
|----------|---------|-----------|-------------|
| Star Schema | BI/analytics with known queries | Low | Fast |
| Snowflake Schema | Normalized dimensions, storage-efficient | Medium | Medium |
| Data Vault | Enterprise DWH, many sources, audit | High | Slow (needs marts) |
| One Big Table (OBT) | Simple analytics, few consumers | Low | Fastest |

## SCD Type Comparison

| Type | Technique | History | Storage | Complexity |
|------|----------|---------|---------|-----------|
| Type 1 | Overwrite | None | Minimal | Trivial |
| Type 2 | Add row + valid_from/to | Full | High | Medium |
| Type 3 | Add previous_value column | Limited (1 prior) | Low | Low |
| Type 4 | Mini-dimension table | Full (separate) | Medium | Medium |
| Type 6 | Type 1+2+3 hybrid | Full + current flag | Highest | High |

## Normalization Forms

| Form | Rule | When to Use |
|------|------|-------------|
| 1NF | Atomic values, no repeating groups | Always (minimum) |
| 2NF | No partial dependencies on composite key | Transactional systems |
| 3NF | No transitive dependencies | OLTP databases |
| BCNF | Every determinant is a candidate key | Strict data integrity |
| Denormalized | Strategic redundancy | Analytical queries (star schema) |

## Schema Evolution Compatibility

| Change Type | Backward Compatible | Forward Compatible | Strategy |
|-------------|--------------------|--------------------|----------|
| Add column | Yes | Yes (with default) | ALTER TABLE ADD |
| Drop column | No | Yes | Deprecate → grace period → drop |
| Rename column | No | No | Add new → backfill → deprecate old |
| Widen type (int→bigint) | Yes | No | ALTER TABLE ALTER |
| Narrow type (bigint→int) | No | No | Avoid — data loss risk |

## Key Design Rules

| Rule | Rationale |
|------|-----------|
| Every table has a primary key | Uniqueness guarantee, merge key |
| Facts have numeric measures | Aggregatable, NOT NULL DEFAULT 0 |
| Dimensions have surrogate keys | Insulates from source key changes |
| Date dimension is always separate | Reusable across all facts |
| Grain is documented | Prevents accidental fan-out |

## Common Pitfalls

| Don't | Do |
|-------|-----|
| Skip grain definition | Document "one row per ___" on every fact |
| Nullable fact measures | DEFAULT 0 for numeric measures |
| Natural keys as PKs | Surrogate keys (hash or sequence) |
| VARCHAR without limits | Set reasonable max: VARCHAR(256) |
