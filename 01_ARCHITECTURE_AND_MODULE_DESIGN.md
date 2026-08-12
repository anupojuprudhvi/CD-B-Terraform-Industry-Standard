# 01 - Architecture & Module Design Standards

This document defines the core rules for structuring Terraform code, organizing modules, and mapping infrastructure to AWS Multi-Account boundaries.

---

## 1. The 4-Layer Multi-Account Architecture

Every project must be segregated into 4 independent architectural layers, matching AWS Account boundaries and blast-radius domains:

```text
┌─────────────────────────────────────────────────────────────────────────────────────────┐
│                               4-LAYER ENTERPRISE ARCHITECTURE                           │
├─────────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                         │
│   LAYER DIRECTORY                          AWS ACCOUNT & TARGET SCOPE                   │
│   ───────────────                          ──────────────────────────                   │
│                                                                                         │
│   00-foundation/        ───────────►   Management Account (AWS Org, SCPs)               │
│                                            Security Account (GuardDuty, CloudTrail)     │
│                                                                                         │
│   01-core-network/      ───────────►   Networking Hub Account (TGW, VPN, RAM)           │
│                                            Spoke VPCs (Dev, Staging, Prod, DR)          │
│                                                                                         │
│   02-shared-services/   ───────────►   Shared Services Account                          │
│                                            (Jenkins, Keycloak, ECR, Bastion, Route53)   │
│                                                                                         │
│   03-applications/      ───────────►   Workload Accounts (Dev, Staging, Prod, DR)       │
│                                            (EKS, Aurora RDS, ElastiCache, SQS, WAF)     │
│                                                                                         │
└─────────────────────────────────────────────────────────────────────────────────────────┘
```

---

## 2. Module Design Pattern: DRY Single-Directory + `.tfvars`

### ❌ Anti-Pattern to Avoid:
Do **NOT** duplicate `main.tf` files into separate `dev/`, `staging/`, and `prod/` subfolders. This causes 3x code duplication and leads to merge drift.

### ✅ Gold Standard Pattern:
Write `main.tf` **once** per layer and parameterize environments via `.tfvars` and `.hcl` backend configuration files:

```text
03-applications/workload-core/
├── backend-dev.hcl             # Backend S3 key: apps/dev/terraform.tfstate
├── backend-staging.hcl         # Backend S3 key: apps/staging/terraform.tfstate
├── backend-prod.hcl            # Backend S3 key: apps/prod/terraform.tfstate
├── backend-prod-dr.hcl         # Backend S3 key: apps/prod-dr/terraform.tfstate
├── dev.tfvars                  # Small instances, 1 replica, debug logs
├── staging.tfvars              # Staging parameters
├── prod.tfvars                 # High Availability, Multi-AZ, Graviton, 3+ replicas
├── prod-dr.tfvars              # Disaster Recovery parameters (secondary region)
├── data.tf                     # Remote state lookups
├── main.tf                     # Single DRY Main Module Declarations
├── outputs.tf                  # Layer outputs
├── providers.tf                # AWS provider setup
├── variables.tf                # Variable definitions
└── modules/                    # Reusable Submodules
    ├── eks/
    ├── amazonmq/
    ├── sftp/
    └── databases/
```

---

## 3. Feature-Flag Module Toggle Design (`for_each`)

Modules within `main.tf` should be conditionally deployed using a clean `locals` feature flag map:

```hcl
locals {
  name_prefix = lower("${var.project_name}-${var.env}")

  # Feature Flag Toggle Map
  deploy_modules = {
    eks       = var.enable_modules.eks ? { eks = {} } : {}
    amazon_mq = var.enable_modules.amazon_mq ? { amazon_mq = {} } : {}
    sftp      = var.enable_modules.sftp ? { sftp = {} } : {}
    databases = var.enable_modules.databases ? { databases = {} } : {}
  }
}

module "eks" {
  for_each = local.deploy_modules.eks
  source   = "./modules/eks"

  name_prefix = local.name_prefix
  env         = var.env
  node_config = var.node_config
}
```

---

## 4. PCI-DSS v4.0 Compliance Safeguards

1. **At-Rest Encryption:** All S3, EFS, RDS, and ElastiCache resources MUST use Customer Managed KMS Keys (CMK) with 365-day rotation.
2. **In-Transit Encryption:** Enforce TLS 1.3 / 1.2 on all ALBs, API Gateways, and CloudFront distributions.
3. **Network Isolation:** Workload EC2/EKS nodes MUST live in **Private Subnets** with zero direct Internet IPs.
4. **WORM Logging:** Centralized CloudTrail and Security Hub logs MUST deliver to an isolated Archive account with S3 Object Lock enabled.
