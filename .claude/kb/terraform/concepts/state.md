# State

> **Purpose**: Terraform state management, locking, and remote backends for GCP
> **Confidence**: 0.95
> **MCP Validated**: 2026-02-17

## Overview

Terraform state is the mapping between your configuration and real-world resources.
State tracks resource metadata, dependency ordering, and performance optimization
data. For teams, remote state with locking prevents concurrent modifications that
could corrupt infrastructure.

## The Pattern

```hcl
# GCS backend for remote state
terraform {
  backend "gcs" {
    bucket = "my-project-terraform-state"
    prefix = "terraform/state"
  }
}
```

## State File Structure

```text
terraform.tfstate
  version: 4
  terraform_version: "1.5.0"
  resources:
    - module: ""
      type: "google_storage_bucket"
      name: "data"
      instances:
        - attributes:
            name: "my-project-data"
            location: "US"
            ...
```

## GCS Backend Configuration

| Setting | Required | Description |
|---------|----------|-------------|
| `bucket` | Yes | GCS bucket name for state storage |
| `prefix` | No | Path prefix inside bucket |
| `encryption_key` | No | Customer-supplied encryption key |
| `impersonate_service_account` | No | SA for backend operations |

## State Locking

GCS backend provides native state locking. When one `terraform apply` is running,
another will wait or fail, preventing concurrent state corruption.

```hcl
# Locking is automatic with GCS backend
# No additional configuration needed

# Force unlock if a lock is stuck (use with caution)
# terraform force-unlock LOCK_ID
```

## State Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `terraform state list` | List all resources | Review managed resources |
| `terraform state show` | Show resource details | `terraform state show google_storage_bucket.data` |
| `terraform state mv` | Rename/move resource | `terraform state mv old_name new_name` |
| `terraform state rm` | Remove from state | `terraform state rm google_storage_bucket.old` |
| `terraform import` | Import existing resource | `terraform import google_storage_bucket.b bucket-name` |

## Import Existing Resources

```hcl
# Step 1: Write the resource block
resource "google_storage_bucket" "existing" {
  name     = "my-existing-bucket"
  location = "US"
}

# Step 2: Import (CLI)
# terraform import google_storage_bucket.existing my-existing-bucket

# Step 3 (Terraform 1.5+): Import block
import {
  to = google_storage_bucket.existing
  id = "my-existing-bucket"
}
```

## Common Mistakes

### Wrong

```hcl
# Local state -- no locking, no team sharing
# (default behavior with no backend block)
```

### Correct

```hcl
# Remote state with GCS backend
terraform {
  backend "gcs" {
    bucket = "${var.project_id}-tf-state"  # Note: variables not allowed here
    prefix = "env/${terraform.workspace}"
  }
}

# Use -backend-config for dynamic values
# terraform init -backend-config="bucket=my-project-tf-state"
```

## State Security

- Enable versioning on the state bucket for rollback
- Restrict bucket access to Terraform service accounts only
- State files contain sensitive data -- never commit to git
- Use `sensitive = true` on outputs that reference secrets

## Related

- [Providers](../concepts/providers.md)
- [Workspaces](../concepts/workspaces.md)
- [Remote State Pattern](../patterns/remote-state.md)
