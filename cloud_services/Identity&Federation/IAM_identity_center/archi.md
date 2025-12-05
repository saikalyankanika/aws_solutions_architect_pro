# AWS IAM Identity Center (Successor to AWS SSO)

📍 Business Requirement:
- Enable **Single Sign-On** for all AWS Organization accounts and business SaaS apps
- Centralize user, group, and permission management
- Reduce IAM user sprawl and simplify access governance
- Support hybrid identity (AD integration) for enterprise environments

📦 Components:
| Component | Why |
|----------|-----|
| **IAM Identity Center** | Central SSO and permissions hub for AWS accounts & SaaS apps |
| **Identity Source** | Where users & groups are stored (Built-in / AD / Okta / OneLogin etc.) |
| **Permission Sets** | Collections of IAM policies assigned to user/groups across accounts |
| **ABAC Rules** | Attribute-based access (example: CostCenter = “Dev”) |

💣 Failure Modes:
- Wrong permission set → production access exposure
- Identity source switch requires re-mapping → outage risk
- SAML metadata expiration → login failures
- Not applying least privilege → audit/compliance issues

🎭 Trade-offs:
- **Built-in identity store**: no external dependency but lacks enterprise federation features
- **AD/SSO Providers Integration**: enterprise-grade controls but requires SAML setup + network (VPN/DX for on-prem AD)
- IAM Users inside accounts are less secure → Identity Center preferred

🌍 DR Strategy (RTO/RPO):
- Redundant availability → AWS-managed high availability
- Federation identity source requires its own DR (Okta/AD)
- Backup critical policies/permission sets via Terraform/CloudFormation

Whiteboard prompt:
> Centralized access via IAM Identity Center in Management Account,  
> integrated with AD/Okta, permission sets applied by OU via AWS Organizations.

---

### 🧠 Key Notes / Exam Learnings

```md
- IAM Identity Center replaces AWS SSO (new name only)
- Identity sources:
  - Built-in users & groups
  - External IdP: AD Connector, AD Federation, Okta, OneLogin
- Permission Sets:
  - Convert to AWS IAM Role per account
  - Support inline & managed IAM policies
- ABAC support using user attributes (department, location, costCenter)
- Works with:
  - AWS Accounts (via Organization)
  - SAML2.0 Business Apps (Salesforce, Box, M365)
  - Windows EC2 Instances (domain join required)
- Federated login → **NO IAM Credentials stored**
- One interface to manage access across all linked accounts
