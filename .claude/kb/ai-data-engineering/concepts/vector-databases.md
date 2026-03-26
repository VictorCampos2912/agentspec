# Vector Databases

> **Purpose**: ANN algorithms, distance metrics, metadata filtering, and platform comparison
> **Confidence**: 0.90
> **MCP Validated**: 2026-03-26

## Overview

Vector databases are purpose-built for storing and searching high-dimensional embeddings using approximate nearest neighbor (ANN) algorithms. The choice of index type (HNSW vs IVF-PQ), distance metric (cosine vs L2 vs dot product), and metadata filtering strategy determines retrieval latency, recall, and memory footprint. Production systems typically combine vector similarity with metadata pre-filtering for relevant, scoped results.

## The Concept

```text
ANN Index Algorithms

HNSW (Hierarchical Navigable Small World)
==========================================
Layer 3:  A ─────────────── D              (few nodes, long-range links)
Layer 2:  A ──── C ──── D ──── F          (more nodes, medium links)
Layer 1:  A ─ B ─ C ─ D ─ E ─ F ─ G      (all nodes, short links)

- Search: start at top layer, greedily descend
- Build time: O(N log N)  |  Query: O(log N)
- Memory: HIGH (in-memory graph)
- Best for: low-latency serving (<10ms), datasets < 50M vectors

IVF-PQ (Inverted File + Product Quantization)
=============================================
Step 1 - IVF: Partition vectors into K clusters (Voronoi cells)
Step 2 - PQ:  Compress each vector into sub-quantized codes

  [Full Vector 768d] --> [8 sub-vectors x 96d] --> [8 centroid IDs]
  Memory: 768 x 4B = 3KB  -->  8 x 1B = 8 bytes (375x compression)

- Search: probe nprobe nearest clusters, scan PQ codes
- Build time: O(N)  |  Query: O(N/K * nprobe)
- Memory: LOW (quantized on disk)
- Best for: billion-scale, cost-sensitive, batch retrieval
```

## Quick Reference

| Distance Metric | Formula | Range | Use Case |
|-----------------|---------|-------|----------|
| Cosine similarity | 1 - (A . B) / (\|A\| \|B\|) | [0, 2] | Text embeddings (normalized) |
| L2 (Euclidean) | sqrt(sum((a-b)^2)) | [0, inf) | Image embeddings, spatial data |
| Dot product | A . B | (-inf, inf) | Pre-normalized, max inner product |

| Platform | Index Types | Hybrid Search | Managed | Best For |
|----------|-------------|---------------|---------|----------|
| **pgvector** | HNSW, IVF | BM25 via pg_search | Self-host | Postgres teams, < 10M vectors |
| **Qdrant** | HNSW + quantization | Built-in sparse vectors | Cloud + OSS | Complex filtering, multi-tenancy |
| **Pinecone** | Proprietary | Sparse-dense | Fully managed | Zero-ops, fast prototyping |
| **Weaviate** | HNSW + PQ | Native BM25 + vector | Cloud + OSS | GraphQL API, hybrid search |
| **LanceDB** | IVF-PQ, DiskANN | Built-in | Embedded | Local-first, serverless |

## Common Mistakes

### Wrong

```python
# Using L2 distance with non-normalized embeddings for text search
# L2 penalizes magnitude, not just direction -- wrong for text
index = faiss.IndexFlatL2(768)
index.add(embeddings)  # embeddings not normalized!

# No metadata filtering -- scanning entire index for tenant-specific query
results = collection.search(query_vector, limit=10)
# Returns results from ALL tenants -- data leak!
```

### Correct

```python
# Normalize embeddings, use cosine (or IP after normalization)
import numpy as np
embeddings = embeddings / np.linalg.norm(embeddings, axis=1, keepdims=True)
index = faiss.IndexFlatIP(768)  # inner product = cosine after normalization
index.add(embeddings)

# Always scope search with metadata filters
results = collection.search(
    query_vector,
    limit=10,
    query_filter=Filter(
        must=[FieldCondition(key="tenant_id", match=MatchValue(value="acme"))]
    )
)
```

## Related

- [rag-pipelines](../concepts/rag-pipelines.md)
- [embedding-pipelines](../concepts/embedding-pipelines.md)
- [vector-db-operations](../patterns/vector-db-operations.md)
