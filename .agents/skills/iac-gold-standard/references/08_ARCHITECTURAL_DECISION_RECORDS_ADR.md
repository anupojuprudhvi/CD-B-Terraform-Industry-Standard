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
Segregate infrastructure into `00-foundation`, `01-core-network`, `02-shared-services`, `03-applications`. Zero blast radius risk and 1-to-1 AWS SSO mapping.

## ADR-02: DRY Single-Directory Design (`.tfvars`) over Folder-per-Environment
Write `main.tf` once per layer; parameterize environments via `dev.tfvars`, `prod.tfvars`, `prod-dr.tfvars`. 80% code reduction and zero copy-paste drift.

## ADR-03: Submodule Feature Flags via `for_each` Deployment Control Maps
Implement `locals { deploy_modules = ... }` with `for_each` to cleanly turn optional submodules on/off per environment via boolean flags in `.tfvars`.

## ADR-04: Dedicated `prod` Branch with Enforced CAB Approval Gate
Enforce promotion hierarchy: `feature/*` → `dev` → `staging` → `main` (Pre-Prod) → `prod` (CAB Gate). Enforces enterprise change management.

## ADR-05: Native S3 State Locking (`use_lockfile = true`) over DynamoDB
Enforce `use_lockfile = true` in Terraform >= 1.10.0; eliminate DynamoDB table overhead and cost.

## ADR-06: PCI-DSS v4.0 Baseline & Multi-Region Disaster Recovery (DR)
Customer Managed KMS CMK rotation, private subnets, WAF v2, CloudTrail WORM log archiving, and multi-region DR failover (RPO < 15m, RTO < 1h).

## ADR-07: Custom Skill & Encapsulated Knowledge Base
Encapsulated inside `.agents/skills/iac-gold-standard/` for automated AI execution and 100% compliance.
