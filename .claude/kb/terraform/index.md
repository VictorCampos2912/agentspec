# Terraform for GCP Knowledge Base

> **Purpose**: Infrastructure as Code for GCP serverless architecture with module patterns
> **MCP Validated**: 2026-02-17

## Quick Navigation

### Concepts (< 150 lines each)

| File | Purpose |
|------|---------|
| [concepts/resources.md](concepts/resources.md) | Resource blocks, lifecycle rules, meta-arguments |
| [concepts/modules.md](concepts/modules.md) | Module composition, source types, versioning |
| [concepts/providers.md](concepts/providers.md) | GCP provider configuration, authentication |
| [concepts/state.md](concepts/state.md) | State management, locking, imports |
| [concepts/variables.md](concepts/variables.md) | Variables, locals, outputs, type constraints |
| [concepts/workspaces.md](concepts/workspaces.md) | Workspace isolation, environment separation |

### Patterns (< 200 lines each)

| File | Purpose |
|------|---------|
| [patterns/cloud-run-module.md](patterns/cloud-run-module.md) | Cloud Run service Terraform module |
| [patterns/pubsub-module.md](patterns/pubsub-module.md) | Pub/Sub topic and subscription module |
| [patterns/gcs-module.md](patterns/gcs-module.md) | GCS bucket with lifecycle rules module |
| [patterns/bigquery-module.md](patterns/bigquery-module.md) | BigQuery dataset and table module |
| [patterns/iam-module.md](patterns/iam-module.md) | IAM bindings and service accounts module |
| [patterns/remote-state.md](patterns/remote-state.md) | Remote state with GCS backend pattern |

### Specs (Machine-Readable)

| File | Purpose |
|------|---------|
| [specs/terraform-config.yaml](specs/terraform-config.yaml) | Configuration spec for Terraform GCP projects |

---

## Quick Reference

- [quick-reference.md](quick-reference.md) - Fast lookup tables for commands, resources, arguments

---

## Key Concepts

| Concept | Description |
|---------|-------------|
| **Resources** | Declarative GCP infrastructure blocks with lifecycle management |
| **Modules** | Reusable, composable infrastructure packages |
| **Providers** | GCP API interface with authentication and region config |
| **State** | Terraform's record of managed infrastructure |
| **Variables** | Parameterization for flexible, reusable configurations |
| **Workspaces** | Environment isolation using shared configuration |

---

## Learning Path

| Level | Files |
|-------|-------|
| **Beginner** | concepts/providers.md, concepts/resources.md, concepts/variables.md |
| **Intermediate** | concepts/modules.md, concepts/state.md, patterns/remote-state.md |
| **Advanced** | concepts/workspaces.md, patterns/iam-module.md, specs/terraform-config.yaml |

---

## Agent Usage

| Agent | Primary Files | Use Case |
|-------|---------------|----------|
| KB Architect | All files | Creating and auditing Terraform KB |
| IaC Engineer | patterns/*.md | Building GCP infrastructure modules |
| Platform Eng | concepts/state.md, concepts/workspaces.md | State and environment management |
