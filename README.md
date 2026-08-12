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
                ├── 04_SECURITY_COMPLIANCE_AND_DR.md
                └── 05_ARCHITECTURAL_DECISION_RECORDS.md
```

---

## 📚 Custom Skill Index (`.agents/skills/iac-gold-standard/`)

| Skill / Reference Document | Focus Area & Content | Target Audience |
| :--- | :--- | :--- |
| 🎯 [**`SKILL.md`**](./.agents/skills/iac-gold-standard/SKILL.md) | **Main Custom Skill:** Trigger rules, core 4-layer principles, DRY submodule creation workflow. | AI Assistants, All Engineers |
| 📘 [**`01_ARCHITECTURE_AND_MODULE_DESIGN.md`**](./.agents/skills/iac-gold-standard/references/01_ARCHITECTURE_AND_MODULE_DESIGN.md) | **Architecture & Module Rules:** 4-layer AWS account mapping, DRY `.tfvars` parameterization, `for_each` feature flags. | Cloud Architects |
| 🌿 [**`02_BRANCHING_AND_GOVERNANCE_SOP.md`**](./.agents/skills/iac-gold-standard/references/02_BRANCHING_AND_GOVERNANCE_SOP.md) | **Git Promotion & CAB Gates:** Hierarchy (`dev` → `staging` → `main` → `prod`) and CAB ticket approval rules. | DevOps Leads |
| 📄 [**`03_README_AND_DOCS_STANDARDS.md`**](./.agents/skills/iac-gold-standard/references/03_README_AND_DOCS_STANDARDS.md) | **Documentation Standards:** Submodule `README.md` layout & markdown standards. | All Engineers |
| 🛡️ [**`04_SECURITY_COMPLIANCE_AND_DR.md`**](./.agents/skills/iac-gold-standard/references/04_SECURITY_COMPLIANCE_AND_DR.md) | **PCI-DSS & DR Standards:** Technical safeguards for PCI-DSS v4.0, KMS CMK rotation, and multi-region DR targets. | Security Leads |
| 💡 [**`05_ARCHITECTURAL_DECISION_RECORDS.md`**](./.agents/skills/iac-gold-standard/references/05_ARCHITECTURAL_DECISION_RECORDS.md) | **ADR Summary:** Explicit rationale behind decisions (ADR-01 to ADR-07). | Architects, PMs |

---

## 🎯 How to Use This Skill in Any Project

Copy `.agents/` into any new client repository, or tell the AI assistant:
> *"Use the `iac-gold-standard` skill to design a new submodule in 03-applications."*

The AI assistant will automatically load the skill and enforce 100% architectural compliance!
