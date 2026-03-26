# AI Data Engineering Quick Reference

> Fast lookup tables. For code examples, see linked files.

## Vector DB Comparison

| Feature | pgvector | Qdrant | Pinecone | Weaviate | LanceDB |
|---------|----------|--------|----------|----------|---------|
| Type | Extension | Standalone | Managed | Standalone | Embedded |
| Language | SQL | Rust | API-only | Go | Rust |
| Hybrid search | BM25 (pg_search) | Built-in | Built-in | Native | Built-in |
| Metadata filter | SQL WHERE | Payload filter | Filter API | GraphQL | SQL |
| Max scale | ~10M vectors | Billions | Billions | Billions | Billions |
| Best for | Postgres teams | Complex RAG | Zero-ops | Hybrid search | Embedded/local |
| Cost | Free (self-host) | Free (OSS) | Pay-per-use | Free (OSS) | Free (OSS) |

## Feature Store Comparison

| Feature | Feast | Tecton | Databricks FS |
|---------|-------|--------|--------------|
| License | OSS (Apache 2.0) | Commercial | Databricks-only |
| Online serving | Redis, DynamoDB | Managed (<10ms) | Databricks |
| Offline store | Any (Parquet, BQ) | Managed | Delta Lake |
| Streaming features | Limited | Full | Full |
| Best for | OSS, flexible | Enterprise, mission-critical | Databricks shops |

## Embedding Model Selection (2026)

| Model | Dimensions | Context | Speed | Quality |
|-------|-----------|---------|-------|---------|
| OpenAI text-embedding-3-large | 3072 (or less via Matryoshka) | 8191 tokens | Fast (API) | Best |
| Cohere embed-v3 | 1024 | 512 tokens | Fast (API) | Excellent |
| BGE-large-en-v1.5 | 1024 | 512 tokens | Self-host | Very good |
| Nomic embed-text-v1.5 | 768 | 8192 tokens | Self-host | Good |

## RAG Pipeline Checklist

| Step | Component | Key Decision |
|------|-----------|-------------|
| 1. Load | Document loader | PDF, HTML, Markdown parser |
| 2. Chunk | Splitter | Fixed (512 tokens) vs recursive vs semantic |
| 3. Embed | Embedding model | OpenAI vs open-source, dimensions |
| 4. Store | Vector DB | pgvector vs Qdrant vs managed |
| 5. Retrieve | Search strategy | Dense, sparse (BM25), or hybrid |
| 6. Rerank | Reranker | Cohere Rerank, cross-encoder, none |
| 7. Generate | LLM | Claude, GPT-4, open-source |

## Common Pitfalls

| Don't | Do |
|-------|-----|
| Single embedding model for all content | Match model to content type |
| No chunk overlap | 10-20% overlap for context preservation |
| Skip metadata in vector store | Store source, page, section with vectors |
| Stale embeddings | Re-embed on source updates |
| No eval pipeline | Measure retrieval precision/recall |
