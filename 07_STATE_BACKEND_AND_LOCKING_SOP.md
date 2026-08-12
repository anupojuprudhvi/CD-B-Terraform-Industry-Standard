# 07 - Terraform Remote State Backend & Native Locking SOP

This standard operating procedure (SOP) defines the backend S3 configuration rules, native state locking (`use_lockfile = true`), and stuck lock recovery procedures.

---

## 1. Remote State Backend Standards

In Terraform >= 1.10.0, state locking MUST use **Native S3 State Locking** (`use_lockfile = true`), eliminating the need for DynamoDB lock tables.

### Backend Configuration Pattern (`backend-prod.hcl`):

```hcl
bucket       = "clientx-prod-tfstate-641079926828"
key          = "apps/prod/terraform.tfstate"
region       = "us-east-2"
encrypt      = true
use_lockfile = true
```

---

## 2. Mandatory S3 Backend Security Rules

1. **Server-Side Encryption:** S3 bucket must be encrypted using Customer Managed KMS Key (CMK) or `aws:kms`.
2. **Versioning:** S3 state bucket versioning MUST be enabled (`status = "Enabled"`).
3. **Public Access Block:** `block_public_acls = true`, `block_public_policy = true`, `ignore_public_acls = true`, `restrict_public_buckets = true`.
4. **MFA Delete:** Recommended for production state buckets.

---

## 3. Stuck Lock Recovery Runbook

If a deployment is interrupted and returns `Error: Error acquiring the state lock`:

```bash
# 1. Identify the Lock ID from the error message
# Example: Lock Info: ID: 52a129d2-9b5b-4c0a-845a-8dfa12b4e4a2

# 2. Verify no active CI/CD pipeline or teammate is currently applying
# 3. Force unlock state
terraform force-unlock <LOCK_ID>

# 4. Re-run plan to confirm state integrity
terraform plan -var-file=prod.tfvars
```
