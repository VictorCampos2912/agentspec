# Embedding Pipelines

> **Purpose**: Model selection, batch vs streaming embedding, Matryoshka embeddings, versioning and drift
> **Confidence**: 0.90
> **MCP Validated**: 2026-03-26

## Overview

Embedding pipelines convert unstructured data (text, images, code) into dense vector representations for downstream retrieval and ML tasks. Key decisions include model selection (OpenAI vs Cohere vs open-source), execution mode (batch for bulk ingestion vs streaming for real-time), dimensionality (Matryoshka embeddings allow variable dimensions from a single model), and lifecycle management (versioning, drift detection, re-embedding strategies).

## The Concept

```python
# Production embedding pipeline with batching, retries, and versioning
import numpy as np
from openai import OpenAI
from tenacity import retry, stop_after_attempt, wait_exponential

client = OpenAI()
EMBEDDING_MODEL = "text-embedding-3-large"
EMBEDDING_VERSION = "v3-large-2024"
DIMENSIONS = 1024  # Matryoshka: 256, 512, 1024, or 3072

@retry(stop=stop_after_attempt(3), wait=wait_exponential(min=1, max=10))
def embed_batch(texts: list[str], dimensions: int = DIMENSIONS) -> list[list[float]]:
    """Embed a batch of texts with automatic retry and dimension control."""
    response = client.embeddings.create(
        model=EMBEDDING_MODEL,
        input=texts,
        dimensions=dimensions,  # Matryoshka truncation
    )
    return [item.embedding for item in response.data]

def embed_documents(documents: list[dict], batch_size: int = 100) -> list[dict]:
    """Batch-embed documents with metadata tracking."""
    results = []
    for i in range(0, len(documents), batch_size):
        batch = documents[i:i + batch_size]
        texts = [doc["content"] for doc in batch]
        vectors = embed_batch(texts)
        for doc, vector in zip(batch, vectors):
            results.append({
                **doc,
                "embedding": vector,
                "embedding_model": EMBEDDING_MODEL,
                "embedding_version": EMBEDDING_VERSION,
                "dimensions": DIMENSIONS,
            })
    return results
```

```text
Matryoshka Embeddings (Variable Dimensions)

  Full model output: 3072 dimensions

  [||||||||||||||||||||||||||||||||] 3072d -- max quality, 12KB/vector
  [||||||||||||||||]                 1024d -- 95% quality, 4KB/vector
  [||||||||]                          512d -- 92% quality, 2KB/vector
  [||||]                              256d -- 88% quality, 1KB/vector

  Same model, truncate output to desired dimension.
  Trade storage/speed for marginal quality loss.
```

## Quick Reference

| Model | Provider | Dimensions | Context | Matryoshka | Cost (per 1M tokens) |
|-------|----------|-----------|---------|------------|---------------------|
| text-embedding-3-large | OpenAI | 3072 (truncatable) | 8191 | Yes | $0.13 |
| text-embedding-3-small | OpenAI | 1536 (truncatable) | 8191 | Yes | $0.02 |
| embed-v3 | Cohere | 1024 | 512 | No | $0.10 |
| BGE-large-en-v1.5 | BAAI (OSS) | 1024 | 512 | No | Free (self-host) |
| Nomic embed-text-v1.5 | Nomic (OSS) | 768 | 8192 | Yes | Free (self-host) |
| GTE-large-en-v1.5 | Alibaba (OSS) | 1024 | 8192 | No | Free (self-host) |

| Mode | Latency | Throughput | Use Case |
|------|---------|-----------|----------|
| Batch (API) | Minutes | 3000+ docs/min | Bulk ingestion, backfills |
| Batch (local GPU) | Minutes | 10000+ docs/min | Privacy-sensitive, high volume |
| Streaming | Real-time | 1 doc/request | New document ingestion, chat |
| Cached | < 1ms | N/A | Repeated queries, hot content |

## Common Mistakes

### Wrong

```python
# Embedding with no version tracking -- impossible to detect drift
vectors = embed(texts)
db.upsert(ids=ids, embeddings=vectors)
# 6 months later: which model version created these vectors?

# Mixing embedding models in the same collection
# OpenAI (1536d) and Cohere (1024d) vectors are incompatible!
```

### Correct

```python
# Track embedding model + version + dimensions per vector
db.upsert(
    ids=ids,
    embeddings=vectors,
    metadatas=[{
        "embedding_model": "text-embedding-3-large",
        "embedding_version": "v3-large-2024",
        "dimensions": 1024,
        "embedded_at": "2026-03-26T00:00:00Z",
    } for _ in ids]
)

# Drift detection: compare new embeddings to stored centroid
centroid = np.mean(stored_vectors, axis=0)
new_centroid = np.mean(new_vectors, axis=0)
drift_score = 1 - np.dot(centroid, new_centroid) / (
    np.linalg.norm(centroid) * np.linalg.norm(new_centroid)
)
if drift_score > 0.05:
    alert("Embedding drift detected -- consider re-embedding")
```

## Related

- [rag-pipelines](../concepts/rag-pipelines.md)
- [vector-databases](../concepts/vector-databases.md)
- [rag-pipeline-implementation](../patterns/rag-pipeline-implementation.md)
