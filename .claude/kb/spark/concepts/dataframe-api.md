# DataFrame API

> **Purpose**: Core PySpark transformations — select, filter, groupBy, join, window, Column expressions, UDFs
> **Confidence**: 0.95
> **MCP Validated**: 2026-03-26

## Overview

The DataFrame API is PySpark's primary abstraction for distributed data processing. DataFrames are lazily evaluated — transformations build a logical plan that Catalyst optimizes before execution. Column expressions (`F.col`, `F.lit`, `F.when`) are preferred over UDFs for performance.

## The Concept

```python
from pyspark.sql import SparkSession, DataFrame
from pyspark.sql import functions as F
from pyspark.sql.types import StructType, StructField, StringType, DecimalType, TimestampType

spark = SparkSession.builder.appName("orders_transform").getOrCreate()

def transform_orders(df: DataFrame) -> DataFrame:
    """Clean and enrich raw orders."""
    return (
        df
        .filter(F.col("status").isin("completed", "shipped"))
        .withColumn("amount_usd", F.col("amount") * F.col("exchange_rate"))
        .withColumn("order_date", F.to_date("created_at"))
        .select(
            F.col("order_id"),
            F.col("customer_id"),
            F.col("amount_usd").cast(DecimalType(12, 2)),
            F.col("order_date"),
            F.col("status"),
        )
        .dropDuplicates(["order_id"])
    )
```

## Quick Reference

| Operation | Method | Notes |
|-----------|--------|-------|
| Select columns | `.select("col1", F.col("col2"))` | Accepts strings or Column objects |
| Filter rows | `.filter(F.col("x") > 10)` | Alias: `.where()` |
| Add column | `.withColumn("new", expr)` | Replaces if name exists |
| Rename | `.withColumnRenamed("old", "new")` | Single column rename |
| Group + agg | `.groupBy("key").agg(F.sum("val"))` | Chain multiple aggs |
| Join | `.join(other, on="key", how="left")` | inner, left, right, full, semi, anti |
| Sort | `.orderBy(F.col("x").desc())` | `.desc_nulls_last()` for null handling |
| Distinct | `.dropDuplicates(["col"])` | Subset dedup; `.distinct()` for all cols |

## Common Mistakes

### Wrong

```python
# UDF for simple string operation — 5-10x slower than built-in
from pyspark.sql.types import StringType

@udf(returnType=StringType())
def upper_name(name):
    return name.upper() if name else None

df = df.withColumn("name_upper", upper_name("name"))
```

### Correct

```python
# Built-in function — runs in JVM, Catalyst-optimized
df = df.withColumn("name_upper", F.upper(F.col("name")))
```

## Related

- [partitioning](../concepts/partitioning.md)
- [window-functions](../patterns/window-functions.md)
- [performance-tuning](../patterns/performance-tuning.md)
