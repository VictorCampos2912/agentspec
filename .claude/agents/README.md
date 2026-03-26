# AgentSpec Agents

AgentSpec includes **41 agents** organized by 8 categories, all following the **KB-First architecture** with confidence scoring.

## Core Principle: KB-First Resolution

Every agent follows this mandatory resolution order:

```text
1. KB CHECK (local, instant, zero tokens)
   └─ Read: .claude/kb/{domain}/ → Project-specific patterns

2. CONFIDENCE ASSIGNMENT
   └─ Calculate score based on evidence quality

3. MCP FALLBACK (only if KB insufficient)
   └─ Query external sources for validation
```

## Categories

### Workflow Agents (6)

Drive the SDD workflow phases:

| Agent | Purpose | Phase |
|-------|---------|-------|
| `brainstorm-agent` | Explore ideas through collaborative dialogue | 0 |
| `define-agent` | Capture requirements with clarity scoring | 1 |
| `design-agent` | Create technical architecture with file manifest | 2 |
| `build-agent` | Execute implementation with agent delegation | 3 |
| `ship-agent` | Archive with lessons learned | 4 |
| `iterate-agent` | Update documents with cascade awareness | All |

### Code Quality Agents (5)

Ensure code excellence:

| Agent | Purpose |
|-------|---------|
| `code-reviewer` | Review code for quality and security issues |
| `code-cleaner` | Clean code, remove redundant comments, apply DRY |
| `code-documenter` | Generate documentation, READMEs, API docs |
| `test-generator` | Generate pytest tests with fixtures |
| `python-developer` | Python code architecture, dataclasses, type hints |

### Communication Agents (4)

Bridge technical and business:

| Agent | Purpose |
|-------|---------|
| `adaptive-explainer` | Explain concepts at any audience level |
| `linear-project-manager` | Linear project management, sprint planning, issue tracking |
| `meeting-analyst` | Extract decisions and action items from meetings |
| `the-planner` | Strategic architecture and roadmap planning |

### Data Engineering Agents (15)

End-to-end data engineering from ingestion to governance:

| Agent | Purpose |
|-------|---------|
| `spark-engineer` | PySpark, Spark SQL, distributed processing |
| `spark-troubleshooter` | Spark debugging — OOM, data skew, shuffle failures |
| `spark-performance-analyzer` | Spark tuning — memory, partitions, joins, AQE |
| `pipeline-architect` | Airflow, Dagster, DAG design patterns |
| `dbt-specialist` | dbt models, macros, tests, incremental strategies |
| `data-quality-analyst` | Great Expectations, dbt tests, data contracts |
| `schema-designer` | Dimensional modeling, SCD, Data Vault |
| `data-platform-engineer` | Snowflake, Databricks, BigQuery, cost optimization |
| `lakehouse-architect` | Iceberg, Delta Lake, catalog governance |
| `lakeflow-specialist` | Databricks Lakeflow (DLT), materialized views, expectations |
| `medallion-architect` | Bronze/Silver/Gold layer design, quality progression |
| `streaming-engineer` | Flink, Kafka, Spark Streaming, CDC |
| `ai-data-engineer` | RAG pipelines, vector DBs, feature stores |
| `sql-optimizer` | Query plans, cross-dialect SQL, window functions |
| `data-contracts-engineer` | ODCS, SLAs, schema governance |

See [data-engineering/README.md](data-engineering/README.md) for routing guide and escalation map.

### Cloud Platform Agents (5)

Cloud-specific data architecture:

| Agent | Purpose |
|-------|---------|
| `aws-data-architect` | Lambda, S3, Glue, Redshift, MWAA, serverless pipelines |
| `aws-deployer` | SAM, CloudFormation, CI/CD, Terraform for AWS |
| `fabric-architect` | Microsoft Fabric — Lakehouse, Warehouse, OneLake, Pipelines |
| `fabric-pipeline-developer` | Fabric Data Factory, PySpark notebooks, Dataflow Gen2 |
| `gcp-data-architect` | BigQuery, Cloud Run, Pub/Sub, Dataflow, Vertex AI |

See [cloud/](cloud/) for cloud platform agents.

### AI/ML Agents (2)

AI system design and prompt engineering:

| Agent | Purpose |
|-------|---------|
| `genai-architect` | Multi-agent systems, state machines, guardrails, RAG design |
| `ai-prompt-specialist` | Prompt optimization, structured extraction, few-shot |

### Dev Agents (2)

Accelerated development:

| Agent | Purpose |
|-------|---------|
| `overnight-builder` | Autonomous overnight execution via `claude -p` + crontab |
| `prompt-crafter` | SDD-lite PROMPT.md builder with agent matching |

### Exploration Agents (2)

Navigate and understand codebases:

| Agent | Purpose |
|-------|---------|
| `codebase-explorer` | Analyze codebase structure with health scoring |
| `kb-architect` | Build and audit knowledge base domains |

## Escalation Map

```text
Workflow ←→ Data Engineering:
  build-agent → dbt-specialist, spark-engineer, pipeline-architect (DE delegation)
  design-agent → schema-designer (data modeling), pipeline-architect (DAG design)
  define-agent → data-contracts-engineer (SLAs), data-quality-analyst (metrics)

Code Quality ←→ Data Engineering:
  code-reviewer → sql-optimizer (SQL anti-patterns), data-quality-analyst (PII)
  code-cleaner → dbt-specialist (CTE refactoring), sql-optimizer (query cleanup)
  test-generator → data-quality-analyst (GE suites), dbt-specialist (dbt tests)
  python-developer → spark-engineer (PySpark code), dbt-specialist (Python models)

Data Engineering ←→ Data Engineering:
  dbt-specialist ←→ spark-engineer (SQL vs PySpark)
  dbt-specialist ←→ schema-designer (modeling layer)
  pipeline-architect ←→ streaming-engineer (batch vs stream)
  lakehouse-architect ←→ data-platform-engineer (infra decisions)
  lakeflow-specialist ←→ lakehouse-architect (DLT vs generic Delta)
  medallion-architect ←→ schema-designer (layer modeling)
  spark-troubleshooter ←→ spark-performance-analyzer (debug vs optimize)
  ai-data-engineer ←→ streaming-engineer (real-time embeddings)
  data-contracts-engineer ←→ data-quality-analyst (enforcement)

Cloud ←→ Data Engineering:
  aws-data-architect → pipeline-architect (MWAA), spark-engineer (Glue)
  gcp-data-architect → pipeline-architect (Composer), spark-engineer (Dataproc)
  fabric-architect → medallion-architect (layer design), lakehouse-architect (Delta)
  fabric-pipeline-developer → spark-engineer (notebooks), dbt-specialist (models)

AI/ML ←→ Data Engineering:
  genai-architect → ai-data-engineer (RAG data layer), streaming-engineer (real-time)
  ai-prompt-specialist → ai-data-engineer (extraction pipelines)

Dev ←→ All:
  prompt-crafter → any agent (agent matching engine)
  overnight-builder → build-agent (execution), prompt-crafter (PROMPT generation)
```

## Creating Custom Agents

Use `_template.md` as the definitive template. Key sections:

1. **Frontmatter** - name, description with examples, tools, kb_domains
2. **Identity Block** - Identity, Domain, Threshold
3. **Knowledge Architecture** - KB-First resolution order (mandatory)
4. **Capabilities** - What the agent can do
5. **Quality Gate** - Pre-flight checklist
6. **Response Format** - Consistent output with confidence scores
7. **Remember** - Mission and core principle

## Creating Domain-Specific Agents

Use `_template.md` to create agents for your specific domain (e.g., AWS, GCP, data engineering, ML/AI). Place them in a new category directory under `agents/`.
