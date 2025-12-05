# Architecture Pattern – IAM Security Architecture

## 📍 Business Requirement
- Centralized authentication & authorization for AWS services
- Enforce least privilege access control
- Avoid long-term credentials — use STS-based short-term roles
- Support secure cross-account access
- Enable permissions delegation while preventing privilege escalation
- Fine-grained access using tags and conditions

---

## 📦 Components

| Component | Why |
|----------|-----|
| IAM Users | Human access with long-term credentials (use sparingly) |
| IAM Groups | Permission management for multiple users |
| IAM Roles | Short-term credentials via STS, secure cross-service access |
| EC2 Instance Roles | Automated rotating creds via metadata service |
| Service Roles | Allow AWS services to assume roles (Lambda, API GW, etc.) |
| Cross Account Roles | Secure external account access without shared keys |
| IAM Policies | Define permissions (AWS Managed / Customer / Inline) |
| Resource Policies | Direct policy on resource (S3, SQS, SNS…) |
| Permission Boundaries | Restrict maximum permissions to prevent escalation |
| Access Advisor / Analyzer | Audit access & detect external sharing |

---

## 💣 Failure Modes
- Explicit **DENY** breaking permissions unexpectedly
- Permission overly broad due to missing **Conditions**
- Inline policies → unmanaged & inconsistent
- Overuse of **AdministratorAccess/PowerUser**
- IMDSv1 exposure → credential theft risk
- Wrong trust policy in cross-account access allowing unauthorized use

---

## 🌍 DR Strategy (RTO/RPO)
- IAM is **global** → No traditional RTO/RPO concerns
- Ensure recoverability via:
  - IaC (Terraform/CloudFormation) for versioned policy definitions
  - AWS Organizations + SCP for governance
  - IAM configuration export for audit & rollback

---

## 🎭 Trade-offs

| Choice | Pros | Cons |
|--------|------|------|
| AWS Managed Policies | Quick setup, maintained by AWS | Often broad permissions |
| Customer Managed Policies | True least privilege | Requires design & maintenance |
| Resource-based Policies | Principal keeps original permissions | Complex trust logic |
| Cross-account Roles | Secure sharing using STS | Must configure trust + role switch |
| Permission Boundaries | Safe delegation | Harder to understand/debug |
| Inline Policies | Per-resource customization | Not reusable or auditable |

---

## 📌 IAM Key Concepts
- Policy JSON: **Effect, Action, Resource, Condition**
- **Explicit Deny** overrides **Allow**
- Condition operators:
  - StringEquals, NumericLessThan, Bool, IpAddress, ArnLike, Null, Date
- Policy Variables: `${aws:username}`, IP, MFA, token time, tags
- Role assumption → original permissions dropped
- Resource-based policies → original permissions retained

---

## 🔐 Best Practices
- Enforce **least privilege** always
- Use **roles instead of user access keys**
- Enable MFA for sensitive access
- Prefer **managed** or **customer managed policies** over inline
- Use **Access Analyzer** to restrict external resource sharing
- Apply **permission boundaries** for delegated IAM control
