# 08 - Architectural Decision Records (ADR)

This document records the context, explicit decisions, rationale, and business/technical benefits for every core architectural decision made in the **CD-B Terraform Industry Standard Framework**.

---

## 📌 Index of Architectural Decision Records (ADRs)

- [ADR-01: 4-Layer Multi-Account Architecture Alignment](#adr-01-4-layer-multi-account-architecture-alignment)
- [ADR-02: DRY Single-Directory Design (`.tfvars`) over Folder-per-Environment](#adr-02-dry-single-directory-design-tfvars-over-folder-per-environment)
- [ADR-03: Submodule Feature Flags via `for_each` Deployment Control Maps](#adr-03-submodule-feature-flags-via-for_each-deployment-control-maps)
- [ADR-04: Dedicated `prod` Branch with Enforced CAB Approval Gate](#adr-04-dedicated-prod-branch-with-enforced-cab-approval-gate)
- [ADR-05: Native S3 State Locking (`use_lockfile = true`) over DynamoDB](#adr-05-native-s3-state-locking-use_lockfile--true-over-dynamodb)
- [ADR-06: PCI-DSS v4.0 Baseline & Multi-Region Disaster Recovery (DR)](#adr-06-pci-dss-v40-baseline--multi-region-disaster-recovery-dr)
- [ADR-07: Dedicated Pure Documentation Knowledge Base Repository](#adr-07-dedicated-pure-documentation-knowledge-base-repository)

---

## ADR-01: 4-Layer Multi-Account Architecture Alignment

### Status
**Accepted**

### Context
Traditional Terraform repositories place all infrastructure into a single flat directory or monolithic workspace. This creates massive blast radius risk, causes state file locking contention among developers, and violates AWS Multi-Account security boundaries.

### Decision
Segregate all client infrastructure into **4 independent Architectural Layers**:
- **`00-foundation`**: Management Account (AWS Org, SCPs) & Security Account (GuardDuty, Security Hub, CloudTrail).
- **`01-core-network`**: Networking Hub Account (Transit Gateway, VPNs, RAM Shares) & Spoke VPCs.
- **`02-shared-services`**: Shared Services Account (Jenkins CI, Keycloak HA, ECR, Bastion, Route 53).
- **`03-applications`**: Workload Accounts (Dev, Staging, Prod, DR) hosting EKS, Aurora RDS, ElastiCache, SQS, WAF.

### Rationale & Benefits
1. **Blast Radius Control:** An error in an application deployment (`03`) cannot destroy network transit routing (`01`) or security guardrails (`00`).
2. **AWS SSO Alignment:** Maps 1-to-1 with AWS IAM Identity Center permission sets and account IDs.
3. **Zero Lock Contention:** Separate S3 state buckets per layer mean app developers never block network or security engineers.

---

## ADR-02: DRY Single-Directory Design (`.tfvars`) over Folder-per-Environment

### Status
**Accepted**

### Context
In early projects (e.g. `THEA`), separate subdirectories were created for each environment (`dev/`, `staging/`, `prod/`), each containing a copy of `main.tf`. This resulted in 3x code duplication (540+ lines of duplicate code) and high risk of copy-paste drift during updates.

### Decision
Adopt the **`Phoenix` DRY Single-Directory Model**: Write `main.tf` **once** per component layer and parameterize environments dynamically via `.tfvars` files (`dev.tfvars`, `prod.tfvars`, `prod-dr.tfvars`) and `.hcl` backend configuration files (`backend-dev.hcl`, `backend-prod.hcl`).

### Rationale & Benefits
1. **80% Code Reduction:** Eliminates duplicate `main.tf` files.
2. **Zero Copy-Paste Drift:** Modifying a submodule in `main.tf` automatically applies to all environments.
3. **Cleaner Maintenance:** Adding an input variable requires updating `main.tf` only once.

---

## ADR-03: Submodule Feature Flags via `for_each` Deployment Control Maps

### Status
**Accepted**

### Context
Different environments often require different subsets of microservices (e.g. `dev` might not need SFTP transfer or Jasper reporting servers, while `prod` requires them). Hardcoding module invocations forces developers to comment out code blocks per environment.

### Decision
Implement **Feature-Flag Control Maps** using `locals` and `for_each` (inherited from `THEA`):

```hcl
locals {
  deploy_modules = {
    sftp      = var.enable_modules.sftp ? { sftp = {} } : {}
    amazon_mq = var.enable_modules.amazon_mq ? { amazon_mq = {} } : {}
  }
}

module "sftp_transfer" {
  for_each = local.deploy_modules.sftp
  source   = "./modules/sftp"
  ...
}
```

### Rationale & Benefits
1. **Zero Code Editing:** Enable or disable any submodule by flipping a boolean in `dev.tfvars` (`enable_modules.sftp = false`).
2. **Clean State Management:** Terraform gracefully creates or destroys optional submodules based on boolean flags.

---

## ADR-04: Dedicated `prod` Branch with Enforced CAB Approval Gate

### Status
**Accepted**

### Context
Using `main` as Production (GitHub Flow) works well for small SaaS startups, but enterprise clients require formal Change Advisory Board (CAB) reviews, scheduled maintenance windows, and multi-tier approval sign-offs before live production changes.

### Decision
Enforce a 4-tier Git promotion hierarchy: **`feature/*` → `dev` → `staging` → `main` (Pre-Prod Integration) → `prod` (Production)**.

### Rationale & Benefits
1. **Enterprise CAB Compliance:** Production releases sit on `main` until formal CAB approval is granted, at which point a PR is opened to `prod`.
2. **Locked Production Branch:** `prod` is locked against direct pushes; merging requires CAB ticket verification and 2 senior approver sign-offs.

---

## ADR-05: Native S3 State Locking (`use_lockfile = true`) over DynamoDB

### Status
**Accepted**

### Context
Historically, Terraform required a separate AWS DynamoDB table (`lock_table`) to manage state locking. In Terraform >= 1.10.0, AWS S3 natively supports state locking via S3 lockfiles.

### Decision
Enforce `use_lockfile = true` in all S3 backend configurations and deprecate DynamoDB lock tables across all new client projects.

### Rationale & Benefits
1. **Cost & Overhead Reduction:** Eliminates creating and managing DynamoDB tables per AWS account.
2. **Simplified IAM:** Reduces IAM policy complexity (no DynamoDB `GetItem`/`PutItem`/`DeleteItem` permissions required).

---

## ADR-06: PCI-DSS v4.0 Baseline & Multi-Region Disaster Recovery (DR)

### Status
**Accepted**

### Context
Enterprise clients in financial services, healthcare, and tolling infrastructure require regulatory compliance out of the box and guaranteed business continuity during cloud outage events.

### Decision
Mandate **PCI-DSS v4.0 Safeguards** (CMK KMS key rotation, private subnets, WAF v2, CloudTrail WORM log archiving) and **Multi-Region DR Support** (`prod-dr.tfvars`, RPO < 15m, RTO < 1h) as standard architectural features in every project template.

### Rationale & Benefits
1. **Audit Readiness:** Projects pass third-party compliance audits (PCI-DSS, SOC2, HIPAA) without structural refactoring.
2. **Cloud Resilience:** Cross-region Aurora RDS Global databases and Route 53 health check failovers ensure zero data loss during regional outages.

---

## ADR-07: Dedicated Pure Documentation Knowledge Base Repository

### Status
**Accepted**

### Context
Storing design rules, SOPs, and architectural decisions inside code repositories often leads to outdated documentation as code evolves. Engineers need a single, immutable source of truth for design standards.

### Decision
Maintain **`CD-B-Terraform-Industry-Standard`** as a **pure Markdown Knowledge Base repository** containing zero code files.

### Rationale & Benefits
1. **Centralized Standards:** Serves as the authoritative blueprint for all current and future client projects.
2. **Immutable Guidance:** Documents the *why* behind every technical choice so new team members understand the architectural rationale immediately.
