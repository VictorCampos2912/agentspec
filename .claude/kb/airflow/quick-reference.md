# Airflow Quick Reference

> Fast lookup tables. For code examples, see linked files.

## Operator Selection Matrix

| Task Type | Recommended Operator | Notes |
|-----------|---------------------|-------|
| Python function | `@task` (TaskFlow) | Modern default |
| Shell command | `BashOperator` | Simple CLI tasks |
| dbt run/test | `DbtCloudRunJobOperator` | Or BashOperator for dbt Core |
| Spark job | `SparkSubmitOperator` | Or `DatabricksSubmitRunOperator` |
| BigQuery SQL | `BigQueryInsertJobOperator` | Native GCP integration |
| Snowflake SQL | `SnowflakeOperator` | Via snowflake provider |
| Wait for file | `S3KeySensor` (deferrable) | Use deferrable=True |
| Wait for DAG | `ExternalTaskSensor` | Or dataset-driven scheduling |
| HTTP request | `SimpleHttpOperator` | For API calls |

## TaskFlow vs Classic

| Feature | Classic | TaskFlow API |
|---------|---------|-------------|
| Syntax | `PythonOperator(python_callable=fn)` | `@task def fn():` |
| XCom | `ti.xcom_push/pull` | Return value = automatic XCom |
| Dependencies | `task1 >> task2` | Same, or implicit from data flow |
| Dynamic mapping | `expand()` on operator | `fn.expand(arg=list)` |
| Readability | Verbose | Pythonic |

## Schedule Intervals

| Expression | Meaning |
|-----------|---------|
| `@daily` | Midnight UTC |
| `@hourly` | Every hour |
| `0 6 * * 1-5` | 6 AM UTC weekdays |
| `Dataset("s3://bucket/data")` | When upstream dataset updates |
| `Timetable(...)` | Custom schedule logic |

## Orchestrator Comparison

| Feature | Airflow 2.x | Dagster | Prefect 3.x |
|---------|------------|---------|-------------|
| Paradigm | Task-centric DAGs | Asset-centric | Flow-centric |
| Learning curve | Medium | Medium-High | Low |
| UI quality | Functional | Excellent | Good |
| Data lineage | Limited | Built-in | Limited |
| Testing story | Manual | First-class | Good |
| Community size | Largest | Growing fast | Medium |
| Ops burden | High | Medium | Low (Cloud) |
| Best for | Battle-tested reliability | Data lineage focus | Python DX |

## Common Pitfalls

| Don't | Do |
|-------|-----|
| Top-level imports in DAG file | Import inside `@task` functions |
| Hardcode connections | Use `Connection` objects + Variables |
| Monolithic tasks (ETL in one) | One task per logical step |
| Skip retries | `retries=2, retry_delay=timedelta(minutes=5)` |
| Use sensors without deferrable | `deferrable=True` saves worker slots |

## Related Documentation

| Topic | Path |
|-------|------|
| Getting Started | `concepts/dag-design.md` |
| Full Index | `index.md` |
