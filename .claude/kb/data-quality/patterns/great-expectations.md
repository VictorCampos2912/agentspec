# Great Expectations

> **Purpose**: Suite definition, checkpoint config, data docs, custom expectations
> **MCP Validated**: 2026-03-26

## When to Use

- Need comprehensive data validation with rich HTML reporting
- Want a Python-native testing framework (vs SQL-only like dbt tests)
- Building reusable expectation suites across multiple datasets
- Require custom business logic validation beyond built-in checks

## Implementation

```python
import great_expectations as gx

# --- Context Setup ---
context = gx.get_context()

# --- Data Source + Asset ---
datasource = context.data_sources.add_pandas("pandas_source")
data_asset = datasource.add_dataframe_asset("orders")
batch = data_asset.add_batch_definition_whole_dataframe("full_batch")

# --- Expectation Suite ---
suite = context.suites.add(gx.ExpectationSuite(name="orders_quality"))

# Completeness
suite.add_expectation(
    gx.expectations.ExpectColumnValuesToNotBeNull(column="order_id"))
suite.add_expectation(
    gx.expectations.ExpectColumnValuesToNotBeNull(column="customer_id"))

# Uniqueness
suite.add_expectation(
    gx.expectations.ExpectColumnValuesToBeUnique(column="order_id"))

# Validity — enum check
suite.add_expectation(
    gx.expectations.ExpectColumnValuesToBeInSet(
        column="status",
        value_set=["pending", "completed", "cancelled", "refunded"]))

# Range check
suite.add_expectation(
    gx.expectations.ExpectColumnValuesToBeBetween(
        column="amount", min_value=0, max_value=1_000_000))

# Row count
suite.add_expectation(
    gx.expectations.ExpectTableRowCountToBeBetween(
        min_value=1000, max_value=10_000_000))

# Regex pattern
suite.add_expectation(
    gx.expectations.ExpectColumnValuesToMatchRegex(
        column="email", regex=r"^[^@]+@[^@]+\.[^@]+$"))


# --- Validation Definition + Checkpoint ---
validation_def = context.validation_definitions.add(
    gx.ValidationDefinition(
        name="orders_validation",
        data=batch,
        suite=suite))

checkpoint = context.checkpoints.add(
    gx.Checkpoint(
        name="orders_checkpoint",
        validation_definitions=[validation_def]))

# --- Run ---
result = checkpoint.run()
print(f"Success: {result.success}")
```

## Configuration

| Component | Purpose | Key Options |
|-----------|---------|-------------|
| `DataSource` | Connection to data | pandas, spark, sql |
| `ExpectationSuite` | Collection of checks | Name, expectations list |
| `ValidationDefinition` | Links suite to data | batch + suite |
| `Checkpoint` | Execution unit | Actions (Slack, email, store) |
| `DataDocs` | HTML quality reports | Auto-generated from results |

## Example Usage

```bash
# CLI workflow
great_expectations suite new
great_expectations checkpoint run orders_checkpoint
great_expectations docs build
```

## See Also

- [dbt-testing](../patterns/dbt-testing.md)
- [quality-dimensions](../concepts/quality-dimensions.md)
