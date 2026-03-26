# Spark Quick Reference

> Fast lookup tables. For code examples, see linked files.

## DataFrame API Cheat Sheet

| Operation | PySpark | Spark SQL |
|-----------|---------|-----------|
| Select | `df.select("col_a", "col_b")` | `SELECT col_a, col_b` |
| Filter | `df.filter(col("age") > 21)` | `WHERE age > 21` |
| Group | `df.groupBy("dept").agg(sum("sal"))` | `GROUP BY dept` |
| Join | `df1.join(df2, "key", "inner")` | `JOIN df2 ON df1.key = df2.key` |
| Window | `Window.partitionBy("dept").orderBy("sal")` | `OVER (PARTITION BY dept ORDER BY sal)` |
| Sort | `df.orderBy(col("sal").desc())` | `ORDER BY sal DESC` |
| Distinct | `df.dropDuplicates(["key"])` | `QUALIFY ROW_NUMBER() = 1` |

## Spark SQL vs DataFrame API

| Choose DataFrame API When | Choose Spark SQL When |
|---------------------------|----------------------|
| Complex chained transformations | Ad-hoc analysis, familiar SQL users |
| Need type safety at compile time | Migrating existing SQL queries |
| UDF composition | Window functions (often cleaner in SQL) |
| Programmatic column generation | CTEs for readability |

## Partitioning Quick Reference

| Method | Use When | Effect |
|--------|----------|--------|
| `repartition(n)` | Need exact N partitions, shuffle OK | Full shuffle, hash-based |
| `repartition("col")` | Downstream joins/groups on col | Hash partition by column |
| `coalesce(n)` | Reduce partitions (no shuffle) | Merge adjacent partitions |
| `.partitionBy("col")` on write | Query patterns filter by col | Physical file partitioning |

## Format Comparison

| Format | Read Speed | Write Speed | Schema Evolution | Time Travel |
|--------|-----------|-------------|-----------------|-------------|
| Parquet | Fast | Fast | Limited | No |
| Delta Lake | Fast | Medium | Full | Yes |
| Iceberg | Fast | Medium | Full (partition too) | Yes |
| ORC | Fast | Fast | Limited | No |

## Performance Tuning Checklist

| Check | Setting | Default |
|-------|---------|---------|
| Auto broadcast | `spark.sql.autoBroadcastJoinThreshold` | 10MB |
| AQE enabled | `spark.sql.adaptive.enabled` | true (3.x) |
| Shuffle partitions | `spark.sql.shuffle.partitions` | 200 |
| Skew join | `spark.sql.adaptive.skewJoin.enabled` | true |
| Dynamic partition pruning | `spark.sql.optimizer.dynamicPartitionPruning.enabled` | true |

## Common Pitfalls

| Don't | Do |
|-------|-----|
| `df.collect()` on large data | `df.take(10)` or `df.show()` |
| UDFs for simple transforms | Built-in functions (`F.when`, `F.coalesce`) |
| `coalesce(1)` on large data | Appropriate partition count |
| `.cache()` without `.unpersist()` | Always unpersist when done |
| Fixed shuffle partitions | Let AQE auto-tune |
