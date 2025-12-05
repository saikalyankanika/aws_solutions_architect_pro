# AWS Control Tower

📍 Business Requirement:
- Quickly set up a secure, governed multi-account AWS environment
- Enforce enterprise compliance from Day-1
- Standardize account creation and operational guardrails
- Reduce manual governance work for platform teams

📦 Components:
| Component | Why |
|----------|-----|
| **Landing Zone** | Preconfigured, secure multi-account structure (Log archive, Security, etc.) |
| **Account Factory** | Automated, compliant account provisioning using Service Catalog |
| **Guardrails** | Governance policies using SCP (preventive) and AWS Config (detective) |
| **Dashboard** | Organization-wide compliance monitoring & alerts |
| **AWS Organizations integration** | Enforce SCPs + OU-based governance at scale |

💣 Failure Modes:
- Lack of region governance → resources deployed in non-compliant regions
- SCP misconfiguration → break deployments / access
- Improper tagging enforcement → cost & audit issues
- Custom changes outside Control Tower → drift & non-compliance

🎭 Trade-offs:
- 🚀 Very easy + opinionated setup (best practices included)
- ❌ Less flexibility than custom Organizations setup
- Detective guardrails mean remediation is reactive, not preventive
- Guardrails require AWS Config → added cost

🌍 DR Strategy (RTO/RPO):
- Control Tower services are highly available (AWS-managed)
- Store and backup:
  - Guardrail configs (IaC)
  - Account baseline templates (Account Factory)
- Multi-region expansion requires additional setup / governance

Whiteboard prompt:
> Multi-account architecture with centralized governance using Control Tower Landing Zone,
> Account Factory automating compliant account creation, guardrails enforcing security baseline.

---

### 🧠 Key Notes / Exam Learnings

```md
- Control Tower runs on top of AWS Organizations
- Account Factory uses AWS Service Catalog to provision accounts
- Guardrail Types:
  - Preventive → SCPs
  - Detective → AWS Config rules + remediation (SNS / Lambda)
- Guardrail Levels:
  - Mandatory → always enforced
  - Strongly Recommended → AWS best practices
  - Elective → optional enterprise choices
- Detect → Monitor → Remediate workflow for config violations
- Best starting point for enterprise Landing Zone deployment
- Focus: Governance, compliance, least privilege across accounts
