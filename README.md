# Terraform Cloud Sentinal policy
How you would implement a Terraform Cloud Sentinel to ensure that all created GCS buckets have a specific label (e.g., `cost-center`). 

Psuedocode

```hcl
# Sentinel policy to enforce that each GCS bucket has the label "cost-center"
import "tfplan/v2" as tfplan
import "strings"

# Get all resources of type google_storage_bucket
gcs_buckets = filter tfplan.resource_changes as rc {
    rc.type is "google_storage_bucket" and
    (rc.change.actions contains "create" or rc.change.actions contains "update")
}

# Evaluate if all buckets have the "cost-center" label
valid_labels = all gcs_buckets as bucket {
    labels = bucket.change.after.labels
    labels is not null and "cost-center" in labels
}

main = rule {
    valid_labels
}

```

How to use:

1. Add this policy to your Sentinel Policy Set or OPA Policy Set in Terraform Cloud.
2. Attach it to your workspace(s).
3. Terraform Cloud will now enforce this check during terraform plan.

