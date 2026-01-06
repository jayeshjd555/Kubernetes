# Multiple Buckets Support for GitHub Actions Cache S3

## Overview

Update the infrastructure to support multiple S3 buckets for GitHub Actions cache, where:

- `is_enabled` remains at the top level (s3 level)
- `iam_user.is_enabled` is shared across all buckets
- Each bucket only needs `bucket_name` in a map structure
- IAM policy grants access to ALL buckets
- Each bucket gets its own bucket policy

## File Changes Required

### 1. `shared_variables.tf` (Lines 57-64)

**Change the variable structure:**

```hcl
# FROM:
github-actions = object({
  s3 = object({
    is_enabled  = bool
    bucket_name = string
    iam_user = object({
      is_enabled = bool
    })
  })
})

# TO:
github-actions = object({
  s3 = object({
    is_enabled = bool
    iam_user = object({
      is_enabled = bool
    })
    buckets = map(object({
      bucket_name = string
    }))
  })
})
```

### 2. `feature_toggles.tf`

**Update `enabled_deployments` to iterate over buckets:**

```hcl
# FROM:
locals {
  enabled_deployments = toset(compact([
    var.shared-services["github-actions"].s3.is_enabled ? var.region : ""
  ]))
  
  enabled_iam_deployments = toset(compact([
    var.shared-services["github-actions"].s3.iam_user.is_enabled ? var.region : ""
  ]))
}

# TO:
locals {
  # Iterate over buckets when s3 is enabled
  enabled_deployments = var.shared-services["github-actions"].s3.is_enabled ? 
    var.shared-services["github-actions"].s3.buckets : {}
  
  # Keep region-based for IAM user (created once per region)
  enabled_iam_deployments = toset(compact([
    var.shared-services["github-actions"].s3.iam_user.is_enabled ? var.region : ""
  ]))
}
```

**Note:** `enabled_deployments` now returns the buckets map instead of regions. This is used directly in `s3.tf` for iteration.

### 3. `s3.tf`

**Update all resources to iterate over buckets using `enabled_deployments`:**

```hcl
# Line 2: for_each already uses local.enabled_deployments (no change needed)
# for_each = local.enabled_deployments

# Line 7: Change bucket name reference
# FROM: bucket = var.shared-services["github-actions"].s3.bucket_name
# TO:   bucket = each.value.bucket_name

# Line 67: for_each already uses local.enabled_deployments (no change needed)
# for_each = local.enabled_deployments

# Lines 90-91: Update resources to reference current bucket
# FROM:
#   resources = [
#     "${module.github_actions_cache[var.region].s3_bucket_arn}",
#     "${module.github_actions_cache[var.region].s3_bucket_arn}/*"
#   ]
# TO:
#   resources = [
#     module.github_actions_cache[each.key].s3_bucket_arn,
#     "${module.github_actions_cache[each.key].s3_bucket_arn}/*"
#   ]

# Line 99: for_each already uses local.enabled_deployments (no change needed)
# for_each = local.enabled_deployments

# Line 101: Update bucket reference
# FROM: bucket = module.github_actions_cache[var.region].s3_bucket_id
# TO:   bucket = module.github_actions_cache[each.key].s3_bucket_id

# Line 102: Update policy reference
# FROM: policy = data.aws_iam_policy_document.github_actions_cache_s3[var.region].json
# TO:   policy = data.aws_iam_policy_document.github_actions_cache_s3[each.key].json
```

### 4. `iam_user.tf`

**Update IAM policy to include ALL bucket ARNs:**

```hcl
# Lines 26-43: Update IAM policy document
# FROM:
data "aws_iam_policy_document" "s3" {
  for_each = local.enabled_iam_deployments
  statement {
    actions = [...]
    resources = [
      "${module.github_actions_cache[var.region].s3_bucket_arn}",
      "${module.github_actions_cache[var.region].s3_bucket_arn}/*"
    ]
    effect = "Allow"
  }
}

# TO:
data "aws_iam_policy_document" "s3" {
  for_each = local.enabled_iam_deployments
  statement {
    actions = [
      "s3:PutObject",
      "s3:GetObject",
      "s3:ListBucket",
      "s3:GetBucketLocation",
      "s3:ListBucketMultipartUploads",
      "s3:ListMultipartUploadParts"
    ]
    resources = concat(
      [for bucket_key, bucket in module.github_actions_cache : bucket.s3_bucket_arn],
      [for bucket_key, bucket in module.github_actions_cache : "${bucket.s3_bucket_arn}/*"]
    )
    effect = "Allow"
  }
}
```

**Note:** The `for_each` in the IAM policy document is still region-based (one policy per region), but the resources array now includes all buckets created by the module.

### 5. `config/shared-services/prd.us-east-2.tfvars.json` (Example)

**Update JSON configuration:**

```json
# FROM:
"github-actions": {
  "s3": {
    "is_enabled": true,
    "bucket_name": "github-actions-cache-backend",
    "iam_user": {
      "is_enabled": true
    }
  }
}

# TO:
"github-actions": {
  "s3": {
    "is_enabled": true,
    "iam_user": {
      "is_enabled": true
    },
    "buckets": {
      "cache-backend": {
        "bucket_name": "github-actions-cache-backend"
      },
      "cache-frontend": {
        "bucket_name": "github-actions-cache-frontend"
      }
    }
  }
}
```

## Summary of Changes

1. **Variable Structure**: Change from single bucket object to map of buckets
2. **Feature Toggles**: Update `enabled_deployments` to return buckets map instead of regions
3. **S3 Resources**: Update bucket name reference and all bucket references from `var.region` to `each.key`
4. **IAM Policy**: Collect all bucket ARNs dynamically using `for` expressions
5. **Bucket Policies**: Each bucket gets its own policy (already per-bucket with `for_each`)

## Key Points

- **`enabled_deployments` in `feature_toggles.tf` now returns buckets map** (when `s3.is_enabled` is true)
- No new locals needed - reuse existing `enabled_deployments` in `s3.tf`
- IAM user is created once per region (unchanged via `enabled_iam_deployments`)
- IAM policy now grants access to ALL buckets dynamically
- Each bucket gets its own S3 bucket policy
- All bucket references use `each.key` instead of `var.region`

