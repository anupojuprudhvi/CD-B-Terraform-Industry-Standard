# 03 - README & Documentation Design Standards

This guide defines the required layout, directory organization, and markdown standards for all project documentation across CD-B repositories.

---

## 1. Clean Root Directory vs `docs/` Subdirectory Standard

Keep the root directory minimal, storing deep-dive technical specs, runbooks, and diagrams inside a dedicated **`docs/`** directory:

```text
CD-B-<ClientName>/
├── README.md                                 # Master Overview & Quick Start ONLY
├── .gitignore                                # Git Ignore Rules
├── .agents/AGENTS.md                         # AI Assistant Rules
├── docs/                                     # All Deep-Dive Technical Documentation
│   ├── ARCHITECTURE.md                       # High-level architecture & VPC design
│   ├── DEPLOYMENT-RUNBOOK.md                 # Deployment & rollback procedures
│   └── SECURITY-COMPLIANCE.md                # PCI-DSS / SOC2 control mapping
├── 00-foundation/
├── 01-core-network/
├── 02-shared-services/
└── 03-applications/
```

---

## 2. Required Structure for Submodule `README.md`

Submodule directories (e.g. `modules/eks`, `modules/databases`) MUST contain a `README.md` structured as follows:

```markdown
# [Module Name] Module

[Summary of resources created by this submodule]

## 📥 Input Variables
| Name | Type | Default | Description |
| :--- | :--- | :--- | :--- |

## 📤 Outputs
| Name | Description |
| :--- | :--- |

## 💡 Usage Example
```hcl
module "example" {
  source = "./modules/example"
  ...
}
```
```
