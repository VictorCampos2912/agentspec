# Data Engineering Agents

> **11 specialized agents** for end-to-end data engineering workflows

---

## Agent Catalog

| # | Agent | Color | Model | Threshold | Primary Domain |
|---|-------|-------|-------|-----------|----------------|
| 1 | [spark-engineer](spark-engineer.md) | red | sonnet | 0.90 | PySpark, Spark SQL, performance tuning |
| 2 | [pipeline-architect](pipeline-architect.md) | blue | sonnet | 0.90 | Airflow, Dagster, DAG design |
| 3 | [dbt-specialist](dbt-specialist.md) | orange | sonnet | 0.90 | dbt models, macros, tests, incremental |
| 4 | [data-quality-analyst](data-quality-analyst.md) | green | sonnet | 0.95 | GE suites, dbt tests, data contracts |
| 5 | [schema-designer](schema-designer.md) | purple | sonnet | 0.95 | Dimensional modeling, SCD, Data Vault |
| 6 | [data-platform-engineer](data-platform-engineer.md) | yellow | sonnet | 0.90 | Platform selection, cost optimization |
| 7 | [lakehouse-architect](lakehouse-architect.md) | blue | sonnet | 0.90 | Iceberg, Delta, catalog governance |
| 8 | [streaming-engineer](streaming-engineer.md) | red | sonnet | 0.90 | Flink, Kafka, Spark Streaming, CDC |
| 9 | [ai-data-engineer](ai-data-engineer.md) | purple | opus | 0.90 | RAG, embeddings, vector DBs, features |
| 10 | [sql-optimizer](sql-optimizer.md) | orange | sonnet | 0.90 | Query plans, cross-dialect, window fns |
| 11 | [data-contracts-engineer](data-contracts-engineer.md) | green | sonnet | 0.95 | ODCS, SLAs, schema governance |

---

## Routing Guide

**"I need to..."**

| Task | Primary Agent | May Escalate To |
|------|--------------|-----------------|
| Write a PySpark job | spark-engineer | pipeline-architect (orchestration) |
| Create an Airflow DAG | pipeline-architect | spark-engineer (job code) |
| Build a dbt model | dbt-specialist | schema-designer (modeling) |
| Add data quality checks | data-quality-analyst | dbt-specialist (dbt tests) |
| Design a star schema | schema-designer | dbt-specialist (implementation) |
| Choose Snowflake vs Databricks | data-platform-engineer | lakehouse-architect (format) |
| Set up Iceberg tables | lakehouse-architect | data-platform-engineer (infra) |
| Build a Kafka pipeline | streaming-engineer | pipeline-architect (batch fallback) |
| Create a RAG pipeline | ai-data-engineer | streaming-engineer (real-time) |
| Optimize a slow query | sql-optimizer | schema-designer (index strategy) |
| Author a data contract | data-contracts-engineer | data-quality-analyst (enforcement) |

---

## Escalation Map

```
dbt-specialist ←→ spark-engineer        (SQL vs PySpark)
               ←→ schema-designer       (modeling layer)
               ←→ data-quality-analyst   (test generation)
               ←→ sql-optimizer          (dbt model SQL)

pipeline-architect ←→ streaming-engineer     (batch vs stream)

lakehouse-architect ←→ data-platform-engineer (infra decisions)

ai-data-engineer ←→ streaming-engineer       (real-time embeddings)

data-contracts-engineer ←→ data-quality-analyst (enforcement)

sql-optimizer ←→ dbt-specialist              (dbt model SQL)
```

---

## Knowledge Base Dependencies

Each agent loads KB domains lazily (just-in-time). Here's what each agent reads:

| Agent | Primary KB | Secondary KB |
|-------|-----------|-------------|
| spark-engineer | spark | sql-patterns, streaming |
| pipeline-architect | airflow | data-quality, dbt |
| dbt-specialist | dbt | data-quality, sql-patterns |
| data-quality-analyst | data-quality | dbt, data-modeling |
| schema-designer | data-modeling | sql-patterns, data-quality |
| data-platform-engineer | lakehouse | cloud-platforms, data-modeling |
| lakehouse-architect | lakehouse | spark, data-modeling |
| streaming-engineer | streaming | spark, sql-patterns |
| ai-data-engineer | ai-data-engineering | data-quality, streaming |
| sql-optimizer | sql-patterns | data-modeling, dbt |
| data-contracts-engineer | data-quality | data-modeling |

---

## Threshold Reference

| Tier | Threshold | Agents | Meaning |
|------|-----------|--------|---------|
| IMPORTANT | 0.95 | data-quality-analyst, schema-designer, data-contracts-engineer | Mistakes are expensive — ASK when below threshold |
| STANDARD | 0.90 | All others | PROCEED with caveat when below threshold |
