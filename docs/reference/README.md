# Reference

Complete catalog of commands, agents, KB domains, templates, and configuration.

## Slash Commands (20 total)

### Workflow Commands (7)

| Command | Purpose | Input | Output |
|---------|---------|-------|--------|
| `/brainstorm` | Explore ideas (Phase 0) | Idea description or file path | `BRAINSTORM_{FEATURE}.md` |
| `/define` | Capture requirements (Phase 1) | Brainstorm file, notes, or description | `DEFINE_{FEATURE}.md` |
| `/design` | Create architecture (Phase 2) | DEFINE file path | `DESIGN_{FEATURE}.md` |
| `/build` | Execute implementation (Phase 3) | DESIGN file path | `BUILD_REPORT_{FEATURE}.md` |
| `/ship` | Archive completed work (Phase 4) | DEFINE file path | `SHIPPED_{DATE}.md` |
| `/iterate` | Update any phase document | File path + change description | Updated document + cascades |
| `/create-pr` | Create pull request | Optional title, `--draft`, `--review` | GitHub PR |

### Data Engineering Commands (8)

| Command | Purpose | Primary Agent |
|---------|---------|---------------|
| `/pipeline` | DAG/pipeline scaffolding | pipeline-architect |
| `/schema` | Interactive schema design | schema-designer |
| `/data-quality` | Quality rules generation | data-quality-analyst |
| `/lakehouse` | Table format + catalog guidance | lakehouse-architect |
| `/sql-review` | SQL-specific code review | code-reviewer + sql-optimizer |
| `/ai-pipeline` | RAG/embedding scaffolding | ai-data-engineer |
| `/data-contract` | Contract authoring (ODCS) | data-contracts-engineer |
| `/migrate` | Legacy ETL migration | dbt-specialist + spark-engineer |

### Core Commands (3)

| Command | Purpose | Input |
|---------|---------|-------|
| `/memory` | Save session insights to storage | Optional context note |
| `/sync-context` | Update CLAUDE.md from codebase | `--section`, `--dry-run` |
| `/readme-maker` | Generate README.md | `--output`, `--style` |

### Knowledge Commands (1)

| Command | Purpose | Input |
|---------|---------|-------|
| `/create-kb` | Create a KB domain | Domain name |

### Review Commands (1)

| Command | Purpose | Input |
|---------|---------|-------|
| `/review` | Dual AI code review | `uncommitted`, `committed`, `--base`, `--deep` |

## Agents (27 total)

### Workflow Agents (6)

| Agent | Trigger | Model |
|-------|---------|-------|
| brainstorm-agent | Raw ideas, vague requirements | Opus |
| define-agent | Requirements capture, data contracts | Opus |
| design-agent | Architecture planning, pipeline design | Opus |
| build-agent | Implementation execution, DE delegation | Sonnet |
| ship-agent | Feature archival | Haiku |
| iterate-agent | Mid-stream changes | Sonnet |

### Code Quality Agents (4)

| Agent | Trigger | DE Capabilities |
|-------|---------|----------------|
| code-reviewer | Code review requests | SQL anti-patterns, PII detection, partition checks, dbt test coverage |
| code-cleaner | Refactoring, DRY cleanup | CTE refactoring, SELECT * expansion, Jinja whitespace cleanup |
| code-documenter | README, API docs, docstrings | — |
| test-generator | Test writing, coverage gaps | Great Expectations suites, dbt tests, row count/type validation |

### Data Engineering Agents (11)

| Agent | Trigger | Model | Primary KB |
|-------|---------|-------|-----------|
| spark-engineer | PySpark jobs, Spark SQL | Sonnet | spark |
| pipeline-architect | Airflow/Dagster DAGs | Sonnet | airflow |
| dbt-specialist | dbt models, macros, tests | Sonnet | dbt |
| data-quality-analyst | Quality checks, GE suites | Sonnet | data-quality |
| schema-designer | Star schema, SCD, Data Vault | Sonnet | data-modeling |
| data-platform-engineer | Platform selection, cost optimization | Sonnet | cloud-platforms |
| lakehouse-architect | Iceberg, Delta, catalogs | Sonnet | lakehouse |
| streaming-engineer | Flink, Kafka, Spark Streaming, CDC | Sonnet | streaming |
| ai-data-engineer | RAG, embeddings, vector DBs, features | Opus | ai-data-engineering |
| sql-optimizer | Query plans, cross-dialect SQL | Sonnet | sql-patterns |
| data-contracts-engineer | ODCS, SLAs, schema governance | Sonnet | data-quality |

### Communication Agents (4)

| Agent | Trigger | Model |
|-------|---------|-------|
| adaptive-explainer | Technical explanations | Sonnet |
| linear-project-manager | Linear issue/project management | Opus |
| meeting-analyst | Meeting transcript analysis | Sonnet |
| the-planner | Strategic planning, roadmaps | Opus |

### Exploration Agents (2)

| Agent | Trigger | Model |
|-------|---------|-------|
| codebase-explorer | Codebase navigation | Sonnet |
| kb-architect | KB domain creation/audit | Sonnet |

## Knowledge Base Domains (11)

| Domain | Concepts | Patterns | Topics |
|--------|----------|----------|--------|
| `dbt` | 5 | 5 | Models, macros, incremental, testing, packages |
| `spark` | 4 | 4 | PySpark, Spark SQL, tuning, structured streaming |
| `sql-patterns` | 3 | 4 | Window functions, CTEs, anti-patterns, optimization |
| `airflow` | 4 | 4 | DAGs, operators, sensors, dynamic task mapping |
| `streaming` | 4 | 4 | Flink SQL, Kafka, Spark Streaming, CDC |
| `data-modeling` | 4 | 4 | Dimensional modeling, SCD, normalization, schema evolution |
| `data-quality` | 4 | 4 | Great Expectations, Soda, observability, data contracts |
| `lakehouse` | 4 | 4 | Iceberg v3, Delta Lake, catalog wars, DuckLake |
| `cloud-platforms` | 4 | 4 | Snowflake Cortex, Databricks Lakeflow, BigQuery AI |
| `ai-data-engineering` | 5 | 5 | RAG, vector DBs, feature stores, LLMOps, embeddings |
| `modern-stack` | 4 | 4 | DuckDB, Polars, SQLMesh, local-first analytics |

## SDD Templates

All templates live in `.claude/sdd/templates/`:

| Template | Phase | Purpose | DE Sections |
|----------|-------|---------|-------------|
| `BRAINSTORM_TEMPLATE.md` | Phase 0 | Idea exploration | Data Engineering Context (sources, volumes, flow) |
| `DEFINE_TEMPLATE.md` | Phase 1 | Requirements with clarity score | Data Contract (schema, SLAs, lineage) |
| `DESIGN_TEMPLATE.md` | Phase 2 | Architecture with file manifest | Pipeline Architecture (DAG, partitions, incremental, quality gates) |
| `BUILD_REPORT_TEMPLATE.md` | Phase 3 | Execution report with agent attribution | Data Quality Results (dbt build, sqlfluff, GE, freshness) |
| `SHIPPED_TEMPLATE.md` | Phase 4 | Archive with lessons learned | — |

## KB Templates

All templates live in `.claude/kb/_templates/`:

| Template | Purpose |
|----------|---------|
| `index.md.template` | Domain overview and navigation |
| `quick-reference.md.template` | Cheat sheet (max 100 lines) |
| `concept.md.template` | Core concept explanation (max 150) |
| `pattern.md.template` | Implementation pattern (max 200) |
| `domain-manifest.yaml.template` | Domain metadata and file registry |
| `spec.yaml.template` | Machine-readable specifications |
| `test-case.json.template` | Validation test cases |

## Configuration

### Workflow Contracts

Phase transition rules are defined in `.claude/sdd/architecture/WORKFLOW_CONTRACTS.yaml`:

- Phase inputs, outputs, and quality gates
- Model allocation per phase (Opus/Sonnet/Haiku)
- Naming conventions (`SCREAMING_SNAKE_CASE`)
- Cascade rules for `/iterate`
- Status transition logic
- Data engineering delegation map and quality gates
- Data anti-patterns checklist

### Settings

Project settings in `.claude/settings.local.json` (auto-generated, gitignored — personal to each developer):

- `permissions` — tool access control
- `outputStyle` — response formatting (e.g., "Explanatory")

## File Naming Conventions

| Artifact | Pattern | Location |
|----------|---------|----------|
| Brainstorm document | `BRAINSTORM_{FEATURE}.md` | `.claude/sdd/features/` |
| Requirements document | `DEFINE_{FEATURE}.md` | `.claude/sdd/features/` |
| Design document | `DESIGN_{FEATURE}.md` | `.claude/sdd/features/` |
| Build report | `BUILD_REPORT_{FEATURE}.md` | `.claude/sdd/reports/` |
| Shipped archive | `SHIPPED_{YYYY-MM-DD}.md` | `.claude/sdd/archive/{FEATURE}/` |

Feature names use `SCREAMING_SNAKE_CASE` (e.g., `ORDERS_PIPELINE`, `STAR_SCHEMA`).
