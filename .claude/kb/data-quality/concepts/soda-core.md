# Soda Core

> **Purpose**: SodaCL check syntax, scan definitions, anomaly detection (RAD), backtesting
> **Confidence**: 0.90
> **MCP Validated**: 2026-03-26

## Overview

Soda Core is an open-source data quality framework that uses **SodaCL** (Soda Checks Language) — a human-readable YAML DSL for defining quality checks. It connects to any SQL warehouse, runs checks as scheduled scans, and supports anomaly detection via RAD (Relative Anomaly Detection) which learns normal patterns from historical data.

## The Concept

```yaml
# checks/orders.yml — SodaCL check definitions
checks for orders:
  # Completeness
  - missing_count(order_id) = 0
  - missing_percent(customer_id) < 1%

  # Uniqueness
  - duplicate_count(order_id) = 0

  # Validity
  - invalid_percent(status) = 0:
      valid values: [pending, completed, cancelled, refunded]
  - min(amount) >= 0

  # Freshness
  - freshness(updated_at) < 2h

  # Volume
  - row_count > 0
  - anomaly detection for row_count  # RAD — learns normal volume

  # Distribution anomaly
  - anomaly detection for avg(amount)

  # Schema
  - schema:
      warn:
        when schema changes:
          - column delete
          - column type change
```

## Quick Reference

| Check Type | Syntax | Example |
|-----------|--------|---------|
| Missing | `missing_count(col)` | `missing_count(email) = 0` |
| Duplicate | `duplicate_count(col)` | `duplicate_count(id) = 0` |
| Valid values | `invalid_percent(col)` | `invalid_percent(status) = 0` |
| Range | `min(col)`, `max(col)` | `min(age) >= 0` |
| Freshness | `freshness(col)` | `freshness(updated_at) < 2h` |
| Volume | `row_count` | `row_count between 1000 and 100000` |
| Anomaly | `anomaly detection` | `anomaly detection for row_count` |
| Custom SQL | `failed_rows` | SQL returning failing rows |

## Common Mistakes

### Wrong

```yaml
# Hardcoded volume threshold — breaks with business growth
checks for orders:
  - row_count between 50000 and 60000  # too rigid
```

### Correct

```yaml
# Anomaly detection learns normal patterns automatically
checks for orders:
  - anomaly detection for row_count:
      severity_level: 3  # standard deviations
```

## Related

- [quality-dimensions](../concepts/quality-dimensions.md)
- [great-expectations](../patterns/great-expectations.md)
