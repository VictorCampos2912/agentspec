# Catalog Wars

> **Purpose**: Catalog comparison — Unity Catalog, Gravitino, Nessie, Polaris, decision matrix
> **Confidence**: 0.90
> **MCP Validated**: 2026-03-26

## Overview

Data catalogs manage metadata, access control, and table discovery across lakehouse engines. The "catalog wars" reflects the competition between vendor-backed catalogs (Unity, Polaris) and open-source alternatives (Gravitino, Nessie). Choice depends on ecosystem lock-in tolerance, multi-engine needs, and governance requirements.

## The Concept

```yaml
# Gravitino: Apache multi-engine catalog (YAML config)
gravitino:
  server:
    host: 0.0.0.0
    port: 8090
  catalogs:
    - name: lakehouse
      type: iceberg
      properties:
        catalog-backend: rest
        uri: http://rest-catalog:8181
        warehouse: s3://my-bucket/warehouse
    - name: hive_legacy
      type: hive
      properties:
        metastore.uris: thrift://hive-metastore:9083
```

```sql
-- Nessie: Git-like branching for data lakes
-- Create a branch for development
CREATE BRANCH dev_feature IN nessie;

-- Work on the branch (isolated from main)
USE REFERENCE dev_feature IN nessie;
INSERT INTO orders VALUES ('new-order', 'cust-1', '2026-03-26', 99.99);

-- Merge when ready (atomic)
MERGE BRANCH dev_feature INTO main IN nessie;
```

## Quick Reference

| Catalog | Backed By | Format Support | Open Source | Multi-Engine | RBAC |
|---------|----------|---------------|------------|-------------|------|
| Unity Catalog | Databricks | Delta, Iceberg (UniForm) | OSS fork | Limited (Spark, some Trino) | Full |
| Polaris | Snowflake | Iceberg (REST) | Yes (Apache) | Spark, Trino, Flink | Full |
| Gravitino | Apache | Iceberg, Hive, JDBC | Yes | Spark, Trino, Flink, Presto | Basic |
| Nessie | Dremio | Iceberg | Yes | Spark, Trino, Flink | Git-based |
| Hive Metastore | Apache | Hive, Iceberg (adapter) | Yes | All | Basic |
| AWS Glue | AWS | Iceberg, Delta, Hive | No | Spark, Trino, Athena | IAM |

| Decision Factor | Weight | Best Option |
|----------------|--------|-------------|
| Multi-engine flexibility | High | Gravitino, Polaris |
| Git-like branching | High | Nessie |
| Databricks ecosystem | High | Unity Catalog |
| Snowflake + Iceberg | High | Polaris |
| Minimal vendor lock-in | High | Gravitino, Nessie |
| Managed service | Medium | AWS Glue, Unity (Databricks-managed) |

## Common Mistakes

### Wrong

```text
Using Hive Metastore as the long-term catalog for Iceberg tables.
HMS lacks: fine-grained RBAC, partition evolution awareness, REST API standard.
```

### Correct

```text
Migrate to REST Catalog (Polaris, Gravitino) for production Iceberg.
Keep HMS as a read-only bridge during migration for legacy Hive consumers.
```

## Related

- [iceberg-v3](iceberg-v3.md)
- [delta-lake](delta-lake.md)
- [catalog-setup pattern](../patterns/catalog-setup.md)
