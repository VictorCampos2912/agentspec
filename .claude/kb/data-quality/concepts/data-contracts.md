# Data Contracts

> **Purpose**: ODCS format, Soda Data Contracts, dbt contracts — enforcement and ownership
> **Confidence**: 0.95
> **MCP Validated**: 2026-03-26

## Overview

Data contracts are formal agreements between data producers and consumers that define schema, quality SLAs, and ownership. The **Open Data Contract Standard (ODCS)** provides a vendor-neutral YAML format. dbt contracts enforce column-level types at build time. Soda contracts validate at runtime. Contracts shift quality left — catching issues before downstream impact.

## The Concept

```yaml
# Open Data Contract Standard (ODCS) v3
apiVersion: v3.0.0
kind: DataContract
metadata:
  name: orders
  version: 1.2.0
  owner: data-engineering@company.com
  domain: commerce

schema:
  type: object
  properties:
    order_id:
      type: string
      format: uuid
      required: true
      unique: true
    customer_id:
      type: string
      required: true
    amount:
      type: number
      minimum: 0
    order_date:
      type: string
      format: date
    status:
      type: string
      enum: [pending, completed, cancelled, refunded]

quality:
  freshness:
    maxStaleness: PT1H  # ISO 8601: 1 hour
  completeness:
    threshold: 0.99
  volume:
    minRows: 1000
    maxGrowthRate: 3.0  # alert if 3x normal volume

sla:
  availability: 99.9%
  latency: PT30M
```

## Quick Reference

| Framework | Contract Type | Enforcement Point | Format |
|-----------|--------------|-------------------|--------|
| ODCS | Schema + SLA | CI/CD pipeline | YAML |
| dbt contracts | Column types | `dbt build` | YAML (schema.yml) |
| Soda | Runtime checks | Scan execution | SodaCL YAML |
| Protobuf/Avro | Wire schema | Serialization | `.proto`/`.avsc` |

## Common Mistakes

### Wrong

```yaml
# Contract with no ownership — who do you call when it breaks?
schema:
  columns:
    - name: order_id
      type: string
# Missing: owner, version, SLA, quality thresholds
```

### Correct

```yaml
# Contract with full ownership and SLA
metadata:
  owner: data-engineering@company.com
  version: 1.2.0
  slack_channel: "#data-orders"
quality:
  freshness:
    maxStaleness: PT1H
sla:
  availability: 99.9%
```

## Related

- [data-contract-authoring](../patterns/data-contract-authoring.md)
- [quality-dimensions](../concepts/quality-dimensions.md)
