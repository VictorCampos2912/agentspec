# Resources

> **Purpose**: Terraform resource blocks for declaring and managing GCP infrastructure
> **Confidence**: 0.95
> **MCP Validated**: 2026-02-17

## Overview

Resources are the most important element in Terraform. Each resource block declares
a single infrastructure object that Terraform manages throughout its lifecycle.
Resources map directly to GCP API objects and support create, read, update, and delete operations.

## The Pattern

```hcl
resource "google_cloud_run_v2_service" "api" {
  name     = "${var.project_prefix}-api"
  location = var.region
  ingress  = "INGRESS_TRAFFIC_ALL"

  template {
    containers {
      image = var.container_image

      resources {
        limits = {
          cpu    = "1000m"
          memory = "512Mi"
        }
      }

      env {
        name  = "PROJECT_ID"
        value = var.project_id
      }
    }

    scaling {
      min_instance_count = 0
      max_instance_count = 10
    }
  }

  lifecycle {
    ignore_changes = [
      template[0].containers[0].image,
    ]
  }
}
```

## Meta-Arguments

| Argument | Purpose | Example |
|----------|---------|---------|
| `depends_on` | Explicit dependency | `depends_on = [google_project_service.run]` |
| `count` | Conditional creation | `count = var.enable_feature ? 1 : 0` |
| `for_each` | Multiple instances from map | `for_each = var.services` |
| `lifecycle` | Control create/destroy behavior | `prevent_destroy = true` |
| `provider` | Non-default provider | `provider = google.us_east` |

## Lifecycle Rules

```hcl
resource "google_storage_bucket" "data" {
  name     = "${var.project_id}-data"
  location = var.region

  lifecycle {
    prevent_destroy = true   # Block accidental deletion
  }
}

resource "google_bigquery_table" "events" {
  dataset_id = google_bigquery_dataset.main.dataset_id
  table_id   = "events"

  lifecycle {
    create_before_destroy = true  # Zero-downtime replacement
    ignore_changes        = [schema]  # Schema managed externally
  }
}
```

## Common Mistakes

### Wrong

```hcl
# Hardcoded values, no naming convention
resource "google_storage_bucket" "bucket" {
  name     = "my-bucket"
  location = "US"
}
```

### Correct

```hcl
# Parameterized with consistent naming
resource "google_storage_bucket" "data_lake" {
  name     = "${var.project_id}-${var.environment}-data-lake"
  location = var.region

  uniform_bucket_level_access = true
  force_destroy               = var.environment != "prod"

  versioning {
    enabled = true
  }
}
```

## Data Sources

```hcl
# Read existing resources without managing them
data "google_project" "current" {}

data "google_service_account" "existing" {
  account_id = "my-service-account"
}
```

## Related

- [Modules](../concepts/modules.md)
- [Variables](../concepts/variables.md)
- [Cloud Run Module](../patterns/cloud-run-module.md)
