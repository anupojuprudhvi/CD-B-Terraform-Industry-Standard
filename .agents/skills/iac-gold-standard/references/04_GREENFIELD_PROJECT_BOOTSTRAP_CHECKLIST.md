# 04 - Greenfield Project Bootstrap Checklist

Use this operational checklist whenever initiating a new client project from scratch.

---

## 📋 Pre-Flight Checklist: Project Setup & Initialization

### Phase 1: Repository Creation & Remote Setup
- [ ] Create GitHub Repository `CD-B-<ClientName>` under account `anupojuprudhvi`.
- [ ] Set repository visibility to **Private**.
- [ ] Initialize local folder `d:\Debug\P-iac\Clients- Delivery\CD-B-<ClientName>`.
- [ ] Add git remote: `git remote add origin git@github-professional:anupojuprudhvi/CD-B-<ClientName>.git`.

### Phase 2: Codebase Architecture Setup
- [ ] Create 4 root layer directories: `00-foundation`, `01-core-network`, `02-shared-services`, `03-applications`.
- [ ] Implement **DRY Single-Directory Design** (`main.tf` + `dev.tfvars`, `staging.tfvars`, `prod.tfvars`, `prod-dr.tfvars`).
- [ ] Implement `locals { deploy_modules = ... }` feature flags in `main.tf` using `for_each`.
- [ ] Verify native S3 state locking (`use_lockfile = true`) in backend configuration.
- [ ] Create standard `.gitignore` ignoring `.terraform/`, `*.tfstate`, `*.pem`, `*.log`.

### Phase 3: Git Branching & Security Setup
- [ ] Create Git branches: `dev`, `staging`, `main` (pre-prod), `prod` (CAB gate).
- [ ] Configure branch protection rules on GitHub for `prod` (requiring CAB ticket & 2 approvers).
- [ ] Add root `README.md` following [03_README_AND_DOCS_STANDARDS.md](./03_README_AND_DOCS_STANDARDS.md).

### Phase 4: Initial Verification & Push
- [ ] Run `terraform fmt -recursive` to format all `.tf` files.
- [ ] Run `terraform validate` across layer folders.
- [ ] Run `git add -A` and create initial commit.
- [ ] Push main branch to GitHub: `git push -u origin main`.
