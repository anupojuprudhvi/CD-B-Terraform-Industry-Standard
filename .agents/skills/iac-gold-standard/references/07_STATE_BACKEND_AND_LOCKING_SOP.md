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

## 2. Stuck Lock Recovery Runbook

If a deployment is interrupted and returns `Error: Error acquiring the state lock`:

```bash
# 1. Identify the Lock ID from the error message
# Example: Lock Info: ID: 52a129d2-9b5b-4c0a-845a-8dfa12b4e4a2

# 2. Force unlock state
terraform force-unlock <LOCK_ID>

# 3. Re-run plan to confirm state integrity
terraform plan -var-file=prod.tfvars
```
