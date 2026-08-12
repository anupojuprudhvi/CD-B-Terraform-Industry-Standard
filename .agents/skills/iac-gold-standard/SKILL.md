---
name: iac-gold-standard
description: Enterprise AWS Terraform IaC Gold Standard blueprint, 4-layer multi-account architecture, DRY single-directory modules with for_each feature flags, PCI-DSS v4.0 controls, technical gotchas, daily handover reports, and CAB governance branching.
---

# Enterprise AWS Terraform IaC Gold Standard Skill

Use this skill whenever creating a new Terraform module, refactoring an existing IaC component, or setting up a fresh client repository in AWS.

---

## 1. 🏗️ Architectural Core Principles

1. **4-Layer Separation:** Organize code into `00-foundation`, `01-core-network`, `02-shared-services`, and `03-applications`.
2. **DRY Single-Directory Pattern:** Write `main.tf` ONCE per layer. Parameterize environments via `dev.tfvars`, `prod.tfvars`, and `prod-dr.tfvars`. Never duplicate `main.tf` across `dev/`, `staging/`, `prod/` subfolders.
3. **Submodule Feature Flags:** Implement `locals { deploy_modules = ... }` with `for_each` to allow optional submodules (e.g., SFTP, MQ, Redis) to be enabled/disabled per environment via boolean flags in `.tfvars`.
4. **Git Branching & CAB Gate:** Follow promotion path `feature/*` → `dev` → `staging` → `main` (Pre-Prod) → `prod` (CAB Approval Gate).
5. **PCI-DSS v4.0 Compliance:** Enforce KMS CMK rotation, private subnets only, WAF v2, CloudTrail WORM log archiving, and multi-region DR failover readiness (RPO < 15m, RTO < 1h).
6. **Native S3 State Locking:** Enforce `use_lockfile = true` in S3 backend configurations.

---

## ⚡ 2. Technical Gotchas & Field Safeguards

- **VPC Flow Logs:** S3 bucket encryption MUST be SSE-S3 (`AES256`), not SSE-KMS (`aws:kms`). S3 log delivery fails with `Access Denied` on KMS buckets without explicit cross-account KMS key policy grants.
- **Secrets Manager Recovery Window:** Immediately re-creating a destroyed secret with the same name fails with `InvalidRequestException`. Force-delete first: `aws secretsmanager delete-secret --secret-id <name> --force-delete-without-recovery`.
- **EKS EBS CSI Addon:** Always add `timeouts { create = "40m" }` to prevent 20-minute resource provisioning timeouts.
- **Aurora Global Database:** Global cluster identifiers cannot be retrofitted to existing clusters; create primary clusters with `global_cluster_identifier` set at creation time.
- **Keycloak Health Checks:** Management health checks run on **port 9000** (not app port 8080). ALB Security Groups must allow egress to both 8080 & 9000.

---

## 📋 3. Daily Task & Engineering Handover Report Standard

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

## 🚫 4. Explicit "What NOT To Do" Guardrails

- **Do NOT** place EC2 or EKS nodes in public subnets (Private Subnets only).
- **Do NOT** hardcode AWS account IDs, VPC IDs, or passwords in `.tf` files (use `data.tf` lookups & AWS Secrets Manager).
- **Do NOT** bypass git hooks or force-push to `main`/`prod` branches (`git push --force` prohibited).
- **Do NOT** commit `.env` or `.pem` key files containing plaintext credentials.

---

## 📚 Complete 8 Reference Standards (`references/`)

For deep-dive technical rules, refer to the files in `references/`:
- `references/01_ARCHITECTURE_AND_MODULE_DESIGN.md` — 4-layer mapping & DRY module layout.
- `references/02_BRANCHING_AND_GOVERNANCE_SOP.md` — Git promotion & CAB approval gates.
- `references/03_README_AND_DOCS_STANDARDS.md` — Submodule `README.md` & `docs/` folder layout rules.
- `references/04_GREENFIELD_PROJECT_BOOTSTRAP_CHECKLIST.md` — 15-minute new project checklist.
- `references/05_SECURITY_COMPLIANCE_AND_DR_STANDARDS.md` — PCI-DSS v4.0 & multi-region DR controls.
- `references/06_TERRAFORM_LINTING_TESTING_AND_SECURITY_PIPELINES.md` — Checkov, tfsec, tflint, Infracost & CI/CD workflows.
- `references/07_STATE_BACKEND_AND_LOCKING_SOP.md` — S3 native state locking (`use_lockfile = true`) & recovery runbook.
- `references/08_ARCHITECTURAL_DECISION_RECORDS_ADR.md` — Rationale for all architectural choices (ADR-01 to ADR-07).
