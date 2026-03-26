# Fusion Engine

> **Purpose**: Rust-based dbt compilation via SDF acquisition — sub-second parsing for 10K+ models
> **Confidence**: 0.90
> **MCP Validated**: 2026-03-26

## Overview

dbt Labs acquired SDF Labs (a Rust-based SQL compiler) in late 2024, creating the **Fusion Engine**. It rewrites dbt's core parsing and execution in Rust, achieving sub-second parse times for projects with 10,000+ models (vs 30-60 seconds with dbt Core's Python parser). Fusion includes built-in column-level lineage, deep SQL comprehension, and IDE-grade autocomplete.

## The Concept

```yaml
# dbt_project.yml — Fusion Engine configuration
name: my_analytics
version: "1.0.0"
config-version: 2

# Fusion Engine reads .env files automatically
# Enable Fusion features via dbt Cloud or dbt Fusion CLI

# Key Fusion capabilities:
# - Sub-second parsing (Rust-based SQL compiler)
# - Column-level lineage without extra config
# - Cross-project dependency resolution (Mesh)
# - IDE-grade autocomplete via LSP
# - Automatic .env file loading
```

## Quick Reference

| Feature | dbt Core (Python) | dbt Fusion (Rust) |
|---------|-------------------|-------------------|
| Parse 10K models | 30-60 seconds | < 1 second |
| Column-level lineage | Requires dbt Cloud | Built-in |
| IDE autocomplete | Basic (extension) | Full LSP server |
| .env file support | Manual loading | Automatic |
| Cross-project refs | dbt Mesh (separate) | Integrated |
| Status | GA, stable | Beta (GA expected early 2026) |

## Common Mistakes

### Wrong

```bash
# Assuming Fusion is a drop-in replacement today
# Fusion is in beta — test before migrating production
dbt run --target prod  # on Fusion CLI without testing
```

### Correct

```bash
# Test Fusion in dev first, compare output with Core
dbt compile --target dev  # Fusion CLI
dbt compile --target dev  # Core CLI
diff target/compiled/     # Compare generated SQL

# Migrate to Fusion only after output parity confirmed
```

## Related

- [mesh-architecture](../concepts/mesh-architecture.md)
- [model-types](../concepts/model-types.md)
