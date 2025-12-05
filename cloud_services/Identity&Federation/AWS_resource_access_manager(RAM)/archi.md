# AWS Resource Access Manager (RAM)

📍 Business Requirement:
- Share AWS resources **securely and cost-efficiently** across multiple AWS accounts
- Enable centralized networking while keeping workloads isolated
- Prevent unnecessary duplication of infrastructure

📦 Components:
| Component | Why |
|----------|-----|
| **Resource Shares** | Define which resources are shared and with whom |
| **VPC Subnets Sharing** | Shared networking allowing private IP connectivity across accounts |
| **Prefix Lists** | Simplified network rule management for multiple accounts |
| **Route 53 Resolver Rules** | Centralized DNS forwarding across accounts |
| **Transit Gateway Sharing** | Multi-account network connectivity model |
| **Others** (ACM PCA, Aurora, EC2 hosts, Glue Catalog…) | Enable centralized and scalable service access |

💣 Failure Modes:
- Misconfigured share → cross-account access blocked / apps unreachable
- Shared network trust boundary too wide → lateral movement risk
- Security groups referencing issues between accounts
- Compliance issues if accounts aren’t within the same AWS Organization (some features restricted)

🎭 Trade-offs:
- 🚫 Limited support: Cannot share Security Groups, default VPC
- 🛡 Participants can’t modify owners’ resources → secure
- 🔁 Operational complexity in IAM, routing, DNS across accounts
- 📈 Great for centralized teams, but boundaries must be governed via SCPs / VPC design

🌍 DR Strategy (RTO/RPO):
- DR depends on shared resource:
  - VPCs & DNS must be regionally replicated
  - Transit Gateway backups via route table duplication
- Validate failover routing & cross-account permissions in secondary region

Whiteboard prompt:
> Centralized VPC Subnet shared via RAM → multiple app accounts deploy EC2 in same network
> + SG references across accounts for secure private communication.

---

### 🧠 Key Notes / Exam Learnings

```md
- Share AWS resources across:
  - Any AWS account, OR
  - Across AWS Organization (preferred)
- Participants manage **their own** resources only
- Strong exam point: **Subnets can only be shared within Organization**
- Great use case: Multi-account apps in same trust boundary
- Prefix Lists:
  - Customer-managed prefix lists **can be shared**
  - AWS-managed prefix lists **cannot be modified/shared**
- Route 53 Resolver Outbound Rules can be shared → scalable DNS forwarding
- RAM helps avoid resource duplication → cost savings
