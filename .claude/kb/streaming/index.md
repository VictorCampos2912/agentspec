# Streaming Knowledge Base

> **Purpose**: Stream processing — Flink, Kafka, Spark Streaming, RisingWave, Materialize, CDC
> **MCP Validated**: 2026-03-26

## Quick Navigation

### Concepts (< 150 lines each)

| File | Purpose |
|------|---------|
| [concepts/stream-processing-fundamentals.md](concepts/stream-processing-fundamentals.md) | Event time, watermarks, windowing |
| [concepts/flink-architecture.md](concepts/flink-architecture.md) | Checkpointing, state backends, Flink SQL |
| [concepts/kafka-fundamentals.md](concepts/kafka-fundamentals.md) | Topics, partitions, exactly-once, Redpanda |
| [concepts/streaming-databases.md](concepts/streaming-databases.md) | RisingWave, Materialize — streaming SQL |

### Patterns (< 200 lines each)

| File | Purpose |
|------|---------|
| [patterns/flink-sql-patterns.md](patterns/flink-sql-patterns.md) | Kafka tables, windowed agg, temporal joins |
| [patterns/kafka-producer-consumer.md](patterns/kafka-producer-consumer.md) | Idempotency, DLQ, transactions |
| [patterns/spark-streaming-patterns.md](patterns/spark-streaming-patterns.md) | foreachBatch, watermarks, stream joins |
| [patterns/cdc-patterns.md](patterns/cdc-patterns.md) | Debezium, Flink CDC, Delta CDF |

---

## Quick Reference

- [quick-reference.md](quick-reference.md) - Fast lookup tables

## Agent Usage

| Agent | Primary Files | Use Case |
|-------|---------------|----------|
| streaming-engineer | All files | Stream pipeline design |
| pipeline-architect | patterns/cdc-patterns.md | CDC pipeline orchestration |
