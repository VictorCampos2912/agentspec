# Getting Started with AgentSpec

Get from zero to your first spec-driven data pipeline in 10 minutes.

## Prerequisites

- [Claude Code CLI](https://docs.anthropic.com/en/docs/claude-code) installed
- Git

## Installation

Clone the AgentSpec framework into your project:

```bash
# Clone the repository
git clone https://github.com/luanmorenommaciel/agentspec.git

# Copy the .claude directory into your project
cp -r agentspec/.claude your-project/.claude
```

Or add it directly to an existing project:

```bash
cd your-project
git clone https://github.com/luanmorenommaciel/agentspec.git /tmp/agentspec
cp -r /tmp/agentspec/.claude .claude
```

## Initialize Your Project

The SDD directory structure is already set up:

```text
your-project/.claude/
├── agents/              # 27 specialized agents (ready to use)
├── commands/            # 20 slash commands (ready to use)
├── sdd/
│   ├── features/        # Your active feature documents go here
│   ├── reports/         # Build reports land here
│   └── archive/         # Shipped features archived here
└── kb/                  # 11 data engineering KB domains (ready to use)
```

## Your First Data Pipeline (5 minutes)

Let's build an orders pipeline using the full SDD workflow.

### Step 1: Brainstorm (Optional)

Explore your idea through guided dialogue:

```bash
claude> /brainstorm "Daily orders pipeline from Postgres to Snowflake with star schema"
```

AgentSpec asks targeted questions about source systems, volumes, freshness SLAs, and consumer needs. Output: `BRAINSTORM_ORDERS_PIPELINE.md`

### Step 2: Define Requirements

Capture formal requirements with data contracts:

```bash
claude> /define ORDERS_PIPELINE
```

Output: `DEFINE_ORDERS_PIPELINE.md` with:

- Problem statement and users
- Data contract (schema, SLAs, lineage)
- Source inventory (volumes, freshness)
- Clarity Score (must reach 12/15 to proceed)

### Step 3: Design Architecture

Create the pipeline architecture:

```bash
claude> /design ORDERS_PIPELINE
```

Output: `DESIGN_ORDERS_PIPELINE.md` with:

- Architecture diagram with DAG structure
- Partition strategy and incremental approach
- File manifest with agent assignments (dbt-specialist, pipeline-architect)
- Schema evolution plan
- Data quality gates

### Step 4: Build

Execute the implementation with agent delegation:

```bash
claude> /build ORDERS_PIPELINE
```

AgentSpec delegates dbt models to `dbt-specialist`, DAGs to `pipeline-architect`, and quality checks to `data-quality-analyst`. Verification includes `dbt build`, `sqlfluff lint`, and data quality assertions. Output: `BUILD_REPORT_ORDERS_PIPELINE.md`

### Step 5: Ship

Archive everything with lessons learned:

```bash
claude> /ship ORDERS_PIPELINE
```

## Quick Data Engineering Commands

Don't need the full SDD workflow? Use commands directly:

```bash
# Design a star schema
claude> /schema "Star schema for e-commerce analytics"

# Scaffold an Airflow DAG
claude> /pipeline "Daily orders ETL from Postgres to Snowflake"

# Generate quality checks for a model
claude> /data-quality models/staging/stg_orders.sql

# Review SQL for anti-patterns
claude> /sql-review models/marts/

# Migrate legacy stored procedures
claude> /migrate legacy/etl_orders_proc.sql
```

## What's Next

- [Core Concepts](../concepts/) — understand how phases, agents, and KB work together
- [Tutorials](../tutorials/) — dbt, star schema, data quality, Spark, streaming, RAG walkthroughs
- [Reference](../reference/) — full command, agent, and KB domain catalog

## Troubleshooting

**Commands not recognized?**
Ensure `.claude/commands/` exists in your project root with the slash command files.

**Agent not matching?**
Check that `.claude/agents/` contains the agent `.md` files. Agents are discovered via glob pattern.

**Clarity score too low?**
The `/define` phase requires 12/15 to proceed. For data pipelines, ensure Source Inventory, Schema Contract, and Freshness SLAs are populated.

**KB domain not loading?**
Check `.claude/kb/_index.yaml` — the domain must be registered there. All 11 DE domains come pre-configured.
