# AWS Organizations

📍 Business Requirement:
- Manage multiple AWS accounts centrally for governance, billing, compliance, and security
- Apply unified policies and enforce controls across environments (Prod, Dev, Finance, etc.)
- Simplify account provisioning and access management

📦 Components:
| Component | Why |
|----------|-----|
| **Root OU** | Top-level container grouping all accounts and OUs |
| **Management Account** | Central admin, billing, master access (SCP not applied) |
| **Member Accounts** | Workload-specific accounts (Prod/Dev/Test/Security/Logging) |
| **OrganizationAccountAccessRole** | Allows management account to assume admin role in member accounts |
| **Organizational Units (OU)** | Group accounts by environment, BU, regulatory needs |
| **Service Control Policies (SCP)** | Governance guardrails — allow/deny permissions across accounts |
| **Tag Policies** | Enforce standard tagging for cost, compliance, and access control |
| **Backup Policies** | Centralized backup enforcement for org workloads |
| **AI Opt-out Policies** | Unified control over AI service data usage across accounts |

💣 Failure Modes:
- Overly restrictive SCP → complete service lockout
- Misconfigured tag or backup policies block deployments
- Lack of dedicated security/logging account → compliance gaps
- RI/Savings Plan sharing disabled → increased cost
- Accidental removal from org without SCP protection

🌍 DR Strategy (RTO/RPO):
- Use separate **Security**, **Log Archive**, and **Backup** accounts for isolation
- Cross-account CloudTrail + centralized S3 log archival
- SCP protection to prevent accounts leaving the org (governance DR)

🎭 Trade-offs:
- Multi-account strategy → best isolation/security but more operational overhead
- One account w/ Multi-VPC → cheaper but risk of blast radius
- SCP powerful but requires careful design (deny takes precedence)
- Consolidated billing simplifies finance but requires strong access controls

Whiteboard prompt:
> Multi-account architecture using OUs (Prod/Dev/Test/Finance/Security),  
> SCP guardrails, centralized logging & billing, with cross-account role access.

---

### 🧠 Key Notes / Exam Learnings

```md
- SCP = global guardrails → applies to root user too (except management account)
- Explicit deny always overrides any allow
- SCPs do NOT grant permissions → IAM must still allow actions
- Trust path: every OU above must explicitly allow required permissions
- OrganizationAccountAccessRole auto-created for new accounts only
- Reserved Instance/Savings Plans discounts can be shared or disabled per account
- RI sharing: must be turned ON for both payer + member accounts
- OU strategies: security isolation, cost tracking, regulatory boundaries
- Strong recommendation: dedicated log-archive + security account
- To move account: Remove → Invite → Accept
