# Architecture Pattern – Identity Federation in AWS

## 📍 Business Requirement:
- Allow external corporate/workforce users to access AWS without creating IAM users
- Centralize identity management outside AWS (e.g., AD, enterprise IdP)
- Enable SSO for AWS Console, CLI & application access
- Provide secure, temporary access with full auditing

## 📦 Components:
| Component | Why |
|----------|-----|
| Identity Provider (IdP) – ADFS / SAML / OIDC / Cognito | Authenticates users externally |
| IAM Role for Federation | Determines permissions after login |
| Trust Policy | Establishes federation trust with IdP |
| SAML Assertions / OIDC Tokens | Carry user identity attributes to AWS |
| AWS Sign-In Endpoint / STS | Issues temporary credentials after validation |
| Attribute Mapping / Session Tags | Fine-grained authorization using ABAC |
| CloudTrail | Logging and auditing user activity |

## 💣 Failure Modes:
- Certificate expiry from IdP → login failure
- Incorrect SAML attribute mapping → wrong access or denial
- Token replay attacks if token misuse allowed
- If IdP is down → users cannot access AWS
- Slow token refresh or session timeout → user disruption

## 🌍 DR Strategy (RTO/RPO):
- Redundant IdP servers (ADFS multi-node deployment)
- Backup signing certificates + rotation policies
- Use IAM Identity Center for managed availability (AWS handles DR)
- RTO: < 5 minutes with failover  
- RPO: Zero (identity updates synced from corporate IdP)

## 🎭 Trade-offs:
| Option | Pros | Cons |
|--------|------|------|
| SAML 2.0 Federation | Mature, supports corporate AD | Complex setup, legacy approach |
| IAM Identity Center | Best AWS experience, central control | Requires AWS-managed service adoption |
| Cognito Federation | Great for apps (web/mobile) | Dev integration required |
| Custom Identity Broker | Works for any IdP type | High operational + security burden |
| ABAC using session tags | Highly granular permissions | Requires enforced tagging standards |

## 🧠 Key Notes / Learnings:

### Why Identity Federation?
- No need to create/manage IAM users
- Users keep their corporate credentials
- Centralized onboarding/offboarding
- Policies evaluated at role assumption → least privilege access

### SAML 2.0 Federation
- Corporate IdP (ADFS, Okta, Ping, etc.)
- Under the hood: `AssumeRoleWithSAML`
- Provides AWS Console, CLI, API access
- Requires 2-way trust between IdP and AWS

### Web Identity Federation (Without Cognito)
- User authenticated by external IdP (Google/Facebook/OIDC)
- AWS recommends **not using this directly**
- Uses `AssumeRoleWithWebIdentity`

### Web Identity Federation (With Cognito)
- Preferred method for web/mobile applications
- Cognito “Token Vending Machine”
- Supports:
  - Anonymous users
  - MFA
  - Syncing identity profiles

### IAM Policy Variables for Federation
Used to map identity attributes for authorization, example keys:
- `cognito-identity.amazonaws.com:sub`
- `graph.facebook.com:id`
- `accounts.google.com:sub`
- `www.amazon.com:user_id`

### ADFS + Active Directory
- AD stores corporate identities
- ADFS issues SAML tokens for AWS login
- Enables SSO across apps including AWS Console

## Whiteboard prompt:
> Design a workforce SSO architecture where employees use their corporate AD credentials to securely log into multiple AWS accounts with least-privilege permissions.
