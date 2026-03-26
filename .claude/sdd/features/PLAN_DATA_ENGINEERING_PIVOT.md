# PLAN: Data Engineering Pivot

> Comprehensive implementation plan for pivoting AgentSpec from a generic SDD framework to a data engineering-focused SDD framework

## Metadata

| Attribute | Value |
|-----------|-------|
| **Plan** | DATA_ENGINEERING_PIVOT |
| **Date** | 2026-03-26 |
| **Author** | the-planner |
| **Status** | In Progress (v2) |
| **Phases** | 6 (0-5) |
| **Estimated Duration** | 7 weeks |
| **Confidence** | 0.95 |
| **v2 Plan** | See `.claude/plans/optimized-popping-cascade.md` for expanded scope |
| **v2 Changes** | 11 KB domains (was 6), 11 agents (was 6), 8 commands (was 3), upgraded template |

---

## Executive Summary

This plan transforms AgentSpec from a generic software engineering SDD framework into **the** spec-driven development framework for data engineering. The pivot leverages the existing extensible architecture (agents, KB domains, templates, workflow contracts) and the owner's AI/data engineering background.

The work is organized into 5 phases, each delivering standalone value. The core SDD workflow (brainstorm, define, design, build, ship) remains intact. All changes are pure prompt engineering -- Markdown agents, YAML contracts, and MD templates. Zero application code.

**Key Principle:** Additive over destructive. We extend existing agents rather than replacing them. The SDD workflow is technology-agnostic by design; we are enriching it with data engineering domain expertise.

---

## Current State Analysis

### What Exists

| Component | Count | Status | Notes |
|-----------|-------|--------|-------|
| Workflow phases | 5 | Stable | brainstorm, define, design, build, ship |
| Agents | 16 | Generic | No data engineering specialization |
| Slash commands | 12 | Stable | No data-specific commands |
| KB domains | 0 | Empty scaffold | Templates ready, no domains populated |
| SDD templates | 5 | Generic | No data engineering sections |
| Workflow contracts | 1 | Generic | Quality gates are technology-agnostic |
| Documentation | 5 docs | Generic | Examples use JWT auth, REST APIs |

### What Must Change

| Area | Change Type | Risk |
|------|-------------|------|
| README, docs, CLAUDE.md | Rebrand + new examples | LOW -- content swap |
| 6 existing agents | Extend with data sections | MEDIUM -- must not break existing |
| 6 new agents | Create from template | LOW -- purely additive |
| 6 KB domains | Populate from scratch | LOW -- additive, uses existing templates |
| 5 SDD templates | Add data engineering sections | MEDIUM -- structural additions |
| Workflow contracts | Add data-specific gates | MEDIUM -- must not break flow |
| 3 new commands | Create new | LOW -- additive |
| 5 doc files | Rewrite with data focus | LOW -- content swap |

---

## Phase 1: Foundation -- KB Domains (Week 1-2)

**Rationale:** KB domains are the knowledge backbone. Every agent, template, and workflow decision pulls from KB. Building this first means all subsequent phases have a knowledge base to reference.

**Dependencies:** None. This is the starting point.

**Standalone Value:** Teams can immediately use `/create-kb` output and reference domains for data engineering projects even before agents are updated.

### Deliverables

#### 1.1 dbt Knowledge Domain

| File | Action | Size | Effort |
|------|--------|------|--------|
| `.claude/kb/dbt/index.md` | Create | ~60 lines | S |
| `.claude/kb/dbt/quick-reference.md` | Create | ~100 lines | S |
| `.claude/kb/dbt/concepts/model-types.md` | Create | ~120 lines | M |
| `.claude/kb/dbt/concepts/incremental-strategies.md` | Create | ~130 lines | M |
| `.claude/kb/dbt/concepts/testing-framework.md` | Create | ~120 lines | M |
| `.claude/kb/dbt/patterns/incremental-model.md` | Create | ~150 lines | M |
| `.claude/kb/dbt/patterns/snapshot-scd2.md` | Create | ~150 lines | M |
| `.claude/kb/dbt/patterns/generic-tests.md` | Create | ~130 lines | M |
| `.claude/kb/dbt/patterns/macro-patterns.md` | Create | ~150 lines | M |

**Content Outline:**
- Model types: staging, intermediate, marts, snapshots
- Incremental strategies: append, delete+insert, merge, insert_overwrite
- ref() and source() usage, exposures, metrics
- dbt test patterns: unique, not_null, accepted_values, relationships, custom
- Jinja macros: control flow, reusable SQL generation
- Snapshot SCD Type 2 patterns
- Project structure: models/, macros/, tests/, seeds/, snapshots/

#### 1.2 Spark Knowledge Domain

| File | Action | Size | Effort |
|------|--------|------|--------|
| `.claude/kb/spark/index.md` | Create | ~60 lines | S |
| `.claude/kb/spark/quick-reference.md` | Create | ~100 lines | S |
| `.claude/kb/spark/concepts/dataframe-api.md` | Create | ~140 lines | M |
| `.claude/kb/spark/concepts/partitioning.md` | Create | ~130 lines | M |
| `.claude/kb/spark/concepts/catalyst-optimizer.md` | Create | ~120 lines | M |
| `.claude/kb/spark/patterns/read-write-patterns.md` | Create | ~150 lines | M |
| `.claude/kb/spark/patterns/window-functions.md` | Create | ~150 lines | M |
| `.claude/kb/spark/patterns/performance-tuning.md` | Create | ~180 lines | L |
| `.claude/kb/spark/patterns/streaming-patterns.md` | Create | ~150 lines | M |

**Content Outline:**
- PySpark DataFrame API: select, filter, groupBy, join, window
- Spark SQL vs DataFrame API decision matrix
- Partitioning: repartition vs coalesce, partition pruning, bucketing
- Read/write: Parquet, Delta, Iceberg, CSV, JSON
- Performance: broadcast joins, salting skewed keys, caching strategies
- Structured Streaming patterns

#### 1.3 Airflow Knowledge Domain

| File | Action | Size | Effort |
|------|--------|------|--------|
| `.claude/kb/airflow/index.md` | Create | ~60 lines | S |
| `.claude/kb/airflow/quick-reference.md` | Create | ~100 lines | S |
| `.claude/kb/airflow/concepts/dag-design.md` | Create | ~140 lines | M |
| `.claude/kb/airflow/concepts/operators.md` | Create | ~130 lines | M |
| `.claude/kb/airflow/concepts/task-dependencies.md` | Create | ~120 lines | M |
| `.claude/kb/airflow/patterns/dag-factory.md` | Create | ~150 lines | M |
| `.claude/kb/airflow/patterns/sensors-triggers.md` | Create | ~150 lines | M |
| `.claude/kb/airflow/patterns/dynamic-task-mapping.md` | Create | ~140 lines | M |
| `.claude/kb/airflow/patterns/error-handling.md` | Create | ~150 lines | M |

**Content Outline:**
- DAG design: idempotency, atomicity, retries, SLAs
- Operators: BashOperator, PythonOperator, dbt Cloud, Spark Submit, BigQuery
- TaskFlow API vs classic operators
- Dynamic task mapping (expand/map)
- Sensors and deferrable operators
- XCom patterns and limitations
- Connection/variable management
- Also covers Dagster as alternative with concept comparison

#### 1.4 Data Modeling Knowledge Domain

| File | Action | Size | Effort |
|------|--------|------|--------|
| `.claude/kb/data-modeling/index.md` | Create | ~60 lines | S |
| `.claude/kb/data-modeling/quick-reference.md` | Create | ~100 lines | S |
| `.claude/kb/data-modeling/concepts/dimensional-modeling.md` | Create | ~140 lines | M |
| `.claude/kb/data-modeling/concepts/scd-types.md` | Create | ~130 lines | M |
| `.claude/kb/data-modeling/concepts/normalization.md` | Create | ~120 lines | M |
| `.claude/kb/data-modeling/patterns/star-schema.md` | Create | ~150 lines | M |
| `.claude/kb/data-modeling/patterns/data-vault.md` | Create | ~150 lines | M |
| `.claude/kb/data-modeling/patterns/one-big-table.md` | Create | ~120 lines | M |
| `.claude/kb/data-modeling/patterns/schema-evolution.md` | Create | ~150 lines | M |

**Content Outline:**
- Kimball dimensional modeling: facts, dimensions, star schema, snowflake
- SCD Types 1-6 with trade-offs and SQL patterns
- Data Vault 2.0: hubs, links, satellites
- One Big Table (OBT) pattern for analytics
- Normalization vs denormalization decision matrix
- Schema evolution strategies: additive, backward-compatible, breaking

#### 1.5 Data Quality Knowledge Domain

| File | Action | Size | Effort |
|------|--------|------|--------|
| `.claude/kb/data-quality/index.md` | Create | ~60 lines | S |
| `.claude/kb/data-quality/quick-reference.md` | Create | ~100 lines | S |
| `.claude/kb/data-quality/concepts/data-contracts.md` | Create | ~140 lines | M |
| `.claude/kb/data-quality/concepts/quality-dimensions.md` | Create | ~130 lines | M |
| `.claude/kb/data-quality/concepts/observability.md` | Create | ~120 lines | M |
| `.claude/kb/data-quality/patterns/great-expectations.md` | Create | ~180 lines | L |
| `.claude/kb/data-quality/patterns/dbt-testing.md` | Create | ~150 lines | M |
| `.claude/kb/data-quality/patterns/schema-validation.md` | Create | ~140 lines | M |
| `.claude/kb/data-quality/patterns/freshness-monitoring.md` | Create | ~140 lines | M |

**Content Outline:**
- Quality dimensions: completeness, accuracy, consistency, timeliness, uniqueness, validity
- Data contracts: schema definition, SLA agreements, ownership
- Great Expectations: suites, expectations, checkpoints, data docs
- dbt tests: generic, singular, custom, source freshness
- Schema validation: JSON Schema, Pydantic, Avro/Protobuf
- Data observability: freshness monitoring, volume tracking, distribution drift

#### 1.6 SQL Patterns Knowledge Domain

| File | Action | Size | Effort |
|------|--------|------|--------|
| `.claude/kb/sql-patterns/index.md` | Create | ~60 lines | S |
| `.claude/kb/sql-patterns/quick-reference.md` | Create | ~100 lines | S |
| `.claude/kb/sql-patterns/concepts/window-functions.md` | Create | ~140 lines | M |
| `.claude/kb/sql-patterns/concepts/cte-patterns.md` | Create | ~130 lines | M |
| `.claude/kb/sql-patterns/concepts/set-operations.md` | Create | ~110 lines | M |
| `.claude/kb/sql-patterns/patterns/deduplication.md` | Create | ~150 lines | M |
| `.claude/kb/sql-patterns/patterns/gap-and-island.md` | Create | ~150 lines | M |
| `.claude/kb/sql-patterns/patterns/pivot-unpivot.md` | Create | ~140 lines | M |
| `.claude/kb/sql-patterns/patterns/performance.md` | Create | ~180 lines | L |

**Content Outline:**
- Window functions: ROW_NUMBER, RANK, DENSE_RANK, LAG, LEAD, running totals
- CTE patterns: recursive, chained, modular SQL
- Deduplication: exact, fuzzy, SCD-aware
- Gap and island detection
- Pivot/unpivot across dialects (Snowflake, BigQuery, Postgres, Spark SQL)
- Performance: indexing strategies, partition pruning, materialized views

#### 1.7 Update KB Index

| File | Action | Effort |
|------|--------|--------|
| `.claude/kb/_index.yaml` | Edit -- register 6 new domains | S |

### Phase 1 Summary

| Metric | Value |
|--------|-------|
| **New files** | ~55 |
| **Total effort** | XL |
| **Duration** | Week 1-2 |
| **Risk** | LOW |
| **Dependency** | None |

---

## Phase 2: New Data Engineering Agents (Week 2-3)

**Rationale:** With KB domains populated, new agents can reference them. These agents bring deep specialization that the generic agents cannot provide.

**Dependencies:** Phase 1 (KB domains must exist for agents to reference).

**Standalone Value:** Users can invoke data engineering specialists directly even before templates and workflow contracts are updated.

### Deliverables

#### 2.1 dbt Specialist Agent

| File | Action | Effort |
|------|--------|--------|
| `.claude/agents/data-engineering/dbt-specialist.md` | Create | L |

**Agent Spec:**
- **Identity:** dbt expert for models, macros, tests, and incremental strategies
- **KB Domains:** `[dbt, data-quality, sql-patterns]`
- **Triggers:** dbt model creation, macro writing, test generation, incremental strategy selection
- **Capabilities:**
  1. Model Generation -- staging, intermediate, mart models with proper ref() chains
  2. Macro Development -- Jinja macros for reusable SQL patterns
  3. Test Strategy -- generic tests, singular tests, custom test macros
  4. Incremental Strategy Selection -- append vs merge vs delete+insert decision matrix
  5. dbt Project Scaffolding -- directory structure, dbt_project.yml, packages.yml
- **Verification:** `dbt compile`, `dbt test`, `sqlfluff lint`
- **Anti-patterns:** SELECT *, missing tests, hardcoded schema names, no documentation

#### 2.2 Spark Engineer Agent

| File | Action | Effort |
|------|--------|--------|
| `.claude/agents/data-engineering/spark-engineer.md` | Create | L |

**Agent Spec:**
- **Identity:** PySpark/Spark SQL specialist for distributed data processing
- **KB Domains:** `[spark, sql-patterns]`
- **Triggers:** Spark job creation, DataFrame transformations, performance optimization
- **Capabilities:**
  1. DataFrame Transformations -- complex joins, aggregations, window functions
  2. Performance Optimization -- partition tuning, broadcast joins, skew handling
  3. Read/Write Patterns -- Parquet, Delta, Iceberg, schema evolution
  4. Structured Streaming -- micro-batch and continuous processing patterns
- **Verification:** PySpark type hints, spark-submit dry run
- **Anti-patterns:** collect() on large DataFrames, UDFs where built-ins work, coalesce(1) on large data

#### 2.3 Pipeline Architect Agent

| File | Action | Effort |
|------|--------|--------|
| `.claude/agents/data-engineering/pipeline-architect.md` | Create | L |

**Agent Spec:**
- **Identity:** Orchestration specialist for Airflow/Dagster DAG design
- **KB Domains:** `[airflow, data-quality]`
- **Triggers:** DAG creation, pipeline design, orchestration pattern selection
- **Capabilities:**
  1. DAG Design -- task dependencies, parallelism, retry strategies
  2. Operator Selection -- choosing the right operator for each task
  3. Dynamic Pipelines -- dynamic task mapping, parameterized DAGs
  4. SLA Management -- alerting, monitoring, timeout configuration
  5. Cross-DAG Dependencies -- sensors, triggers, dataset-driven scheduling
- **Verification:** DAG import check, `airflow dags test`
- **Anti-patterns:** Monolithic DAGs, top-level code outside DAG context, hardcoded connections

#### 2.4 Data Quality Analyst Agent

| File | Action | Effort |
|------|--------|--------|
| `.claude/agents/data-engineering/data-quality-analyst.md` | Create | L |

**Agent Spec:**
- **Identity:** Data quality specialist for validation, contracts, and observability
- **KB Domains:** `[data-quality, dbt, data-modeling]`
- **Triggers:** Quality rule generation, data contract creation, test suite design
- **Capabilities:**
  1. Expectation Suite Generation -- Great Expectations YAML suites
  2. dbt Test Generation -- schema.yml tests, singular tests, custom macros
  3. Data Contract Authoring -- schema definitions, SLA agreements, ownership
  4. Quality Dashboard Design -- key metrics, alerting thresholds
  5. PII Detection -- column-level scanning rules, masking patterns
- **Verification:** `great_expectations suite run`, `dbt test`
- **Anti-patterns:** Missing uniqueness tests, no null checks on required fields, no freshness monitoring

#### 2.5 Schema Designer Agent

| File | Action | Effort |
|------|--------|--------|
| `.claude/agents/data-engineering/schema-designer.md` | Create | L |

**Agent Spec:**
- **Identity:** Data modeling specialist for schema design and evolution
- **KB Domains:** `[data-modeling, sql-patterns, data-quality]`
- **Triggers:** Schema creation, modeling decisions, schema migration planning
- **Capabilities:**
  1. Dimensional Modeling -- star schema, snowflake, fact/dimension identification
  2. SCD Strategy Selection -- Type 1-6 trade-off analysis
  3. Schema Evolution Planning -- backward-compatible migrations, versioning
  4. DDL Generation -- CREATE TABLE with proper types, constraints, partitioning
  5. Data Vault Design -- hubs, links, satellites for enterprise data warehousing
- **Verification:** DDL syntax validation, constraint completeness
- **Anti-patterns:** Missing primary keys, VARCHAR without length limits, no partition key, nullable facts

#### 2.6 Data Platform Engineer Agent

| File | Action | Effort |
|------|--------|--------|
| `.claude/agents/data-engineering/data-platform-engineer.md` | Create | L |

**Agent Spec:**
- **Identity:** Infrastructure specialist for data platforms (Snowflake, BigQuery, Databricks, Delta Lake, Iceberg)
- **KB Domains:** `[spark, data-modeling, data-quality]`
- **Triggers:** Platform setup, infrastructure decisions, cost optimization
- **Capabilities:**
  1. Platform Comparison -- Snowflake vs BigQuery vs Databricks decision matrix
  2. Table Format Selection -- Delta Lake vs Iceberg vs Hudi trade-offs
  3. Warehouse Configuration -- clustering, materialized views, search optimization
  4. Cost Optimization -- compute sizing, auto-suspend, slot reservations
  5. Access Control -- roles, grants, row-level security, dynamic masking
- **Verification:** Infrastructure configuration validation
- **Anti-patterns:** No warehouse auto-suspend, missing clustering keys, over-provisioned compute

#### 2.7 Create Data Engineering Agents README

| File | Action | Effort |
|------|--------|--------|
| `.claude/agents/data-engineering/README.md` | Create | S |

#### 2.8 Update Agents README

| File | Action | Effort |
|------|--------|--------|
| `.claude/agents/README.md` | Edit -- add data-engineering category | S |

### Phase 2 Summary

| Metric | Value |
|--------|-------|
| **New files** | 8 |
| **Total effort** | XL |
| **Duration** | Week 2-3 |
| **Risk** | LOW (purely additive) |
| **Dependency** | Phase 1 |

---

## Phase 3: Adapt Existing Components (Week 3-4)

**Rationale:** With KB and new agents in place, existing agents and templates need data engineering awareness. This is the highest-risk phase because it modifies existing files.

**Dependencies:** Phase 1 (KB domains). Phase 2 is helpful but not strictly required.

**Standalone Value:** The core SDD workflow becomes data engineering-aware. Define captures data SLAs, Design includes DAG diagrams, Build verifies with dbt/sqlfluff.

### 3A: Adapt Existing Agents

#### 3A.1 Adapt code-reviewer

| File | Action | Effort |
|------|--------|--------|
| `.claude/agents/code-quality/code-reviewer.md` | Edit | M |

**Changes:**
- Add **Capability 5: Data Engineering Review** with checklist:
  - PII detection in SELECT statements and logging
  - Schema validation (NOT NULL on required fields, proper types)
  - Partition key presence for large tables
  - dbt test coverage (unique, not_null, relationships)
  - SQL anti-patterns (SELECT *, CROSS JOIN without filter, missing WHERE on UPDATE/DELETE)
  - Data contract compliance
- Add data engineering severity examples to the classification table
- Add `kb_domains: [data-quality, sql-patterns, dbt]` to frontmatter

#### 3A.2 Adapt code-cleaner

| File | Action | Effort |
|------|--------|--------|
| `.claude/agents/code-quality/code-cleaner.md` | Edit | M |

**Changes:**
- Add **Capability 5: SQL/dbt Cleaning** with transformations:
  - CTE refactoring: subqueries to CTEs
  - SELECT * to explicit column lists
  - Consistent SQL formatting (uppercase keywords, lowercase identifiers)
  - YAML config cleaning: proper indentation, alphabetical keys, no duplicate entries
  - Jinja whitespace control: `{%- -%}` patterns
- Add SQL-specific comment classification (keep business rule comments in SQL)
- Expand identity to "Python and SQL code cleaning specialist"

#### 3A.3 Adapt test-generator

| File | Action | Effort |
|------|--------|--------|
| `.claude/agents/code-quality/test-generator.md` | Edit | M |

**Changes:**
- Add **Capability 5: Data Transformation Tests** with templates:
  - Row count assertions (input vs output)
  - Column type validation
  - Null percentage checks
  - Value range assertions
  - Referential integrity tests
- Add **Capability 6: Great Expectations Suite Generation** with template:
  - Expectation suite YAML structure
  - Common expectations: expect_column_values_to_not_be_null, expect_column_values_to_be_unique
  - Checkpoint configuration
- Add **Capability 7: dbt Test Generation** with template:
  - schema.yml test blocks
  - Singular test SQL files
  - Custom test macro patterns
- Add `kb_domains: [data-quality, dbt]` to frontmatter

#### 3A.4 Adapt design-agent

| File | Action | Effort |
|------|--------|--------|
| `.claude/agents/workflow/design-agent.md` | Edit | M |

**Changes:**
- Add data engineering sections to design output guidance:
  - Pipeline DAG diagram (ASCII art showing task dependencies)
  - Data flow architecture (source to target with transformations)
  - Partition strategy section
  - Incremental model strategy section
  - Schema evolution plan
- Update agent discovery to include `.claude/agents/data-engineering/` path
- Add data engineering decision templates (e.g., "Incremental vs Full Refresh")

#### 3A.5 Adapt define-agent

| File | Action | Effort |
|------|--------|--------|
| `.claude/agents/workflow/define-agent.md` | Edit | M |

**Changes:**
- Add data engineering extraction elements:
  - Data freshness SLAs: "Data must be available within X hours of source update"
  - Completeness metrics: "99.5% of records must have non-null values for field X"
  - Schema contract: "Output schema must match contract defined in Y"
  - Volume estimates: "Expected X rows/day, Y GB/month"
  - Source system inventory: "Data comes from A, B, C"
- Add data engineering patterns to extraction_elements
- Add data-specific assumptions (e.g., "Source schema will not change without notice")

#### 3A.6 Adapt build-agent

| File | Action | Effort |
|------|--------|--------|
| `.claude/agents/workflow/build-agent.md` | Edit | M |

**Changes:**
- Add data engineering verification commands:
  - `dbt build` (compile + run + test)
  - `sqlfluff lint` for SQL files
  - `great_expectations suite run` for GE checkpoints
  - `dbt source freshness` for source monitoring
  - `spark-submit --dry-run` for Spark jobs (where applicable)
- Add data engineering agent delegation patterns (delegate to dbt-specialist, spark-engineer, etc.)
- Add data-specific quality gate checks in build report generation

### 3B: Adapt SDD Templates

#### 3B.1 Adapt BRAINSTORM_TEMPLATE.md

| File | Action | Effort |
|------|--------|--------|
| `.claude/sdd/templates/BRAINSTORM_TEMPLATE.md` | Edit | M |

**Changes -- add new section after "Sample Data Inventory":**

```markdown
## Data Engineering Context (if applicable)

### Source Systems

| Source | Type | Volume | Freshness | Access |
|--------|------|--------|-----------|--------|
| {Source 1} | {API / DB / File / Stream} | {rows/day or GB} | {Real-time / Hourly / Daily} | {How accessed} |

### Data Volume Estimates

| Metric | Estimate | Notes |
|--------|----------|-------|
| Daily row volume | {N} | {Growth expectations} |
| Monthly storage | {N GB} | {Retention policy} |
| Peak processing time | {N min} | {SLA window} |

### Freshness Requirements

| Consumer | Maximum Latency | Current State |
|----------|----------------|---------------|
| {Dashboard/Report/API} | {minutes/hours} | {What exists today} |
```

#### 3B.2 Adapt DEFINE_TEMPLATE.md

| File | Action | Effort |
|------|--------|--------|
| `.claude/sdd/templates/DEFINE_TEMPLATE.md` | Edit | L |

**Changes -- add new sections after "Technical Context":**

```markdown
## Data Contract (if applicable)

### Source Schema

| Field | Type | Nullable | Description | PII |
|-------|------|----------|-------------|-----|
| {field} | {type} | {Y/N} | {description} | {Y/N} |

### Output Schema

| Field | Type | Nullable | Description | Derived From |
|-------|------|----------|-------------|--------------|
| {field} | {type} | {Y/N} | {description} | {source field or transformation} |

### Data SLAs

| SLA | Target | Measurement |
|-----|--------|-------------|
| Freshness | {Data available within X hours of source} | {How measured} |
| Completeness | {>99% non-null for required fields} | {Which fields} |
| Accuracy | {<0.1% error rate} | {Validation method} |
| Volume | {Process X rows/day} | {Growth factor} |

### Data Lineage

```text
[Source A] ──┐
             ├──→ [Staging] ──→ [Transform] ──→ [Mart] ──→ [Consumer]
[Source B] ──┘
```
```

#### 3B.3 Adapt DESIGN_TEMPLATE.md

| File | Action | Effort |
|------|--------|--------|
| `.claude/sdd/templates/DESIGN_TEMPLATE.md` | Edit | L |

**Changes -- add new sections after "Data Flow":**

```markdown
## Pipeline Architecture (if applicable)

### DAG Diagram

```text
[extract_source_a] ──┐
                     ├──→ [stage_data] ──→ [transform] ──→ [load_mart]
[extract_source_b] ──┘                                        │
                                                               ▼
                                                        [run_quality_checks]
                                                               │
                                                               ▼
                                                        [notify_consumers]
```

### Partition Strategy

| Table | Partition Key | Granularity | Rationale |
|-------|--------------|-------------|-----------|
| {table} | {column} | {daily/monthly} | {query patterns} |

### Incremental Strategy

| Model | Strategy | Unique Key | Updated At | Rationale |
|-------|----------|------------|------------|-----------|
| {model} | {merge/delete+insert/append} | {key columns} | {timestamp col} | {why this strategy} |

### Schema Evolution Plan

| Change Type | Strategy | Backward Compatible |
|-------------|----------|---------------------|
| Add column | {ALTER TABLE ADD} | Yes |
| Rename column | {Add new + backfill + deprecate old} | Yes (transition period) |
| Change type | {New column + cast + swap} | Yes (transition period) |
| Remove column | {Deprecate + grace period + drop} | Yes (with notice) |
```

#### 3B.4 Adapt BUILD_REPORT_TEMPLATE.md

| File | Action | Effort |
|------|--------|--------|
| `.claude/sdd/templates/BUILD_REPORT_TEMPLATE.md` | Edit | M |

**Changes -- add new section after "Performance Notes":**

```markdown
## Data Quality Results (if applicable)

### Pipeline Metrics

| Metric | Expected | Actual | Status |
|--------|----------|--------|--------|
| Input rows | {N} | {N} | Pass/Fail |
| Output rows | {N} | {N} | Pass/Fail |
| Processing time | {X min} | {X min} | Pass/Fail |
| Failed records | {<N} | {N} | Pass/Fail |

### Data Quality Checks

| Check | Tool | Status | Details |
|-------|------|--------|---------|
| Schema validation | dbt test | Pass/Fail | {details} |
| Uniqueness | dbt test | Pass/Fail | {columns checked} |
| Null checks | dbt test | Pass/Fail | {columns checked} |
| Freshness | dbt source freshness | Pass/Fail | {max age} |
| Custom rules | Great Expectations | Pass/Fail | {suite name} |

### SQL Lint Results

```text
{sqlfluff lint output or "All checks passed"}
```
```

### 3C: Update Workflow Contracts

#### 3C.1 Update WORKFLOW_CONTRACTS.yaml

| File | Action | Effort |
|------|--------|--------|
| `.claude/sdd/architecture/WORKFLOW_CONTRACTS.yaml` | Edit | L |

**Changes:**

1. **Update description** from "software development workflow" to "software and data engineering development workflow"

2. **Add data engineering examples** alongside existing ones:
   - brainstorm examples: `'/brainstorm "Build a daily revenue pipeline from Stripe to Snowflake"'`
   - define examples: `'/define "Create an incremental dbt model for order facts"'`
   - design examples: `'/design DEFINE_ORDER_FACTS_PIPELINE.md'`

3. **Add data engineering quality gates** (additive, not replacing):

```yaml
data_engineering_quality_gate:
  description: "Additional quality checks for data engineering features"
  optional: true
  activated_by: "Feature involves data pipelines, models, or schemas"
  checks:
    define_phase:
      - data_sources_identified
      - freshness_sla_defined
      - schema_contract_drafted
      - volume_estimates_provided
    design_phase:
      - partition_strategy_defined
      - incremental_strategy_selected
      - dag_dependencies_mapped
      - schema_evolution_planned
    build_phase:
      - dbt_tests_passing
      - sql_lint_clean
      - data_quality_checks_green
      - source_freshness_verified
```

4. **Add data engineering anti-patterns** to each phase:

```yaml
data_anti_patterns:
  universal:
    - select_star: "SELECT * in production queries"
    - missing_partitions: "Large tables without partition keys"
    - no_schema_tests: "Models without uniqueness or null tests"
    - hardcoded_dates: "Date filters with hardcoded values instead of variables"
    - no_incremental_strategy: "Full refresh on tables >1M rows without justification"
  define:
    - no_freshness_sla: "Data pipeline without latency requirements"
    - no_volume_estimate: "No sizing for data growth"
  design:
    - monolithic_pipeline: "Single DAG task doing extract+transform+load"
    - no_idempotency: "Pipeline that produces different results on re-run"
  build:
    - untested_model: "dbt model without at least unique + not_null tests"
    - no_sql_lint: "SQL files not validated by sqlfluff or sqlfmt"
```

5. **Add data engineering verification commands:**

```yaml
data_verification:
  per_file:
    sql: "sqlfluff lint {file}"
    dbt_model: "dbt compile --select {model}"
    python_spark: "ruff check {file} && mypy {file}"
  full:
    dbt: "dbt build"
    sql_lint: "sqlfluff lint models/"
    great_expectations: "great_expectations suite run"
    source_freshness: "dbt source freshness"
```

### Phase 3 Summary

| Metric | Value |
|--------|-------|
| **Modified files** | 11 |
| **New sections per template** | 1-3 |
| **Total effort** | XL |
| **Duration** | Week 3-4 |
| **Risk** | MEDIUM (modifying existing files) |
| **Dependency** | Phase 1, Phase 2 (soft) |

---

## Phase 4: New Commands + Rebrand (Week 4-5)

**Rationale:** New slash commands provide quick-access workflows for common data engineering tasks. Rebranding positions the framework for the target audience.

**Dependencies:** Phase 1-3 (agents and KB must exist for commands to reference).

**Standalone Value:** Users get data-specific shortcuts and the project communicates its data engineering focus.

### 4A: New Slash Commands

#### 4A.1 /pipeline Command

| File | Action | Effort |
|------|--------|--------|
| `.claude/commands/data-engineering/pipeline.md` | Create | M |

**Command Spec:**
- **Purpose:** Quick pipeline scaffolding -- generates Airflow DAG or Dagster job skeleton
- **Args:** `<pipeline-name> [--orchestrator airflow|dagster]`
- **Behavior:**
  1. Ask: source systems, target, schedule, orchestrator preference
  2. Generate DAG skeleton with proper structure
  3. Include error handling, retries, SLA alerts
  4. Reference KB domain: `airflow/`
- **Delegates to:** `pipeline-architect` agent

#### 4A.2 /schema Command

| File | Action | Effort |
|------|--------|--------|
| `.claude/commands/data-engineering/schema.md` | Create | M |

**Command Spec:**
- **Purpose:** Interactive schema design helper
- **Args:** `<table-name> [--approach star|vault|obt]`
- **Behavior:**
  1. Ask: entity type (fact/dimension/hub/link), grain, key columns
  2. Generate DDL with proper types, constraints, partitioning
  3. Suggest tests for the schema
  4. Reference KB domain: `data-modeling/`
- **Delegates to:** `schema-designer` agent

#### 4A.3 /data-quality Command

| File | Action | Effort |
|------|--------|--------|
| `.claude/commands/data-engineering/data-quality.md` | Create | M |

**Command Spec:**
- **Purpose:** Generate data quality rules for a table or model
- **Args:** `<model-or-table> [--framework dbt|great-expectations]`
- **Behavior:**
  1. Read model/table schema
  2. Auto-generate quality rules: not_null, unique, accepted_values, relationships
  3. Generate freshness checks if source
  4. Output: schema.yml block or GE expectation suite
  5. Reference KB domain: `data-quality/`
- **Delegates to:** `data-quality-analyst` agent

#### 4A.4 Create Commands README

| File | Action | Effort |
|------|--------|--------|
| `.claude/commands/data-engineering/README.md` | Create | S |

#### 4A.5 Update Commands README

| File | Action | Effort |
|------|--------|--------|
| `.claude/commands/README.md` | Edit -- add data-engineering section | S |

### 4B: Rebrand and Positioning

#### 4B.1 Update README.md

| File | Action | Effort |
|------|--------|--------|
| `README.md` | Rewrite | L |

**Changes:**
- Tagline: "Spec-Driven Development for Data Engineering" (was "for Claude Code")
- Subtitle: "Turn data pipeline ideas into production-grade, tested, documented pipelines"
- Problem statement: Focus on data engineering chaos (undocumented pipelines, missing tests, schema drift, no SLAs)
- Solution: Frame SDD through data engineering lens
- Quick Start example: Replace JWT auth with "Build your first dbt pipeline"
  ```bash
  claude> /brainstorm "Build a daily revenue pipeline from Stripe API to Snowflake marts"
  claude> /define STRIPE_REVENUE_PIPELINE
  claude> /design STRIPE_REVENUE_PIPELINE
  claude> /build STRIPE_REVENUE_PIPELINE
  claude> /ship STRIPE_REVENUE_PIPELINE
  ```
- "What You Get" section: Highlight data engineering agents, KB domains, data quality gates
- Add "Built for Data Engineers" section showcasing agent specializations
- Add "Knowledge Base" section listing the 6 pre-populated domains
- Keep "Works for Any Software Project" note (backward compatibility)

#### 4B.2 Update CLAUDE.md

| File | Action | Effort |
|------|--------|--------|
| `CLAUDE.md` | Edit | M |

**Changes:**
- Project Context: "A Claude Code plugin for spec-driven data engineering development"
- Repository Structure: Add data-engineering agent and KB domain directories
- Examples: Replace generic examples with data engineering ones
- Commands table: Add new /pipeline, /schema, /data-quality commands
- Agent count: Update from 16 to 22

### Phase 4 Summary

| Metric | Value |
|--------|-------|
| **New files** | 5 |
| **Modified files** | 3 |
| **Total effort** | L |
| **Duration** | Week 4-5 |
| **Risk** | LOW |
| **Dependency** | Phase 1-3 |

---

## Phase 5: Documentation Overhaul (Week 5-6)

**Rationale:** Documentation is the first thing new users see. With all components in place, documentation can accurately reflect the data engineering focus.

**Dependencies:** Phase 1-4 (all components must exist to document accurately).

**Standalone Value:** Complete onboarding experience for data engineers.

### Deliverables

#### 5.1 Getting Started Guide

| File | Action | Effort |
|------|--------|--------|
| `docs/getting-started/README.md` | Rewrite | L |

**Changes:**
- Title: "Getting Started: Build Your First Data Pipeline with SDD"
- Walk through a complete example: dbt model pipeline from source to mart
- Show each phase producing a document
- Demonstrate data engineering quality gates
- Include data-specific commands (/pipeline, /schema, /data-quality)

#### 5.2 Concepts Documentation

| File | Action | Effort |
|------|--------|--------|
| `docs/concepts/README.md` | Rewrite | L |

**Changes:**
- Frame SDD pillars through data engineering:
  - **Traceability** = data lineage from source to consumer
  - **Quality Gates** = data contracts and SLA enforcement
  - **Knowledge Preservation** = schema decisions, modeling rationale, pipeline patterns
  - **Iterative Refinement** = schema evolution, incremental strategies
- Add "Why SDD for Data Engineering?" section
- Add comparison: "SDD vs Ad-hoc Pipeline Development"

#### 5.3 Tutorials

| File | Action | Effort |
|------|--------|--------|
| `docs/tutorials/README.md` | Rewrite | L |

**Changes:**
- Tutorial 1: "End-to-End dbt Pipeline" (brainstorm through ship)
- Tutorial 2: "Designing a Star Schema with SDD"
- Tutorial 3: "Adding Data Quality to an Existing Pipeline"
- Tutorial 4: "Building a Spark ETL Job with SDD"
- Each tutorial shows the full SDD workflow with data engineering examples

#### 5.4 Reference Documentation

| File | Action | Effort |
|------|--------|--------|
| `docs/reference/README.md` | Rewrite | L |

**Changes:**
- Agent catalog: Add 6 new data engineering agents with descriptions
- Command catalog: Add 3 new data engineering commands
- KB domain catalog: List all 6 pre-populated domains
- Template reference: Document new data engineering sections in each template
- Quality gate reference: Document data engineering quality gates

#### 5.5 Main Docs README

| File | Action | Effort |
|------|--------|--------|
| `docs/README.md` | Edit | S |

**Changes:**
- Update navigation to highlight data engineering content
- Add "Data Engineering Quick Links" section

### Phase 5 Summary

| Metric | Value |
|--------|-------|
| **Modified files** | 5 |
| **Total effort** | L |
| **Duration** | Week 5-6 |
| **Risk** | LOW |
| **Dependency** | Phase 1-4 |

---

## Risk Assessment

### Risk Matrix

```text
              | Low Impact  | High Impact |
--------------+-------------+-------------+
High Prob     | Monitor     | CRITICAL    |
--------------+-------------+-------------+
Low Prob      | Accept      | Monitor     |
```

### Identified Risks

| # | Risk | Impact | Probability | Mitigation |
|---|------|--------|-------------|------------|
| R1 | Breaking existing SDD workflow when modifying agents/templates | HIGH | LOW | All changes are additive. New sections marked "(if applicable)". Existing sections untouched. |
| R2 | KB domain content becomes stale as tools evolve | MEDIUM | MEDIUM | Use MCP validation dates. Schedule quarterly reviews. Focus on stable patterns, not bleeding-edge features. |
| R3 | Agent prompts become too long, hitting context limits | MEDIUM | LOW | Keep agents focused. Data engineering capabilities are 1-2 new sections per agent, not complete rewrites. New agents are standalone. |
| R4 | Scope creep into application code (CLI tools, validators) | HIGH | LOW | Constraint is clear: all changes are Markdown/YAML. No Python, no CLI tools, no runtime dependencies. |
| R5 | Data engineering examples become too opinionated toward one stack | MEDIUM | MEDIUM | Cover multiple stacks (Snowflake + BigQuery + Databricks, Airflow + Dagster). Use decision matrices, not prescriptions. |
| R6 | Existing generic users feel excluded by data engineering focus | LOW | MEDIUM | Keep core SDD workflow generic. Data engineering sections are clearly optional. README note: "Built for data engineering, works for any software project." |
| R7 | KB domains are too shallow to be useful | MEDIUM | LOW | Each domain targets 9 files with concrete code examples. Patterns include production-ready snippets. Quick references provide immediate value. |

### Contingency Plans

| Trigger | Response |
|---------|----------|
| Phase 3 breaks existing workflow | Revert agent changes, use feature branches per agent |
| KB content found inaccurate | MCP-validate with context7/ref, update specific files |
| Agent prompts too large | Split into sub-capabilities, use KB offloading |
| User feedback: too data-engineering-specific | Add "General Software" toggle in templates |

---

## Dependency Map

```text
Phase 1: KB Domains
    |
    ├──→ Phase 2: New Agents (needs KB to reference)
    |        |
    |        ├──→ Phase 3: Adapt Existing (needs KB + new agents)
    |        |        |
    |        |        ├──→ Phase 4: Commands + Rebrand (needs all components)
    |        |        |        |
    |        |        |        └──→ Phase 5: Documentation (needs everything)
    |        |        |
    |        |        └──→ Phase 4B: Rebrand (can start in parallel with 4A)
    |        |
    |        └──→ Phase 3B: Templates (can start in parallel with 3A)
    |
    └──→ Phase 3C: Workflow Contracts (needs KB for anti-patterns)
```

**Critical Path:** Phase 1 --> Phase 2 --> Phase 3A --> Phase 4A --> Phase 5

**Parallelizable:**
- Phase 3A (agents) and Phase 3B (templates) can run in parallel
- Phase 4A (commands) and Phase 4B (rebrand) can run in parallel
- Phase 5 can start as soon as Phase 4 is underway (writing ahead)

---

## Effort Summary

| Phase | Description | New Files | Modified Files | Effort | Duration |
|-------|-------------|-----------|----------------|--------|----------|
| 1 | KB Domains | ~55 | 1 | XL | Week 1-2 |
| 2 | New Agents | 8 | 0 | XL | Week 2-3 |
| 3 | Adapt Existing | 0 | 11 | XL | Week 3-4 |
| 4 | Commands + Rebrand | 5 | 3 | L | Week 4-5 |
| 5 | Documentation | 0 | 5 | L | Week 5-6 |
| **Total** | | **~68** | **~20** | **XL** | **6 weeks** |

---

## Execution Strategy

### Recommended Approach: Use SDD to Build SDD

Each phase should be executed using AgentSpec's own SDD workflow:

```bash
# Phase 1 example
/brainstorm "Create dbt knowledge base domain for AgentSpec"
/define KB_DBT_DOMAIN
/design KB_DBT_DOMAIN
/build KB_DBT_DOMAIN
/ship KB_DBT_DOMAIN
```

This approach:
- Dog-foods the framework during the pivot
- Produces traceable artifacts for each change
- Captures lessons learned that improve the framework itself
- Creates referenceable examples of the SDD workflow applied to data engineering

### Phase Execution Checklist

For each phase:

- [ ] Create feature branch: `feature/phase-N-description`
- [ ] Run `/define` to capture phase requirements
- [ ] Run `/design` to plan file-level changes
- [ ] Run `/build` to execute changes
- [ ] Verify no regressions: existing SDD workflow still works
- [ ] Run `/ship` to archive the phase
- [ ] Create PR with phase deliverables
- [ ] Merge to main

### File Count by Phase

| Phase | Files to Create | Files to Modify |
|-------|----------------|-----------------|
| Phase 1 | 55 (9 per domain x 6 + _index.yaml) | 1 |
| Phase 2 | 8 (6 agents + 2 READMEs) | 0 |
| Phase 3 | 0 | 11 (6 agents + 4 templates + 1 contract) |
| Phase 4 | 5 (3 commands + 2 READMEs) | 3 (README, CLAUDE.md, commands README) |
| Phase 5 | 0 | 5 (5 doc files) |

---

## Success Criteria

| Criterion | Measurement | Target |
|-----------|-------------|--------|
| KB domains populated | Domains with 7+ files each | 6/6 |
| New agents created | Agents following _template.md structure | 6/6 |
| Existing agents adapted | Data engineering capabilities added | 6/6 |
| Templates adapted | Data engineering sections added | 4/4 |
| Workflow contracts updated | Data quality gates added | 1/1 |
| New commands created | Working slash commands | 3/3 |
| README rebranded | Data engineering positioning | Yes |
| Documentation rewritten | Data engineering focus throughout | 5/5 |
| Backward compatibility | Existing SDD workflow unchanged | Yes |
| Zero application code | All changes are .md and .yaml | Yes |

---

## Post-Pivot Opportunities

After completing the 5 phases, the following enhancements become possible:

| Opportunity | Description | Priority |
|-------------|-------------|----------|
| Data lineage visualization | ASCII-art lineage diagrams auto-generated from DESIGN docs | SHOULD |
| dbt project template | `/init-dbt` command that scaffolds a full dbt project with SDD | COULD |
| Lakehouse KB domain | Delta Lake, Iceberg, Hudi patterns in dedicated domain | SHOULD |
| Streaming KB domain | Kafka, Flink, Spark Streaming patterns | COULD |
| Cost estimation agent | Estimate compute/storage costs during Design phase | COULD |
| Migration playbook | Guide for migrating legacy ETL to modern stack using SDD | SHOULD |
| Community KB domains | Allow community to contribute KB domains for niche tools | COULD |

---

## Revision History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2026-03-26 | the-planner | Initial plan |
