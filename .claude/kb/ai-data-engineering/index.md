# AI Data Engineering Knowledge Base

> **Purpose**: AI data engineering — RAG pipelines, vector DBs, feature stores, LLMOps, embeddings
> **MCP Validated**: 2026-03-26

## Quick Navigation

### Concepts (< 150 lines each)

| File | Purpose |
|------|---------|
| [concepts/rag-pipelines.md](concepts/rag-pipelines.md) | Chunking, indexing, re-ranking, hybrid search |
| [concepts/vector-databases.md](concepts/vector-databases.md) | HNSW, IVF, distance metrics, filtering |
| [concepts/feature-stores.md](concepts/feature-stores.md) | Online/offline stores, Feast, Tecton |
| [concepts/embedding-pipelines.md](concepts/embedding-pipelines.md) | Model selection, batch vs streaming |
| [concepts/llmops-patterns.md](concepts/llmops-patterns.md) | Prompt versioning, guardrails, eval |

### Patterns (< 200 lines each)

| File | Purpose |
|------|---------|
| [patterns/rag-pipeline-implementation.md](patterns/rag-pipeline-implementation.md) | End-to-end RAG with LangChain |
| [patterns/vector-db-operations.md](patterns/vector-db-operations.md) | pgvector, Qdrant, hybrid search |
| [patterns/feature-engineering.md](patterns/feature-engineering.md) | Feast definitions, materialization |
| [patterns/text-to-sql.md](patterns/text-to-sql.md) | Schema-aware prompting, validation |
| [patterns/training-data-pipelines.md](patterns/training-data-pipelines.md) | DVC, labeling, bias detection |

---

## Quick Reference

- [quick-reference.md](quick-reference.md) - Fast lookup tables

## Agent Usage

| Agent | Primary Files | Use Case |
|-------|---------------|----------|
| ai-data-engineer | All files | RAG, embeddings, feature stores |
| data-quality-analyst | concepts/rag-pipelines.md | Training data quality |
