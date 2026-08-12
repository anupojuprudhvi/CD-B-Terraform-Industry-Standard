# 03 - README & Documentation Design Standards

This guide defines the required layout, structure, and markdown standards for all project and module `README.md` files across CD-B repositories.

---

## 1. Required Structure for Root Project `README.md`

Every client repository root `README.md` MUST contain these 6 sections in order:

```markdown
# [Client / Project Name]

[1-2 sentence executive summary of the architecture and environment scope]

## 📌 Architecture Diagram
[ASCII or Mermaid diagram of the AWS Multi-Account layout & Transit Gateway Hub]

## 🚀 AWS Account & Profile Matrix
[Table listing Profiles, Account IDs, and Account Usages]

## 🏗️ Repository Structure
[Folder tree explaining 00-foundation, 01-core-network, 02-shared-services, 03-applications]

## 🛠️ Quick Start & Deployment Guide
[Standard commands for initializing backend and executing terraform plan / apply]

## 🔐 Security & Compliance Safeguards
[PCI-DSS v4.0 safeguards, CMK KMS encryption rules, state locking details]
```

---

## 2. Required Structure for Module `README.md`

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

---

## 3. Markdown Formatting Standards (Markdownlint Clean)

- **Headings (MD022):** Must have blank lines before and after every `#` heading.
- **Unordered Lists (MD004):** Must use hyphens (`-`) consistently.
- **Math Symbols:** Use standard text or Unicode arrows (`→`) rather than LaTeX `\rightarrow`.
- **EOF Newline (MD047):** Every `.md` file must end with a single newline.
