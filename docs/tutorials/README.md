# Tutorials

Step-by-step walkthroughs for common AgentSpec workflows.

## Available Tutorials

### Data Engineering Workflows

| Tutorial | Time | Level |
|----------|------|-------|
| [Your First Data Pipeline](../getting-started/) | 10 min | Beginner |
| Build a dbt Staging + Mart Layer | 15 min | Coming soon |
| Design a Star Schema with `/schema` | 10 min | Coming soon |
| Add Data Quality with Great Expectations | 10 min | Coming soon |
| Build a PySpark Processing Job | 15 min | Coming soon |
| Create a Kafka Streaming Pipeline | 15 min | Coming soon |
| Build a RAG Pipeline with `/ai-pipeline` | 15 min | Coming soon |

### SDD Workflow Basics

| Tutorial | Time | Level |
|----------|------|-------|
| Using `/iterate` When Requirements Change | 5 min | Coming soon |
| Building with Agent Delegation | 15 min | Coming soon |
| Creating a Knowledge Base Domain | 10 min | Coming soon |

### Common Data Engineering Workflows

#### Quick Schema Design

Design a star schema without the full SDD workflow:

```bash
# Interactive schema design
claude> /schema "Star schema for e-commerce: orders, customers, products"
```

The `schema-designer` agent creates dimension/fact tables, grain definitions, and DDL for your target platform.

#### Quick Pipeline Scaffold

Scaffold an Airflow DAG:

```bash
# Pipeline scaffolding
claude> /pipeline "Daily orders ETL: Postgres → staging → dbt → Snowflake marts"
```

The `pipeline-architect` creates DAG code with task dependencies, sensors, and error handling.

#### Data Quality Rules Generation

Generate quality checks for existing models:

```bash
# Point at a model file
claude> /data-quality models/staging/stg_orders.sql

# Or describe what you need
claude> /data-quality "Quality checks for customer dimension with SCD Type 2"
```

The `data-quality-analyst` generates Great Expectations suites and/or dbt test YAML.

#### SQL Review for Anti-Patterns

Review SQL code for common issues:

```bash
# Review a directory of models
claude> /sql-review models/marts/

# Review specific files
claude> /sql-review models/staging/stg_orders.sql
```

The `code-reviewer` (with DE capability) + `sql-optimizer` check for `SELECT *`, missing partition filters, PII exposure, implicit coercion, and more.

#### Legacy ETL Migration

Migrate stored procedures to dbt:

```bash
# Point at legacy SQL
claude> /migrate legacy/sp_daily_orders.sql

# Or describe the migration
claude> /migrate "Convert Informatica workflows to Airflow + dbt"
```

#### Data Contract Authoring

Create producer-consumer agreements:

```bash
# Create a contract
claude> /data-contract "Contract between orders team and analytics for mart_revenue"
```

### Full SDD Workflow Examples

#### Quick Feature with `/define` + `/build`

Skip brainstorm when requirements are already clear:

```bash
# Start directly with requirements
claude> /define "Add incremental refresh to stg_orders model"

# Review the DEFINE document, then design
claude> /design STG_ORDERS_INCREMENTAL

# Build it
claude> /build STG_ORDERS_INCREMENTAL

# Ship when done
claude> /ship STG_ORDERS_INCREMENTAL
```

#### Updating Requirements Mid-Stream

When a stakeholder changes scope after you've started design:

```bash
# Update the DEFINE document with cascade detection
claude> /iterate .claude/sdd/features/DEFINE_ORDERS_PIPELINE.md "Add support for late-arriving data with 3-day lookback"
```

The iterate-agent detects which downstream documents (DESIGN, BUILD_REPORT) need updates and guides you through each cascade.

### Code Quality Workflows

#### Code Review with Dual AI

```bash
# Review uncommitted changes
claude> /review

# Review against a specific branch
claude> /review --base main

# Quick review (CodeRabbit only, faster feedback)
claude> /review --quick
```

## What's Next

- [Core Concepts](../concepts/) — understand the mental model
- [Reference](../reference/) — full command, agent, and KB domain catalog
