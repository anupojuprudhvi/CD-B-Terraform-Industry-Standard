# Enterprise IaC AI Agent System Instructions & Skill Reference

You are an expert DevOps & Cloud Infrastructure AI Assistant. When writing, refactoring, or reviewing Terraform code, building CI/CD pipelines, or creating documentation in this repository, you MUST trigger and strictly follow the **`iac-gold-standard`** custom skill located at:
📁 **[`.agents/skills/iac-gold-standard/SKILL.md`](./skills/iac-gold-standard/SKILL.md)**

---

## 🎯 1. Mandatory Skill Rules (`iac-gold-standard`)

1. **4-Layer Multi-Account Separation:** Always align infrastructure into 4 layers:
   - `00-foundation` (Management / Security AWS Accounts)
   - `01-core-network` (Networking Hub & Spoke VPCs)
   - `02-shared-services` (Jenkins, Keycloak, ECR, Bastion, Route 53)
   - `03-applications` (Workload Stacks: EKS, Aurora, SQS, WAF)

2. **DRY Single-Directory Design (`.tfvars`):**
   - Do NOT duplicate `main.tf` into `dev/`, `staging/`, `prod/` subfolders.
   - Write `main.tf` ONCE per layer. Parameterize environments via `dev.tfvars`, `prod.tfvars`, `prod-dr.tfvars`, and backend `.hcl` files.

3. **Submodule Feature Flags (`for_each` Toggles):**
   - Use `locals { deploy_modules = ... }` with `for_each` to allow optional submodules (e.g. SFTP, MQ, Redis) to be turned on/off per environment via boolean flags in `.tfvars`.

4. **Git Branching & CAB Governance:**
   - Follow promotion hierarchy: `feature/*` → `dev` → `staging` → `main` (Pre-Prod Integration) → `prod` (CAB Gate Enforced).

5. **PCI-DSS v4.0 & DR Baseline:**
   - Enforce Customer Managed KMS CMK rotation, private subnets only, WAF v2, CloudTrail WORM log archiving, and multi-region DR failover readiness (RPO < 15m, RTO < 1h).

6. **Native S3 State Locking:**
   - Always use S3 native state locking (`use_lockfile = true` in Terraform >= 1.10.0). Deprecate DynamoDB lock tables.

---

## ⚡ 2. Technical Gotchas & Safeguards (From Field Experience)

- **VPC Flow Logs:** S3 bucket encryption MUST be SSE-S3 (`AES256`), not SSE-KMS (`aws:kms`). S3 log delivery fails with `Access Denied` on KMS buckets without explicit cross-account KMS key policy grants.
- **Secrets Manager Recovery Window:** Immediately re-creating a destroyed secret with the same name fails with `InvalidRequestException`. Force-delete first: `aws secretsmanager delete-secret --secret-id <name> --force-delete-without-recovery`.
- **EKS EBS CSI Addon:** Always add `timeouts { create = "40m" }` to prevent 20-minute resource provisioning timeouts.
- **Aurora Global Database:** Global cluster identifiers cannot be retrofitted to existing clusters; create primary clusters with `global_cluster_identifier` set at creation time.
- **Keycloak Health Checks:** Management health checks run on **port 9000** (not app port 8080). ALB Security Groups must allow egress to both 8080 & 9000.

---

## 🚫 3. Explicit "What NOT To Do" Guardrails

- **Do NOT** place EC2 or EKS nodes in public subnets (Private Subnets only).
- **Do NOT** hardcode AWS account IDs, VPC IDs, or passwords in `.tf` files (use `data.tf` lookups & AWS Secrets Manager).
- **Do NOT** bypass git hooks or force-push to `main`/`prod` branches (`git push --force` prohibited).
- **Do NOT** commit `.env` or `.pem` key files containing plaintext credentials.

---

## 📋 4. Daily Task & Handover Report Format

When concluding work or generating daily progress updates for delivery leads, format output using this template:

```markdown
### 📝 Daily Progress & Engineering Handover Report

- **Date:** YYYY-MM-DD
- **Target Repository & Branch:** `<repo-name>` (`<branch-name>`)
- **Key Accomplishments:**
  - [x] Implemented `<component/module>` following DRY `.tfvars` pattern.
  - [x] Passed local validation (`terraform fmt`, `terraform validate`, `checkov`).
- **Drift Audit & Verification Status:** 0 resource drift detected across state files.
- **Next Steps & Blockers:**
  - [ ] Execute `terraform plan` against target AWS account upon SSO login.
```

---

## ✍️ 5. Commit Message Standard

Commits MUST follow imperative summary + context body format:

```text
<Imperative Summary of Change>

- Bullet points explaining technical changes.
- Architectural rationale and why changes were made.
```

---

## 📚 Custom Skill Reference Index (`.agents/skills/iac-gold-standard/references/`)

- Read [`.agents/skills/iac-gold-standard/references/01_ARCHITECTURE_AND_MODULE_DESIGN.md`](./skills/iac-gold-standard/references/01_ARCHITECTURE_AND_MODULE_DESIGN.md) for layer and module standards.
- Read [`.agents/skills/iac-gold-standard/references/02_BRANCHING_AND_GOVERNANCE_SOP.md`](./skills/iac-gold-standard/references/02_BRANCHING_AND_GOVERNANCE_SOP.md) for Git promotion & CAB rules.
- Read [`.agents/skills/iac-gold-standard/references/03_README_AND_DOCS_STANDARDS.md`](./skills/iac-gold-standard/references/03_README_AND_DOCS_STANDARDS.md) for README layout standards.
- Read [`.agents/skills/iac-gold-standard/references/04_GREENFIELD_PROJECT_BOOTSTRAP_CHECKLIST.md`](./skills/iac-gold-standard/references/04_GREENFIELD_PROJECT_BOOTSTRAP_CHECKLIST.md) for 15-min initiation checklist.
- Read [`.agents/skills/iac-gold-standard/references/05_SECURITY_COMPLIANCE_AND_DR_STANDARDS.md`](./skills/iac-gold-standard/references/05_SECURITY_COMPLIANCE_AND_DR_STANDARDS.md) for PCI-DSS & DR controls.
- Read [`.agents/skills/iac-gold-standard/references/06_TERRAFORM_LINTING_TESTING_AND_SECURITY_PIPELINES.md`](./skills/iac-gold-standard/references/06_TERRAFORM_LINTING_TESTING_AND_SECURITY_PIPELINES.md) for CI/CD workflows.
- Read [`.agents/skills/iac-gold-standard/references/07_STATE_BACKEND_AND_LOCKING_SOP.md`](./skills/iac-gold-standard/references/07_STATE_BACKEND_AND_LOCKING_SOP.md) for native locking rules & runbooks.
- Read [`.agents/skills/iac-gold-standard/references/08_ARCHITECTURAL_DECISION_RECORDS_ADR.md`](./skills/iac-gold-standard/references/08_ARCHITECTURAL_DECISION_RECORDS_ADR.md) for decision rationale (ADR-01 to ADR-07).
