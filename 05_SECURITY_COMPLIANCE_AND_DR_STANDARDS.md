# 05 - Security, Compliance (PCI-DSS / SOC2) & Disaster Recovery (DR) Standards

This guide defines the security baseline, regulatory compliance safeguards (PCI-DSS v4.0, SOC2, HIPAA), and multi-region Disaster Recovery (DR) architecture for all enterprise client deployments.

---

## 1. Regulatory Compliance Framework

### 🛡️ PCI-DSS v4.0 & SOC2 Safeguards Matrix

| Requirement Domain | Technical Safeguard | Infrastructure Location |
| :--- | :--- | :--- |
| **Network Boundary Security** | Multi-VPC Transit Gateway Hub. Private subnets only for all EKS/EC2/RDS nodes. Public access allowed only via AWS WAF v2 + ALB. | `01-core-network`<br/>`03-applications/cloudfront-waf` |
| **Data Protection at Rest** | Customer Managed KMS Keys (CMK) with automated 365-day rotation. Enforced AES-256 on S3, EFS, Aurora RDS, and ElastiCache. | `03-applications/kms-key`<br/>`03-applications/databases` |
| **Data Protection in Transit** | Enforced TLS 1.3 / 1.2 on all ALBs, API Gateways, and CloudFront. HTTP automatically redirected to HTTPS. | `02-shared-services/acm`<br/>`03-applications/apigateway` |
| **Change Control & Approval** | Environment branch segregation (`dev` → `staging` → `main` → `prod`). **CAB approval gate** enforced on `prod`. | `02_BRANCHING_AND_GOVERNANCE_SOP.md` |
| **Least Privilege & Identity** | AWS IAM Identity Center SSO with short-lived session tokens. IRSA (IAM Roles for Service Accounts) on EKS. | `00-foundation/identity-center`<br/>`03-applications/iam` |
| **Audit Logging (WORM)** | Multi-region CloudTrail, GuardDuty, and Security Hub logs aggregated to an isolated Archive account with S3 Object Lock. | `00-foundation/landing-zone` |

---

## 2. Disaster Recovery (DR) Architectural Standards

### RPO & RTO Targets:
- **Recovery Point Objective (RPO):** < 15 Minutes (Aurora RDS Global Databases & EFS Cross-Region Replication).
- **Recovery Time Objective (RTO):** < 1 Hour (Automated Route 53 DNS Failover).

```text
┌─────────────────────────────────────────────────────────────────────────────────────────┐
│                              MULTI-REGION DISASTER RECOVERY                             │
├─────────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                         │
│   PRIMARY REGION (us-east-2)                 SECONDARY DR REGION (us-east-1)           │
│   ──────────────────────────                 ───────────────────────────────           │
│   • Active EKS Workloads                     • Warm Standby EKS Cluster                │
│   • Aurora RDS Primary                       • Aurora RDS Read Replica (Global DB)     │
│   • Primary Route 53 Health Checks           • Secondary Route 53 Failover Target      │
│   • Keycloak HA Active                       • Keycloak HA Passive Standby             │
│                                                                                         │
│                     Route 53 DNS Failover (Automatic Latency/Health Routing)          │
└─────────────────────────────────────────────────────────────────────────────────────────┘
```

---

## 3. Mandatory Security Controls Checklist

1. [ ] **No Public IPs:** Zero EC2 or EKS nodes in public subnets.
2. [ ] **No Hardcoded Secrets:** AWS Secrets Manager for all DB passwords & API keys.
3. [ ] **KMS Rotation:** `enable_key_rotation = true` on all CMK keys.
4. [ ] **S3 Bucket Encryption:** `aws_s3_bucket_server_side_encryption_configuration` enforced.
5. [ ] **S3 Public Access Block:** `aws_s3_bucket_public_access_block` enabled globally.
