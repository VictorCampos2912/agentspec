# Cross-Dialect Patterns

> **Purpose**: DuckDB, Snowflake, BigQuery, Spark SQL syntax differences and translation patterns
> **MCP Validated**: 2026-03-26

## When to Use

- Migrating queries between data platforms
- Writing portable SQL that works across warehouses
- Translating Snowflake-specific syntax to BigQuery or vice versa
- Leveraging DuckDB-specific features (QUALIFY, STRUCT, list comprehensions)

## Implementation

```sql
-- === DATE/TIME FUNCTIONS ===

-- Date truncation
-- Snowflake:  DATE_TRUNC('MONTH', order_date)
-- BigQuery:   DATE_TRUNC(order_date, MONTH)
-- DuckDB:     DATE_TRUNC('MONTH', order_date)
-- Spark SQL:  DATE_TRUNC('MONTH', order_date)  -- or TRUNC(order_date, 'MM')

-- Date difference
-- Snowflake:  DATEDIFF('DAY', start_date, end_date)
-- BigQuery:   DATE_DIFF(end_date, start_date, DAY)
-- DuckDB:     DATE_DIFF('DAY', start_date, end_date)
-- Spark SQL:  DATEDIFF(end_date, start_date)

-- Current timestamp
-- Snowflake:  CURRENT_TIMESTAMP()
-- BigQuery:   CURRENT_TIMESTAMP()
-- DuckDB:     NOW() or CURRENT_TIMESTAMP
-- Spark SQL:  CURRENT_TIMESTAMP()


-- === STRING FUNCTIONS ===

-- String aggregation
-- Snowflake:  LISTAGG(col, ', ') WITHIN GROUP (ORDER BY col)
-- BigQuery:   STRING_AGG(col, ', ' ORDER BY col)
-- DuckDB:     STRING_AGG(col, ', ' ORDER BY col)
-- Spark SQL:  COLLECT_LIST(col)  -- returns array, not string


-- === ARRAY OPERATIONS ===

-- Array creation
-- Snowflake:  ARRAY_CONSTRUCT(1, 2, 3)
-- BigQuery:   [1, 2, 3]
-- DuckDB:     [1, 2, 3]
-- Spark SQL:  ARRAY(1, 2, 3)

-- Array unnest/flatten
-- Snowflake:  SELECT f.VALUE FROM table, LATERAL FLATTEN(array_col) f
-- BigQuery:   SELECT val FROM table, UNNEST(array_col) AS val
-- DuckDB:     SELECT UNNEST(array_col) FROM table
-- Spark SQL:  SELECT EXPLODE(array_col) FROM table


-- === QUALIFY (filter on window functions) ===
-- Supported: Snowflake, BigQuery, DuckDB, Databricks SQL
-- NOT supported: PostgreSQL, MySQL, standard Spark SQL

-- DuckDB/Snowflake/BigQuery:
SELECT * FROM orders
QUALIFY ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY order_date DESC) = 1;

-- PostgreSQL/Spark workaround:
SELECT * FROM (
    SELECT *, ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY order_date DESC) AS rn
    FROM orders
) sub WHERE rn = 1;


-- === STRUCT / SEMI-STRUCTURED ===

-- Struct access
-- Snowflake:  col:key::STRING  (variant) or col['key']
-- BigQuery:   col.key
-- DuckDB:     col.key  or  col['key']
-- Spark SQL:  col.key

-- JSON parsing
-- Snowflake:  PARSE_JSON('{"a":1}'):a::INT
-- BigQuery:   JSON_VALUE('{"a":1}', '$.a')
-- DuckDB:     '{"a":1}'::JSON->>'a'
-- Spark SQL:  GET_JSON_OBJECT('{"a":1}', '$.a')
```

## Configuration

| Feature | Snowflake | BigQuery | DuckDB | Spark SQL |
|---------|-----------|----------|--------|-----------|
| QUALIFY | Yes | Yes | Yes | Databricks only |
| STRUCT type | VARIANT/OBJECT | STRUCT | STRUCT | STRUCT |
| ARRAY type | ARRAY | ARRAY | LIST | ARRAY |
| MERGE | Yes | Yes | Yes (0.10+) | Yes |
| LATERAL JOIN | Yes | Correlated subquery | Yes | No |
| Regex | REGEXP_LIKE | REGEXP_CONTAINS | REGEXP_MATCHES | RLIKE |

## Example Usage

```sql
-- DuckDB-specific power features
-- List comprehension
SELECT [x * 2 FOR x IN [1, 2, 3, 4] IF x > 1];  -- [4, 6, 8]

-- Direct Parquet/CSV query
SELECT * FROM 's3://bucket/data/*.parquet' WHERE year = 2026;

-- COLUMNS expression (dynamic column selection)
SELECT COLUMNS('revenue_.*') FROM quarterly_report;
```

## See Also

- [pivot-unpivot](../patterns/pivot-unpivot.md)
- [set-operations](../concepts/set-operations.md)
