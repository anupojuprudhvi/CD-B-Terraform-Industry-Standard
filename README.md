# CD-B Terraform Industry Standard — Master Knowledge Base & Custom Skill Repository

Welcome to the **Master Knowledge Base & Custom Skill Repository** for enterprise Infrastructure-as-Code (IaC) using Terraform on AWS across **Clients-Delivery (CD-B)**.

This repository encapsulates all enterprise architecture standards, PCI-DSS compliance frameworks, CAB governance SOPs, and module design rules into a reusable **Custom Skill** located at:
📁 **[`.agents/skills/iac-gold-standard/`](./.agents/skills/iac-gold-standard/)**

---

## 🧠 Custom Skill & Knowledge Base Structure

```text
CD-B-Terraform-Industry-Standard/
├── README.md                                 # Master Repository Index
├── CHANGELOG.md                              # Repository Version Log
├── .gitignore                                # Git Ignore Rules
└── .agents/
    ├── AGENTS.md                             # AI Assistant Rule Engine
    └── skills/
        └── iac-gold-standard/                # Custom Skill Folder
            ├── SKILL.md                      # Main Skill Definition & Rules (< 500 lines)
            └── references/                   # Deep-Dive Modular Reference Guides
                ├── 01_ARCHITECTURE_AND_MODULE_DESIGN.md
                ├── 02_BRANCHING_AND_GOVERNANCE_SOP.md
                ├── 03_README_AND_DOCS_STANDARDS.md
                ├── 04_GREENFIELD_PROJECT_BOOTSTRAP_CHECKLIST.md
                ├── 05_SECURITY_COMPLIANCE_AND_DR_STANDARDS.md
                ├── 06_TERRAFORM_LINTING_TESTING_AND_SECURITY_PIPELINES.md
                ├── 07_STATE_BACKEND_AND_LOCKING_SOP.md
                └── 08_ARCHITECTURAL_DECISION_RECORDS_ADR.md
```

---

## 📚 Custom Skill Index (`.agents/skills/iac-gold-standard/`)

| Skill / Reference Document | Focus Area & Content | Target Audience |
| :--- | :--- | :--- |
| 🎯 [**`SKILL.md`**](./.agents/skills/iac-gold-standard/SKILL.md) | **Main Custom Skill:** Trigger rules, 4-layer principles, DRY submodule workflow, gotchas & handover templates. | AI Assistants, All Engineers |
| 📘 [**`01_ARCHITECTURE_AND_MODULE_DESIGN.md`**](./.agents/skills/iac-gold-standard/references/01_ARCHITECTURE_AND_MODULE_DESIGN.md) | **Architecture & Module Rules:** 4-layer AWS account mapping, DRY `.tfvars` parameterization, `for_each` feature flags. | Cloud Architects |
| 🌿 [**`02_BRANCHING_AND_GOVERNANCE_SOP.md`**](./.agents/skills/iac-gold-standard/references/02_BRANCHING_AND_GOVERNANCE_SOP.md) | **Git Promotion & CAB Gates:** Hierarchy (`dev` → `staging` → `main` → `prod`) and CAB ticket approval rules. | DevOps Leads |
| 📄 [**`03_README_AND_DOCS_STANDARDS.md`**](./.agents/skills/iac-gold-standard/references/03_README_AND_DOCS_STANDARDS.md) | **Documentation Standards:** Submodule `README.md` layout, `docs/` folder rules, markdown standards. | All Engineers |
| 📋 [**`04_GREENFIELD_PROJECT_BOOTSTRAP_CHECKLIST.md`**](./.agents/skills/iac-gold-standard/references/04_GREENFIELD_PROJECT_BOOTSTRAP_CHECKLIST.md) | **Initiation Checklist:** 15-minute operational checklist when bootstrapping a new client project. | Project Leads, Engineers |
| 🛡️ [**`05_SECURITY_COMPLIANCE_AND_DR_STANDARDS.md`**](./.agents/skills/iac-gold-standard/references/05_SECURITY_COMPLIANCE_AND_DR_STANDARDS.md) | **PCI-DSS & DR Standards:** Technical safeguards for PCI-DSS v4.0, KMS CMK rotation, and multi-region DR targets. | Security Leads |
| 🤖 [**`06_TERRAFORM_LINTING_TESTING_AND_SECURITY_PIPELINES.md`**](./.agents/skills/iac-gold-standard/references/06_TERRAFORM_LINTING_TESTING_AND_SECURITY_PIPELINES.md) | **CI/CD & Security Scanning:** Pre-commit hooks, `checkov`, `tfsec`, `tflint`, `infracost` cost estimation, and GitHub Actions workflows. | DevOps Engineers |
| 🔒 [**`07_STATE_BACKEND_AND_LOCKING_SOP.md`**](./.agents/skills/iac-gold-standard/references/07_STATE_BACKEND_AND_LOCKING_SOP.md) | **State Backend & Locking SOP:** Native S3 state locking (`use_lockfile = true`), encryption/versioning, stuck lock recovery. | Operations |
| 💡 [**`08_ARCHITECTURAL_DECISION_RECORDS_ADR.md`**](./.agents/skills/iac-gold-standard/references/08_ARCHITECTURAL_DECISION_RECORDS_ADR.md) | **Architectural Decision Records (ADR):** Explicit context, decisions, rationale, and business benefits (ADR-01 to ADR-07). | Architects, Team Leads |

---

## 🎯 How to Use This Skill in Any Project

Copy `.agents/` into any new client repository, or tell the AI assistant:
> *"Use the `iac-gold-standard` skill to design a new submodule in 03-applications."*

The AI assistant will automatically load the skill and enforce 100% architectural compliance!
