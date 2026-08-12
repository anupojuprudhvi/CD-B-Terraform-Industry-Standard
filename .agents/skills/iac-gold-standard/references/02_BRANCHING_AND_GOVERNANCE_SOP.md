# 02 - Branching Strategy & CAB Governance SOP

This standard operating procedure (SOP) defines the Git branch promotion hierarchy, PR policies, and Change Advisory Board (CAB) approval gates.

---

## 1. Branch Promotion Hierarchy

Code updates move strictly upward through the environment hierarchy:

```text
[ Feature Branch ] ──(PR)──► [ dev ] ──(Auto-Apply)──► AWS Dev Account
                                │
                           (PR Review)
                                ▼
                          [ staging ] ──(Auto-Apply)──► AWS Staging Account
                                │
                          (Integration PR)
                                ▼
                             [ main ] ──(Pre-Prod Integration Baseline)
                                │
                          (CAB Sign-off Ticket Required)
                                ▼
                             [ prod ] ──(Manual Approval)──► AWS Prod & Prod-DR Accounts
```

---

## 2. Branch Responsibilities & Governance Matrix

| Branch Name | Scope & Purpose | Target AWS Account | PR Approval Rule | CI/CD Pipeline Action |
| :--- | :--- | :--- | :--- | :--- |
| **`feature/*`** | Local feature development & bug fixes | Local workspace | N/A | Local `terraform fmt` & `checkov` scan |
| **`dev`** | Development testing environment | AWS Dev Account | 1 Peer Approver | Auto-plan & auto-apply on merge |
| **`staging`** | QA, UAT, and staging validation | AWS Staging Account | 1 Lead Approver | Auto-plan on PR; auto-apply on merge |
| **`main`** | Pre-production integration baseline | Management / Security / Shared / Hub | 2 Senior Approvers | Pre-production integration verification |
| **`prod`** | **Production Live Environment** | AWS Prod Account (`prod` & `prod-dr`) | **CAB Board Sign-off + 2 Approvers** | Manual release click-approval by Operations Lead |
