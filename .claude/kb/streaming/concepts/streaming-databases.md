# Streaming Databases

> **Purpose**: RisingWave, Materialize — streaming SQL databases for continuous aggregation
> **Confidence**: 0.85
> **MCP Validated**: 2026-03-26

## Overview

Streaming databases (RisingWave, Materialize) run SQL queries continuously over event streams, maintaining materialized views that update in real-time. Unlike Flink/Spark Streaming which process in micro-batches or event-at-a-time, streaming databases feel like Postgres — you write SQL and it stays current.

## The Concept

```sql
-- RisingWave: Postgres-compatible streaming SQL
-- Connect to Kafka source
CREATE SOURCE kafka_events (
    event_id    VARCHAR,
    user_id     VARCHAR,
    event_type  VARCHAR,
    amount      DECIMAL,
    event_ts    TIMESTAMPTZ
) WITH (
    connector = 'kafka',
    topic = 'raw_events',
    properties.bootstrap.server = 'kafka:9092',
    scan.startup.mode = 'earliest'
) FORMAT PLAIN ENCODE JSON;

-- Materialized view: auto-updates as events arrive
CREATE MATERIALIZED VIEW mv_revenue_per_minute AS
SELECT
    date_trunc('minute', event_ts) AS minute_window,
    event_type,
    COUNT(*) AS event_count,
    SUM(amount) AS total_revenue,
    COUNT(DISTINCT user_id) AS unique_users
FROM kafka_events
WHERE event_type = 'purchase'
GROUP BY 1, 2;

-- Query like Postgres (always fresh)
SELECT * FROM mv_revenue_per_minute
WHERE minute_window > NOW() - INTERVAL '1 hour'
ORDER BY minute_window DESC;
```

## Quick Reference

| Feature | RisingWave | Materialize | Flink SQL |
|---------|-----------|------------|-----------|
| SQL dialect | PostgreSQL | PostgreSQL | Flink SQL (ANSI+) |
| Deployment | Cloud / self-hosted | Cloud-only (SaaS) | Self-hosted / managed |
| Source | Kafka, Postgres CDC, S3 | Kafka, Postgres CDC | Kafka, files, JDBC |
| Sink | Kafka, JDBC, S3, Iceberg | Kafka, Postgres, webhooks | Kafka, JDBC, Iceberg |
| State backend | Hummock (LSM on S3) | Differential dataflow | RocksDB |
| Best for | Wide SQL compat, cost-efficient | Complex joins/aggregations | Full streaming ecosystem |
| Latency | Sub-second | Sub-second | Milliseconds (per-event) |
| Cost model | Compute-based | Consumption-based | Cluster-based |

## Common Mistakes

### Wrong

```sql
-- Running complex windowed aggregation in application code
-- Polling database every second for updated counts
SELECT COUNT(*) FROM events WHERE event_ts > NOW() - INTERVAL '5 min';
```

### Correct

```sql
-- Let the streaming database maintain the result continuously
CREATE MATERIALIZED VIEW live_counts AS
SELECT COUNT(*) AS recent_events
FROM kafka_events
WHERE event_ts > NOW() - INTERVAL '5 minutes';
-- Query is always pre-computed, sub-millisecond reads
```

## Related

- [stream-processing-fundamentals](stream-processing-fundamentals.md)
- [flink-architecture](flink-architecture.md)
- [flink-sql-patterns](../patterns/flink-sql-patterns.md)
