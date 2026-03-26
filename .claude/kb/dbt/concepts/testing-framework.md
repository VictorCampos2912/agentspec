# Testing Framework

> **Purpose**: dbt test types — generic, singular, custom macros, severity, store_failures
> **Confidence**: 0.95
> **MCP Validated**: 2026-03-26

## Overview

dbt testing ensures data quality at the model level. **Generic tests** (unique, not_null, accepted_values, relationships) are declared in YAML. **Singular tests** are standalone SQL files returning failing rows. **Custom generic tests** are Jinja macros that accept parameters. Tests run via `dbt test` or `dbt build`.

## The Concept

```yaml
# models/staging/stripe/_stripe__models.yml
version: 2

models:
  - name: stg_stripe__payments
    description: "Staging model for Stripe payments"
    columns:
      - name: payment_id
        description: "Primary key"
        tests:
          - unique
          - not_null
      - name: payment_status
        tests:
          - accepted_values:
              values: ['succeeded', 'pending', 'failed', 'refunded']
              severity: warn
      - name: order_id
        tests:
          - relationships:
              to: ref('stg_orders')
              field: order_id
```

## Quick Reference

| Test Type | Location | Returns | Use When |
|-----------|----------|---------|----------|
| Generic (built-in) | schema.yml | Failing rows | Standard column checks |
| Singular | tests/*.sql | Failing rows | Complex multi-table logic |
| Custom generic | macros/tests/ | Failing rows | Reusable parameterized checks |
| Source freshness | sources.yml | Stale flag | Monitoring upstream data |

## Common Mistakes

### Wrong

```yaml
# No tests on a model — data quality unknown
models:
  - name: fct_orders
    columns:
      - name: order_id
        # No tests! Duplicates will silently corrupt downstream
```

### Correct

```yaml
models:
  - name: fct_orders
    tests:
      - dbt_utils.recency:
          datepart: hour
          field: updated_at
          interval: 24
    columns:
      - name: order_id
        tests:
          - unique
          - not_null
      - name: order_total
        tests:
          - dbt_utils.accepted_range:
              min_value: 0
              inclusive: true
```

## Related

- [generic-tests](../patterns/generic-tests.md)
- [model-types](../concepts/model-types.md)
