# AgentSpec Development

> Spec-Driven Development framework for Data Engineering on Claude Code

---

## Project Context

**What is AgentSpec?** A Claude Code plugin that provides structured AI-assisted development through a 5-phase SDD workflow, specialized for data engineering with 41 agents, 22 commands, and 22 KB domains.

**Current Status:** v2.0.0 — Data engineering pivot complete. Linear is the project tracker (source of truth).

---

## Repository Structure

```text
agentspec/
├── .claude/                 # Claude Code integration
│   ├── agents/              # 41 specialized agents
│   │   ├── workflow/        # 6 SDD phase agents
│   │   ├── code-quality/    # 5 code excellence agents (DE-aware)
│   │   ├── data-engineering/ # 15 DE specialist agents
│   │   ├── cloud/           # 5 cloud platform agents (AWS, Fabric, GCP)
│   │   ├── ai-ml/           # 2 AI/ML agents
│   │   ├── dev/             # 2 dev agents (AgentLoop)
│   │   ├── communication/   # 4 communication agents
│   │   └── exploration/     # 2 codebase agents
│   │
│   ├── commands/            # 22 slash commands
│   │   ├── workflow/        # SDD commands (7)
│   │   ├── data-engineering/ # DE commands (8)
│   │   ├── dev/             # Dev commands (1)
│   │   ├── core/            # Utility commands (4)
│   │   ├── knowledge/       # KB commands (1)
│   │   └── review/          # Review commands (1)
│   │
│   ├── sdd/                 # SDD framework
│   │   ├── architecture/    # WORKFLOW_CONTRACTS.yaml, ARCHITECTURE.md
│   │   ├── templates/       # 5 document templates (DE-aware)
│   │   ├── features/        # Active development
│   │   ├── reports/         # Build reports
│   │   └── archive/         # Shipped features
│   │
│   └── kb/                  # Knowledge Base (22 domains)
│       ├── _templates/      # 7 KB domain templates
│       ├── _index.yaml      # Domain registry
│       ├── dbt/             # dbt patterns and concepts
│       ├── spark/           # PySpark, Spark SQL
│       ├── sql-patterns/    # SQL best practices
│       ├── airflow/         # DAG patterns
│       ├── streaming/       # Flink, Kafka, CDC
│       ├── data-modeling/   # Star schema, Data Vault, SCD
│       ├── data-quality/    # GE, Soda, observability
│       ├── lakehouse/       # Iceberg, Delta, catalogs
│       ├── cloud-platforms/ # Snowflake, Databricks, BigQuery
│       ├── ai-data-engineering/ # RAG, vector DBs, features
│       ├── modern-stack/    # DuckDB, Polars, SQLMesh
│       ├── aws/             # Lambda, S3, Glue, SAM
│       ├── gcp/             # Cloud Run, Pub/Sub, BigQuery
│       ├── microsoft-fabric/ # Lakehouse, Warehouse, Pipelines
│       ├── lakeflow/        # Databricks Lakeflow (DLT)
│       ├── medallion/       # Bronze/Silver/Gold architecture
│       ├── prompt-engineering/ # Chain-of-thought, extraction
│       ├── genai/           # Multi-agent systems, guardrails
│       ├── pydantic/        # Validation, LLM output schemas
│       ├── python/          # Python patterns and idioms
│       ├── testing/         # pytest, fixtures, CI testing
│       └── terraform/       # IaC modules, state, workspaces
│
├── docs/                    # Documentation
│   ├── getting-started/     # Installation and first pipeline
│   ├── concepts/            # SDD pillars through DE lens
│   ├── tutorials/           # dbt, star schema, Spark, streaming tutorials
│   └── reference/           # Full catalog: agents, commands, KB domains
│
├── CHANGELOG.md             # Version history
├── CONTRIBUTING.md          # Contribution guide
├── SECURITY.md              # Security policy
└── README.md                # Project overview
```

---

## Development Workflow

Use AgentSpec's own SDD workflow to develop AgentSpec:

```bash
# Explore an enhancement idea
/brainstorm "Add Judge layer for spec validation"

# Capture requirements
/define JUDGE_LAYER

# Design the architecture
/design JUDGE_LAYER

# Build it
/build JUDGE_LAYER

# Ship when complete
/ship JUDGE_LAYER
```

Data engineering example:

```bash
# Design a star schema
/schema "Star schema for e-commerce analytics"

# Scaffold a pipeline
/pipeline "Daily orders ETL from Postgres to Snowflake"

# Generate quality checks
/data-quality models/staging/stg_orders.sql
```

---

## Active Development Tasks

| Task | Status | Description |
|------|--------|-------------|
| Data engineering pivot | Done | 11 KB domains, 11 DE agents, 8 DE commands |
| Adapt existing agents for DE | Done | code-reviewer, code-cleaner, test-generator, design, define, build |
| Adapt SDD templates for DE | Done | BRAINSTORM, DEFINE, DESIGN, BUILD_REPORT templates |
| Documentation overhaul | Done | Getting started, concepts, tutorials, reference, README |
| Create CLAUDE.md.template | Pending | Template for user projects |
| Implement Judge layer | Planned | Spec validation via external LLM |
| Add telemetry | Planned | Local usage tracking |

---

## Coding Standards

### Markdown Files

- ATX-style headers (`#`, `##`, `###`)
- Fenced code blocks with language identifiers
- Tables properly aligned

### Agent Prompts

- Specific trigger conditions
- Clear capabilities list
- Concrete examples
- Defined output format
- `kb_domains` field for DE agents

### KB Domains

- `index.md` - Domain overview
- `quick-reference.md` - Cheat sheet
- `concepts/` - 3-6 concept files
- `patterns/` - 3-6 pattern files with code examples

---

## Commands Available

### SDD Workflow (7)

| Command | Purpose |
|---------|---------|
| `/brainstorm` | Explore ideas (Phase 0) |
| `/define` | Capture requirements (Phase 1) |
| `/design` | Create architecture (Phase 2) |
| `/build` | Execute implementation (Phase 3) |
| `/ship` | Archive completed work (Phase 4) |
| `/iterate` | Update existing docs (Cross-phase) |
| `/create-pr` | Create pull request |

### Data Engineering (8)

| Command | Purpose |
|---------|---------|
| `/pipeline` | DAG/pipeline scaffolding |
| `/schema` | Interactive schema design |
| `/data-quality` | Quality rules generation |
| `/lakehouse` | Table format + catalog guidance |
| `/sql-review` | SQL-specific code review |
| `/ai-pipeline` | RAG/embedding scaffolding |
| `/data-contract` | Contract authoring (ODCS) |
| `/migrate` | Legacy ETL migration |

### Dev (1)

| Command | Purpose |
|---------|---------|
| `/dev` | AgentLoop — SDD-lite for quick tasks |

### Core & Utilities (6)

| Command | Purpose |
|---------|---------|
| `/create-kb` | Create KB domain |
| `/review` | Code review |
| `/meeting` | Meeting transcript analysis |
| `/memory` | Save session insights |
| `/sync-context` | Update CLAUDE.md |
| `/readme-maker` | Generate README |

---

## Key Files to Know

| File | Purpose |
|------|---------|
| `.claude/sdd/architecture/WORKFLOW_CONTRACTS.yaml` | Phase transition rules |
| `.claude/sdd/templates/*.md` | Document templates (DE-aware) |
| `.claude/kb/_templates/*.template` | KB domain templates |
| `.claude/kb/_index.yaml` | KB domain registry (11 domains) |
| `.claude/agents/data-engineering/README.md` | DE agent routing + escalation map |
| `.claude/agents/cloud/` | AWS, Fabric, GCP cloud platform agents |
| `.claude/agents/ai-ml/` | GenAI architect, prompt specialist |
| `.claude/agents/dev/` | Overnight builder, prompt crafter (AgentLoop) |

---

## Version

- **Version:** 2.1.0
- **Status:** Release
- **Last Updated:** 2026-03-26
