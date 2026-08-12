# 06 - Automated Security Scanning, Linting & CI/CD Pipelines

This guide defines the automated pre-flight checks, static code analysis tools, cost estimation tools, and CI/CD pipeline specifications.

---

## 1. Automated Tooling Matrix

| Tool | Purpose | Execution Stage | Command |
| :--- | :--- | :--- | :--- |
| **`terraform fmt`** | Code formatting standardization | Pre-commit / CI | `terraform fmt -check -recursive` |
| **`terraform validate`** | Syntax & schema validation | Pre-commit / CI | `terraform validate` |
| **`tflint`** | Terraform linter for cloud best practices | Pre-commit / CI | `tflint --recursive` |
| **`checkov`** | Static security & compliance scanner | Pre-commit / CI | `checkov -d . --framework terraform` |
| **`tfsec`** | Security scanner for IaC vulnerabilities | Pre-commit / CI | `tfsec .` |
| **`infracost`** | Cloud cost estimation on PRs | CI Pipeline | `infracost breakdown --path .` |

---

## 2. GitHub Actions CI/CD Pipeline Blueprint

Create `.github/workflows/terraform-ci.yml`:

```yaml
name: "Terraform Security & Plan Audit"

on:
  pull_request:
    branches: [ dev, staging, main, prod ]

jobs:
  security-audit:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Code
        uses: actions/checkout@v4

      - name: Setup Terraform
        uses: hashicorp/setup-terraform@v3
        with:
          terraform_version: 1.10.0

      - name: Terraform Format Check
        run: terraform fmt -check -recursive

      - name: Security Scan (Checkov)
        uses: bridgecrewio/checkov-action@master
        with:
          framework: terraform
          output_format: cli
          soft_fail: false

      - name: Infracost Breakdown
        uses: infracost/actions/setup@v2
        with:
          api_key: ${{ secrets.INFRACOST_API_KEY }}
```

---

## 3. Pre-Commit Hooks Configuration (`.pre-commit-config.yaml`)

Engineers can enable pre-commit hooks locally to prevent pushing bad code:

```yaml
repos:
  - repo: https://github.com/antonbabenko/pre-commit-terraform
    rev: v1.96.0
    hooks:
      - id: terraform_fmt
      - id: terraform_validate
      - id: terraform_tflint
      - id: terraform_tfsec
      - id: terraform_checkov
```
