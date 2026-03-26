---
name: dev
description: AgentLoop — SDD-lite for quick tasks with Agent Matching Engine and structured iteration
---

# /dev Command

> **AgentLoop** — SDD-lite for quick tasks with Agent Matching Engine and structured iteration.

## Usage

```bash
# Craft a new PROMPT (prompt-crafter guides you)
/dev "I want to create a date parser utility"
/dev "Add incremental refresh to stg_orders"

# Execute an existing PROMPT
/dev tasks/PROMPT_DATE_PARSER.md

# Dry run (plan without executing)
/dev tasks/PROMPT_CACHE.md --dry-run

# List available PROMPTs
/dev --list
```

## How It Works

```text
┌─────────────────────────────────────────────────────────────────────┐
│                          /dev COMMAND ROUTING                         │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  Input is a string description?                                      │
│  ├─ YES → Route to prompt-crafter                                    │
│  │        Explore → Define → Design → Generate PROMPT.md             │
│  │                                                                   │
│  Input is a PROMPT_*.md file?                                        │
│  ├─ YES → Route to build-agent with PROMPT context                   │
│  │        Parse manifest → Delegate to matched agents → Verify       │
│  │                                                                   │
│  --list flag?                                                        │
│  └─ YES → List tasks/PROMPT_*.md with status                        │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

## When to Use /dev vs /build

| Scenario | Use |
|----------|-----|
| Quick task, < 5 files | `/dev` (SDD-lite) |
| Full feature, formal requirements | `/define` → `/design` → `/build` (full SDD) |
| Overnight unattended execution | `/dev` with `overnight-builder` |

## Agent Delegation

| Agent | Role |
|-------|------|
| `prompt-crafter` | PROMPT.md generation (SDD-lite phases) |
| `build-agent` | PROMPT execution with agent matching |
| `overnight-builder` | Unattended batch execution |
