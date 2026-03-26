# Airflow Knowledge Base

> **Purpose**: Orchestration patterns — Airflow 2.x TaskFlow, Dagster, Prefect comparison, DAG design
> **MCP Validated**: 2026-03-26

## Quick Navigation

### Concepts (< 150 lines each)

| File | Purpose |
|------|---------|
| [concepts/dag-design.md](concepts/dag-design.md) | Idempotency, atomicity, retries, SLAs |
| [concepts/operators.md](concepts/operators.md) | Operator catalog, TaskFlow @task, deferrable |
| [concepts/task-dependencies.md](concepts/task-dependencies.md) | Trigger rules, XCom, dynamic task mapping |
| [concepts/orchestrator-comparison.md](concepts/orchestrator-comparison.md) | Airflow vs Dagster vs Prefect 3.x |

### Patterns (< 200 lines each)

| File | Purpose |
|------|---------|
| [patterns/dag-factory.md](patterns/dag-factory.md) | Parameterized DAGs, YAML-driven generation |
| [patterns/sensors-triggers.md](patterns/sensors-triggers.md) | Deferrable operators, external sensors |
| [patterns/dynamic-task-mapping.md](patterns/dynamic-task-mapping.md) | expand(), map(), fan-out/fan-in |
| [patterns/error-handling.md](patterns/error-handling.md) | Retries, SLA callbacks, alerting |

---

## Quick Reference

- [quick-reference.md](quick-reference.md) - Fast lookup tables

---

## Key Concepts

| Concept | Description |
|---------|-------------|
| **DAG Design** | Idempotent, atomic tasks with retry and SLA configuration |
| **TaskFlow API** | Modern @task decorators replacing classic operators |
| **Dynamic Mapping** | expand()/map() for runtime-determined task parallelism |
| **Orchestrator Choice** | Airflow (battle-tested) vs Dagster (asset-centric) vs Prefect (DX-first) |

---

## Learning Path

| Level | Files |
|-------|-------|
| **Beginner** | concepts/dag-design.md |
| **Intermediate** | patterns/dynamic-task-mapping.md |
| **Advanced** | concepts/orchestrator-comparison.md |

---

## Agent Usage

| Agent | Primary Files | Use Case |
|-------|---------------|----------|
| pipeline-architect | All files | DAG creation, orchestration design |
| build-agent | patterns/error-handling.md | Pipeline verification |
