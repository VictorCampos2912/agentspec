> **MCP Validated:** 2026-02-17

# Microsoft Fabric Quick Reference

> Fast lookup tables. For code examples, see linked files.

## Workload Selection

| Use Case | Workload | Why |
|----------|----------|-----|
| Raw ingestion + Spark | Lakehouse | Delta tables, PySpark, notebooks |
| Structured analytics + T-SQL | Warehouse | Full DML, RLS, data masking |
| Real-time streaming | Eventhouse | KQL, time-series, sub-second |
| ETL/ELT orchestration | Data Factory | Copy Activity, Dataflows Gen2 |
| Dashboards + reports | Power BI | DirectLake, semantic models |
| ML + AI workflows | Data Science | MLflow, Copilot, AI Skills |

## Capacity SKUs

| SKU | CUs | Max Memory | Best For |
|-----|-----|------------|----------|
| F2 | 2 | 3 GB | Dev/test |
| F16 | 16 | 25 GB | Small team |
| F64 | 64 | 100 GB | Department |
| F128 | 128 | 200 GB | Enterprise |
| F256+ | 256+ | 400 GB+ | Large-scale |

## Common API Endpoints

| Operation | Method | Endpoint |
|-----------|--------|----------|
| List workspaces | GET | `/v1/workspaces` |
| Create lakehouse | POST | `/v1/workspaces/{id}/lakehouses` |
| Run pipeline | POST | `/v1/workspaces/{id}/items/{id}/jobs/instances?jobType=Pipeline` |
| Deploy stage | POST | `/v1/deploymentPipelines/{id}/deploy` |
| Git connect | POST | `/v1/workspaces/{id}/git/connect` |

## Decision Matrix

| Use Case | Choose |
|----------|--------|
| Petabyte-scale raw data | Lakehouse (Delta + Spark) |
| Complex joins + stored procs | Warehouse (T-SQL) |
| Sub-second event analytics | Eventhouse (KQL) |
| No-code data movement | Copy Activity (Data Factory) |
| Code-first transforms | Spark Notebooks |
| Row-level access control | Warehouse RLS + security policies |
| Automated deployments | Deployment Pipelines + Git integration |

## Common Pitfalls

| Don't | Do |
|-------|-----|
| Use V-Order on streaming tables | Use V-Order on batch Delta tables |
| Skip Git integration for prod | Connect workspace to Azure DevOps/GitHub |
| Grant broad workspace roles | Use item-level permissions + RLS |
| Run heavy Spark on F2 | Scale to F16+ for production Spark |
| Ignore capacity throttling | Monitor CU usage via Capacity Metrics app |
| Mix bronze/silver/gold in one lakehouse | Separate lakehouses per medallion layer |

## Related Documentation

| Topic | Path |
|-------|------|
| Lakehouse Architecture | `02-data-engineering/concepts/lakehouse.md` |
| Warehouse Basics | `04-data-warehouse/concepts/warehouse-basics.md` |
| Medallion Pattern | `03-architecture-patterns/patterns/medallion-fabric.md` |
| Full Index | `index.md` |
