# Architecture Pattern – AWS STS (Security Token Service) & Role Assumption

## 📍 Business Requirement:
- Provide secure temporary access to AWS resources without long-term credentials
- Enable cross-account or third-party access in a controlled way
- Enforce identity-based trust with auditability
- Support identity federation for external users / workforce
- Prevent confused-deputy problems using External IDs & tags

---

## 📦 Components:

| Component | Why |
|----------|-----|
| IAM Role | Defines allowed permissions to assume |
| STS | Generates temporary API credentials |
| Trust Policy | Defines **who can assume** the role |
| IAM Policy | Defines **what actions** are allowed |
| External ID | Protects against confused deputy in 3rd-party access |
| Session Tags | Enforce attribute-based access |
| CloudTrail | Track who assumed the role & actions |

---

## 💣 Failure Modes:
- Incorrect trust policies → unauthorized assumption
- Missing External ID when granting access to 3rd parties → **confused deputy risk**
- Expired/Revoked sessions → failed access in production flows
- Too long role sessions → increased risk of token exposure
- Privilege escalation if principal retains original permissions (incorrectly designed roles)

---

## 🌍 DR Strategy (RTO/RPO):
> IAM & STS are AWS control-plane — no DR maintenance required

Best practices:
- Store IAM roles/trust policies in IaC for rapid restoration
- Use session revocation condition: `AWSRevokeOlderSessions`
- Monitor CloudTrail for suspicious role assumptions

---

## 🎭 Trade-offs:

| Option | Pros | Cons |
|--------|------|------|
| Temporary STS credentials | Auto-expire, strong security | Operational handling of refresh |
| Same-account role assumption | Easy setup | Still requires trust definitions |
| Cross-account access | Strong separation of duties | Complex IAM & auditing |
| MFA on roles | Strong authentication | Increases login friction |
| Using Session Tags | Fine-grained ABAC controls | Requires well-planned tagging strategy |
| External ID | Prevents confused deputy | Need secure exchange during onboarding |

---

## 🧠 Key Notes / Learnings:

### 🔐 STS Temporary Credentials
- Validity: **15 minutes to 12 hours**
- When a role is assumed:
  ✔ Original permissions dropped  
  ✔ Only role permissions active  

### 🌍 Cross-Account Access
- Grant access:
  - Principal in Account A assumes role in Account B
  - Must allow `sts:AssumeRole` in trust policy
  - MFA can be enforced

### 🤝 Access for **Third-Party AWS Accounts**
- Required inputs:
  - **3rd-party AWS Account ID**
  - **External ID** (secret token between both parties)
- Protects against **Confused Deputy problem**
  - Makes sure only the intended party can assume the role
- IAM Access Analyzer identifies unintended external access

### 🏷 STS Session Tags
- Tags passed while assuming a role
- Used with condition keys like:
  - `aws:PrincipalTag`
  - Enables **Attribute-Based Access Control (ABAC)**
- Example: Only HR-tagged session can access HR-docs bucket

### 🧩 STS Important APIs
| API | Use Case |
|-----|---------|
| `AssumeRole` | Same/cross-account role assumption |
| `AssumeRoleWithSAML` | Enterprise identity with SSO via SAML |
| `AssumeRoleWithWebIdentity` | Login via OIDC/Facebook/Google/Cognito |
| `GetSessionToken` | MFA for IAM user / root creds |
| `GetFederationToken` | Proxy app assigns temporary creds to internal systems |

---

## Whiteboard prompt:
> “How does STS AssumeRole prevent credential sharing and secure cross-account access, especially for third-party access?”

