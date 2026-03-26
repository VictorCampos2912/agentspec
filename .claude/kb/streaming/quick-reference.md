# Streaming Quick Reference

> Fast lookup tables. For code examples, see linked files.

## Framework Comparison

| Feature | Flink | Spark Streaming | RisingWave | Materialize | Kafka Streams |
|---------|-------|----------------|------------|-------------|---------------|
| Latency | ms | seconds (RT: ms) | ms | ms | ms |
| Language | Java/SQL | Python/SQL | SQL (Postgres) | SQL | Java |
| State mgmt | RocksDB/heap | In-memory/disk | S3-native | Differential | RocksDB |
| Exactly-once | Yes | Yes | Yes | Yes | Yes |
| SQL support | Flink SQL | Spark SQL | Full Postgres | Full Postgres | ksqlDB |
| Complexity | High | Medium | Low | Low | Medium |
| Best for | Complex event processing | Unified batch+stream | Streaming SQL apps | Materialized views | Kafka-native |

## Windowing Types

| Window | Behavior | Use Case |
|--------|----------|----------|
| Tumbling | Fixed, non-overlapping | Hourly aggregations |
| Sliding (Hop) | Fixed, overlapping | Moving averages |
| Session | Gap-based, dynamic | User session analysis |
| Global | Single window | All-time aggregations |

## Exactly-Once Comparison

| Engine | Mechanism |
|--------|-----------|
| Flink | Chandy-Lamport checkpointing |
| Kafka | Idempotent producer + transactions |
| Spark | Write-ahead log + idempotent sinks |
| RisingWave | Internal consistency (no external coordination) |

## Common Pitfalls

| Don't | Do |
|-------|-----|
| Process-time when event-time needed | Use event-time + watermarks |
| Unbounded state | Set state TTL, use watermarks |
| No dead letter queue | Route bad records to DLQ |
| Skip checkpointing | Enable checkpointing every 1-5 min |
| Process everything as stream | Use batch for historical backfill |
