# AWS Directory Services

📍 Business Requirement:
- Provide centralized authentication and authorization for AWS resources and enterprise workloads
- Integrate AWS workloads with on-premises Active Directory
- Enable domain-join, user/group management, and SSO capabilities

📦 Components:
| Component | Why |
|----------|-----|
| **AWS Managed Microsoft AD** | Fully managed Microsoft AD in AWS supporting MFA, trust, and domain join |
| **AD Connector** | Proxy to forward authentication requests to on-prem AD, no user sync |
| **Simple AD** | Low-cost AD-compatible directory without trust support |

💣 Failure Modes:
- Loss of Direct Connect/VPN causes authentication failures when AD Connector is used
- Misconfigured trust relationships may deny access
- Region-wide outage if directory is not multi-region replicated

🌍 DR Strategy (RTO/RPO):
- Managed Microsoft AD: Multi-AZ by default + optional multi-region replication (low RTO/RPO)
- AD Connector: Depends on on-prem AD availability (high RTO/RPO if DX/VPN fails)
- Simple AD: Single-region only → restore from backups (longer RTO)

🎭 Trade-offs:
- Managed Microsoft AD → Most expensive but full features + trust + MFA
- AD Connector → Cheapest but depends fully on on-prem AD & no caching
- Simple AD → Cheap but no MFA, no RDS SQL integration, no trust support

Whiteboard prompt:
> A VPC with Managed Microsoft AD deployed in Multi-AZ,  
> EC2 and RDS domain-joined, optional forest trust to on-prem AD over VPN/DX.

---

### 🧠 Key Notes / Exam Learnings
```md
- Managed Microsoft AD = Full AD + MFA + Trust + Multi-AZ + Multi-Region
- AD Connector = Proxy → No caching, no trust, relies 100% on on-prem AD
- Simple AD = Cheapest but limited features; powered by Samba 4
- Trust = Not the same as replication
- EC2 domain join from multiple accounts/VPCs supported with Managed AD
- Required when using SQL Server on RDS with Windows Authentication
