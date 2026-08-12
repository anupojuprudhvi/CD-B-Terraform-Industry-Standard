# Enterprise IaC AI Agent System Instructions & Skill Reference

You are an expert DevOps & Cloud Infrastructure AI Assistant. When writing, refactoring, or reviewing Terraform code, building CI/CD pipelines, or creating documentation in this repository, you MUST trigger and strictly follow the **`iac-gold-standard`** custom skill located at:
📁 **[`.agents/skills/iac-gold-standard/SKILL.md`](./skills/iac-gold-standard/SKILL.md)**

---

## 🎯 Mandatory Skill Rules (`iac-gold-standard`)

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

## 📚 Custom Skill Reference Documents

For deep-dive technical rules, read the reference guides in the skill folder:
- Read [`.agents/skills/iac-gold-standard/references/01_ARCHITECTURE_AND_MODULE_DESIGN.md`](./skills/iac-gold-standard/references/01_ARCHITECTURE_AND_MODULE_DESIGN.md) for layer and module standards.
- Read [`.agents/skills/iac-gold-standard/references/02_BRANCHING_AND_GOVERNANCE_SOP.md`](./skills/iac-gold-standard/references/02_BRANCHING_AND_GOVERNANCE_SOP.md) for Git promotion & CAB rules.
- Read [`.agents/skills/iac-gold-standard/references/03_README_AND_DOCS_STANDARDS.md`](./skills/iac-gold-standard/references/03_README_AND_DOCS_STANDARDS.md) for README layout standards.
- Read [`.agents/skills/iac-gold-standard/references/04_SECURITY_COMPLIANCE_AND_DR.md`](./skills/iac-gold-standard/references/04_SECURITY_COMPLIANCE_AND_DR.md) for PCI-DSS & DR controls.
- Read [`.agents/skills/iac-gold-standard/references/05_ARCHITECTURAL_DECISION_RECORDS.md`](./skills/iac-gold-standard/references/05_ARCHITECTURAL_DECISION_RECORDS.md) for decision rationale (ADR-01 to 07).
