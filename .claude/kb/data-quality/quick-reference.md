# Data Quality Quick Reference

> Fast lookup tables. For code examples, see linked files.

## Quality Dimensions

| Dimension | Definition | Measurement |
|-----------|-----------|-------------|
| Completeness | % non-null for required fields | `COUNT(col) / COUNT(*)` |
| Accuracy | Data matches real-world truth | Cross-reference with source |
| Consistency | Same value across systems | Compare source vs target |
| Timeliness | Data available within SLA | `NOW() - MAX(loaded_at)` |
| Uniqueness | No duplicate records | `COUNT(*) = COUNT(DISTINCT pk)` |
| Validity | Values within accepted range | Accepted values, regex, range |

## Framework Comparison

| Feature | dbt Tests | Great Expectations | Soda Core | Monte Carlo |
|---------|----------|-------------------|-----------|-------------|
| Language | YAML + SQL | Python + YAML | SodaCL (YAML) | Config UI |
| Setup time | Minutes | Hours | Minutes | Hours |
| Custom checks | Generic test macros | Custom expectations | Custom checks | Limited |
| Anomaly detection | No | Limited | RAD (built-in) | ML-based |
| Cost | Free (OSS) | Free (Core) / Paid (Cloud) | Free (Core) / Paid (Cloud) | Paid |
| Best for | dbt projects | Python pipelines | Any (universal) | Enterprise observability |

## Data Contract Format Comparison

| Feature | ODCS | Soda Contracts | dbt Contracts |
|---------|------|---------------|---------------|
| Format | YAML | YAML (SodaCL) | YAML (schema.yml) |
| Schema enforcement | Full | Checks-based | Column types + tests |
| SLA support | Built-in | Via checks | Via source freshness |
| Versioning | Semantic versions | Implicit | Model versions |
| CI/CD integration | CLI validation | `soda scan` | `dbt build` |

## Common Pitfalls

| Don't | Do |
|-------|-----|
| Ship models without tests | Minimum: unique + not_null on PK |
| Ignore freshness | Monitor with `dbt source freshness` or Soda |
| Test only happy path | Include edge cases: 0 rows, all nulls |
| Contracts without owners | Every contract needs `owner: team@email` |
