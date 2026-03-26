# Orchestrator Comparison

> **Purpose**: Airflow 2.x vs Dagster vs Prefect 3.x — decision matrix with trade-offs
> **Confidence**: 0.90
> **MCP Validated**: 2026-03-26

## Overview

The three leading Python orchestrators each have a distinct philosophy: **Airflow** is task-centric (schedule tasks on a timeline), **Dagster** is asset-centric (define data assets and their dependencies), and **Prefect** is workflow-centric (decorate Python functions with retry/scheduling). Choosing the right one depends on team size, existing infrastructure, and whether you think in tasks or data assets.

## The Concept

```python
# Same pipeline in three orchestrators

# --- AIRFLOW 2.x ---
from airflow.decorators import dag, task
@dag(schedule="@daily")
def orders_pipeline():
    @task()
    def extract(): ...
    @task()
    def transform(data): ...
    transform(extract())

# --- DAGSTER ---
from dagster import asset
@asset
def raw_orders():
    """Extract orders from API."""
    return extract_from_api()

@asset(deps=[raw_orders])
def clean_orders(raw_orders):
    """Transform raw orders."""
    return transform(raw_orders)

# --- PREFECT 3.x ---
from prefect import flow, task
@task(retries=2)
def extract(): ...
@task
def transform(data): ...

@flow(name="orders-pipeline")
def orders_pipeline():
    data = extract()
    transform(data)
```

## Quick Reference

| Dimension | Airflow 2.x | Dagster | Prefect 3.x |
|-----------|------------|---------|-------------|
| **Mental model** | Tasks on a schedule | Data assets + lineage | Decorated Python functions |
| **Scheduling** | Cron, datasets, sensors | Cron, sensors, freshness policies | Cron, event-driven |
| **Dynamic tasks** | `expand()` / `map()` | `@multi_asset`, `DynamicPartitionsDefinition` | `.map()` on tasks |
| **Testing** | Difficult (needs Airflow context) | First-class (`materialize()` in pytest) | Easy (call functions directly) |
| **UI** | Mature, complex | Modern, asset-focused | Clean, flow-focused |
| **Deployment** | Self-hosted, MWAA, Astronomer | Self-hosted, Dagster Cloud | Self-hosted, Prefect Cloud |
| **dbt integration** | Provider package | `dagster-dbt` (asset-level) | `prefect-dbt` |
| **Learning curve** | Medium-high | Medium | Low |
| **Community size** | Largest | Growing fast | Medium |
| **Best for** | Complex scheduling, legacy migration | Data-asset-centric teams, testing-heavy | Small teams, Python-first |

## Common Mistakes

### Wrong

```text
Choosing Airflow because "everyone uses it" without considering:
- Team already thinks in data assets → Dagster is natural
- Small team, simple pipelines → Prefect is faster to adopt
- Heavy testing requirements → Dagster's materialize() is unmatched
```

### Correct

```text
Decision framework:
1. Team > 10 DE + existing Airflow? → Stay with Airflow, modernize with TaskFlow
2. Asset-centric + strong testing culture? → Dagster
3. Small team + Python-first + fast iteration? → Prefect
4. Regulated industry + audit trail? → Airflow (most mature logging)
```

## Related

- [dag-design](../concepts/dag-design.md)
- [dag-factory](../patterns/dag-factory.md)
