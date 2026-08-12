---
name: iac-gold-standard
description: Enterprise AWS Terraform IaC Gold Standard blueprint, 4-layer multi-account architecture, DRY single-directory modules with for_each feature flags, PCI-DSS v4.0 controls, and CAB governance branching.
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

## 2. 📂 Submodule Creation Workflow

When adding or developing a new module:

1. **Submodule Directory:** Create `modules/<module-name>/` containing `main.tf`, `variables.tf`, `outputs.tf`, `README.md`.
2. **Parent Invocation:** Register in parent layer `main.tf`:
   ```hcl
   locals {
     deploy_modules = {
       my_module = var.enable_modules.my_module ? { my_module = {} } : {}
     }
   }

   module "my_module" {
     for_each = local.deploy_modules.my_module
     source   = "./modules/my-module"
     ...
   }
   ```
3. **Parameter Input:** Add boolean toggle in `variables.tf` and environment configs in `dev.tfvars` / `prod.tfvars`.
4. **Format & Validate:** Run `terraform fmt -recursive` and `terraform validate`.

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
