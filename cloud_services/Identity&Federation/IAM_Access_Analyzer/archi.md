# Architecture Pattern – IAM Access Analyzer

## 📍 Business Requirement:
- Detect unintended external access to AWS resources
- Maintain least-privilege security posture continuously
- Automate IAM policy validation and auditing
- Control cross-account sharing in multi-account environments
- Generate fine-grained IAM policies automatically

---

## 📦 Components:

| Component | Why |
|----------|-----|
| IAM Access Analyzer | Core service to detect external sharing |
| Zone of Trust (Account/Organization) | Defines what is considered internal vs. external access |
| Resource-based Policies | Access exposure is detected only when these are used |
| CloudTrail Logs | Used for generating fine-grained IAM policies |
| Policy Validation Engine | Detects errors/security issues in policy JSON |

---

## 💣 Failure Modes:
- Analyzer not enabled → **no monitoring** of external access
- Incorrect Zone of Trust → false positives or missed risks
- Public S3 buckets, roles, or keys overlooked without alerts
- Over-permissive policies created before validation
- CloudTrail disabled → Policy Generation cannot function
- Findings ignored → continued security exposure

---

## 🌍 DR Strategy (RTO/RPO):
> IAM Access Analyzer is a **global service** — no RTO/RPO considerations for outages  
Focus on:
- Version-controlled IAM policy definitions via IaC (Terraform/CFN)
- Automated compliance scans integrated into CI/CD
- Alerting pipelines for new findings

---

## 🎭 Trade-offs:

| Choice | Pros | Cons |
|--------|------|------|
| Continuous analyzer | Real-time detection | Possible operational noise if not tuned |
| Organization-level trust zone | Better multi-account security | More setup complexity |
| Policy Validation | Prevents risky deployments | Requires team to fix findings before deploy |
| Policy Generation | Least-privilege from real usage | Generated permissions may miss unused future actions |

---

## 🎯 Purpose
- Detect which **AWS resources are shared externally** (outside your AWS Account or Org)
- Identify unintended access exposure
- Improve IAM policy security and correctness

---

## 🔍 What It Analyzes
Resources where **resource-based policies** are applied:

- S3 Buckets
- IAM Roles
- KMS Keys
- Lambda Functions & Layers
- SQS Queues
- Secrets Manager Secrets
- Kinesis Data Streams

---

## 🛡 Zone of Trust
- You can define **trust boundaries** as:
  - Single AWS Account
  - Entire AWS Organization

➡ **Any external access beyond this zone** → flagged as a **finding**

---

## 🧠 How It Works
1. Continuously monitors resource-based policies  
2. Detects if a principal **outside** the trusted zone has access  
3. Generates security **findings** with remediation suggestions

Example Findings:
- S3 bucket publicly accessible
- IAM role assumable from external account
- KMS key shared with unknown principals

---

## 🧰 Additional Features

### ✔ Policy Validation
- Validates IAM & resource policies for:
  - Grammar
  - Security best practices
- Highlights:
  - Errors
  - General warnings
  - Security warnings
  - Suggestions

➡ Helps prevent risky permissions before deployment

---

### ✔ Policy Generation
- Automatically generates **least-privilege IAM policies**
- Uses **CloudTrail access logs (up to 90 days)**
- Produces granular **Actions + Services** actually used

➡ Great for replacing overly-broad policies

---

## 🚨 When to Use IAM Access Analyzer
- Reviewing new environments or accounts
- Auditing permissions during migrations
- Checking 3rd-party integrations
- Ensuring zero trust principles
- Securing resources before compliance audits

---

## 🔐 Best Practices
- Enable continuously in all accounts
- Define clear **Zones of Trust**
- Integrate analyzer findings into:
  - CI/CD deployment checks
  - Security alerts
- Use policy validation **before** creating policies
- Replace wildcard permissions using policy generation results

---

## 🧩 Outcome
IAM Access Analyzer ensures:
- Secure access boundaries  
- No unintentional external exposure  
- Correct, least-privilege policies  
- Continuous compliance & monitoring  
