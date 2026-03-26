# Task Dependencies

> **Purpose**: Trigger rules, XCom, dynamic task mapping, dataset-driven scheduling
> **Confidence**: 0.95
> **MCP Validated**: 2026-03-26

## Overview

Airflow tasks form a DAG through explicit dependencies (`>>` operator or TaskFlow return values). Beyond simple sequencing, Airflow supports trigger rules (run even if upstream fails), XCom for inter-task data passing, dynamic task mapping for runtime-determined parallelism, and dataset-driven scheduling where DAGs trigger on data updates rather than time.

## The Concept

```python
from airflow.decorators import dag, task
from airflow import Dataset
from airflow.utils.trigger_rule import TriggerRule
from datetime import datetime

# Dataset-driven scheduling: this DAG runs when orders dataset updates
orders_dataset = Dataset("s3://lake/silver/orders")

@dag(schedule=[orders_dataset], start_date=datetime(2026, 1, 1))
def downstream_analytics():

    @task()
    def build_metrics() -> dict:
        result = {"revenue": 150000, "orders": 1200}
        return result  # automatically pushed to XCom

    @task()
    def send_report(metrics: dict):
        # XCom pulled automatically via TaskFlow
        print(f"Revenue: ${metrics['revenue']}")

    @task(trigger_rule=TriggerRule.ALL_DONE)
    def cleanup():
        """Runs regardless of upstream success/failure."""
        pass

    metrics = build_metrics()
    send_report(metrics) >> cleanup()

downstream_analytics()
```

## Quick Reference

| Trigger Rule | Behavior | Use Case |
|-------------|----------|----------|
| `all_success` (default) | All parents succeeded | Normal flow |
| `all_failed` | All parents failed | Error-only branch |
| `all_done` | All parents finished (any state) | Cleanup tasks |
| `one_success` | At least one parent succeeded | Branch convergence |
| `none_failed` | No parent failed (skipped OK) | After branching |
| `none_skipped` | No parent skipped | Ensure all branches ran |

| Feature | Mechanism | Limit |
|---------|-----------|-------|
| XCom | Return value from `@task` | < 48KB (use S3/GCS for large data) |
| XCom Backend | Custom backend (S3, GCS) | Unlimited |
| Dataset scheduling | `schedule=[Dataset(...)]` | Multiple datasets = AND logic |

## Common Mistakes

### Wrong

```python
# Passing large data through XCom — clogs metadata DB
@task()
def extract() -> pd.DataFrame:
    return huge_dataframe  # serialized to metadata DB!
```

### Correct

```python
# Pass a reference, not the data itself
@task()
def extract() -> str:
    path = "s3://lake/staging/extract_20260326.parquet"
    df.to_parquet(path)
    return path  # lightweight reference via XCom

@task()
def transform(path: str):
    df = pd.read_parquet(path)
```

## Related

- [dynamic-task-mapping](../patterns/dynamic-task-mapping.md)
- [sensors-triggers](../patterns/sensors-triggers.md)
