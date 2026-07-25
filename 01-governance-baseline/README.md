# AWS Security Governance Baseline

## Project Title
**AWS Security Governance Baseline - Multi-Framework Compliance Architecture** [cite: 1]

## Overview
A fully documented, Infrastructure-as-Code (IaC) deployed security baseline for a single AWS account. This project establishes core security guardrails—including IAM roles with permission boundaries, an encrypted and immutable S3 audit log bucket, AWS Config compliance rules, CloudTrail, GuardDuty, and KMS with enforced role separation [cite: 1]. Every deployed control is mapped explicitly to the Monetary Authority of Singapore Technology Risk Management (MAS TRM) framework, alongside 12 other global standards, demonstrating enterprise-grade regulatory compliance [cite: 1]. 

## How it Will Help the Business
This architecture provides automated, continuous compliance validation, drastically reducing manual audit overhead. By establishing strict access ceilings and tamper-proof audit trails, the business minimizes the risk of data exfiltration and configuration drift. It lays a foundational security posture that allows organizations—particularly financial institutions operating under strict jurisdictional regulations—to scale securely in the cloud without compromising agility.

## Why it's Important
Unchecked privilege escalation and untracked API activity are leading causes of cloud breaches. This baseline ensures that even if individual policies are misconfigured, maximum access is hard-capped. It guarantees that security and audit logs remain immutable for years, ensuring verifiable forensic evidence is always available during an incident or regulatory audit [cite: 1]. 

## Architecture Diagram
*(Include a visual architecture diagram here in the final repository)*
*   **Users/Roles:** `GovernanceAuditorRole`, `GovernanceSecurityOpsRole` interacting with boundaries [cite: 1].
*   **Logging:** Multi-region CloudTrail delivering SHA-256 validated logs to an S3 Bucket [cite: 1].
*   **Storage:** S3 Bucket with SSE-KMS, Object Lock (7-Year Governance Mode), and Public Access Block [cite: 1].
*   **Monitoring/Detection:** AWS Config (evaluating 10 managed rules) and Amazon GuardDuty (behavioral threat detection) [cite: 1].
*   **Cryptography:** KMS Customer Managed Key (CMK) separating Key Administrators from Key Users [cite: 1].

## Security Layers & Security Checks to Implement
*   **Identity Layer:** Least-privilege IAM roles governed by strict permission boundaries (`GovernancePermissionBoundary`) [cite: 1]. 
*   **Storage & Data Security Layer:** Amazon S3 configuration denying HTTP requests (TLS enforcement), blocking all public access, and forcing SSE-KMS encryption [cite: 1].
*   **Detection & Visibility Layer:** AWS Config for continuous compliance evaluation, multi-region CloudTrail with log file validation, and GuardDuty for behavioral anomaly detection [cite: 1].
*   **Cryptographic Layer:** Enforced Segregation of Duties (SoD) on KMS encryption keys, separating key management from decryption capabilities, mirroring strict enterprise database security principles [cite: 1].

## What You'll Build
*   IAM roles with permission boundaries mapping to MAS TRM 9.1.2 [cite: 1].
*   An encrypted, immutable S3 audit log bucket meeting MAS TRM 11.2 [cite: 1].
*   An AWS Config continuous evaluation setup with rules for MFA, S3 public access, KMS rotation, and IAM credential lifecycle [cite: 1].
*   A multi-region AWS CloudTrail with log integrity validation [cite: 1].
*   Amazon GuardDuty detectors spanning API activity and S3/EC2 protection [cite: 1].
*   A centralized AWS KMS Customer Managed Key demonstrating cryptographic role separation [cite: 1].

## Project Structure & Flow of the Project
```text
├── 01-governance-baseline/
│   ├── cloudformation/
│   │   ├── iam-roles.yaml           # IAM roles and boundaries [cite: 1]
│   │   ├── s3-audit-bucket.yaml     # Immutable log storage [cite: 1]
│   │   ├── config-rules.yaml        # MAS TRM aligned rules [cite: 1]
│   │   └── cloudtrail.yaml          # Audit trail configuration [cite: 1]
│   ├── docs/
│   │   └── mas-trm-control-mapping.md # 13-framework matrix [cite: 1]
│   ├── screenshots/                 # Validation artifacts [cite: 1]
│   └── README.md
└── .github/workflows/
    └── validate-p1.yml              # CI pipeline for cfn-lint [cite: 1]
```

## Implementation Steps
1.  **Deploy IAM Roles:** Apply permission boundaries to cap maximum privileges, followed by creating Auditor and SecOps roles [cite: 1].
2.  **Establish Secure Storage:** Deploy the S3 audit bucket with SSE-KMS, public access blocks, and 7-year Object Lock [cite: 1].
3.  **Enable Configuration Monitoring:** Activate AWS Config and deploy mapping rules via CloudFormation to evaluate resource compliance [cite: 1].
4.  **Activate Threat Detection:** Enable GuardDuty, configure S3/EC2 protection, and deploy multi-region CloudTrail [cite: 1].
5.  **Configure Cryptography:** Create a KMS key, update the key policy to separate administrators from users, and enable annual rotation [cite: 1].
6.  **CI/CD Validation:** Implement GitHub Actions with `cfn-lint` on feature branches to validate Infrastructure as Code prior to merges [cite: 1].

## Key Decisions to Document
*   **Permission Boundaries over Policies:** Chosen to establish an unbreachable permission ceiling, preventing future privilege creep [cite: 1].
*   **Object Lock 'GOVERNANCE' vs 'COMPLIANCE' Mode:** Governance mode selected for this baseline to allow highly audited, exceptional overrides, whereas Compliance mode is irreversible [cite: 1].
*   **KMS Segregation of Duties:** Ensuring the Key Administrator cannot decrypt data, and the Key User cannot manage the key lifecycle, preventing single points of cryptographic failure [cite: 1].
*   **CI/CD Pipeline Validation:** Enforcing `cfn-lint` on all pull requests ensures no malformed templates reach the environment [cite: 1].

## Security Considerations
*   **Key Principles and Characteristics:** Defense in depth, least privilege, immutable logging, and continuous compliance evaluation.
*   **Key Capabilities:** Tamper-proof forensic trails, automated drift detection, and behavioral anomaly flagging.
*   **Basic Components/Features:** CloudFormation (IaC), AWS Config (CSPM), CloudTrail (Audit), GuardDuty (Threat Detection).
*   **Prerequisites:** AWS Account (Free Tier viable), AWS CLI configured, GitHub repository for CI/CD setup [cite: 1].
*   **High-Level Workflow:** Code commit -> CI linting -> CFN Deployment -> Continuous Config Evaluation -> Audit Logging -> Threat Detection Alerting.

## Controls Mapping
| Framework / Standard | Portfolio Control / Evidence |
| :--- | :--- |
| **MAS TRM 2021** | Config root-MFA rule (§9.1.1), KMS SSE (§8.3), CloudTrail (§11.2) [cite: 1] |
| **ISO 27001** | IAM roles (A.5.15), KMS (A.8.24), CloudTrail (A.8.15) [cite: 1] |
| **NIST SP 800-53 (Rev.5)** | IAM account mgmt (AC-2), S3 Object Lock (AU-9), KMS (SC-12) [cite: 1] |
| **GDPR** | Encryption at rest/in transit (Art. 32), Access controls [cite: 1] |
| **PDPA** | SSE-KMS + Block Public Access on audit bucket [cite: 1] |

## Threats Mitigated
*   **Compromised Root Credentials:** Mitigated by MFA enforcement and Config alerting (T1078.004) [cite: 1].
*   **Accidental S3 Public Exposure:** Mitigated by Block Public Access & Deny-HTTP bucket policies (T1530) [cite: 1].
*   **Audit Log Tampering:** Mitigated by CloudTrail validation and S3 Object Lock (T1562.008) [cite: 1].
*   **Encryption Key Misuse:** Mitigated by KMS role separation and automated rotation [cite: 1].
*   **Configuration Drift:** Mitigated by AWS Config continuous evaluations [cite: 1].

## Trade-off Pointers
*   **Config Detection Lag:** AWS Config evaluates on a schedule, leading to slight detection lag. For near-real-time detection on critical resources, Amazon EventBridge should be paired with Config (addressed in future iterations) [cite: 1].
*   **Object Lock Flexibility vs Strictness:** Governance mode allows recovery from errors with special permissions, while Compliance mode offers stricter immutability but higher operational risk [cite: 1].

## Enterprise Scale & Alternate Solutions
*   **How it scales in Cloud (Enterprise Consideration):** In a production multi-account environment, these CloudFormation templates would be deployed via StackSets from the AWS Organizations management account, enforced as non-negotiable baselines via AWS Control Tower guardrails, with Config conformance packs generating unified compliance dashboards [cite: 1]. 
*   **Alternate Solutions & Comparison:** Terraform could replace CloudFormation for multi-cloud deployments. Third-party CSPMs (like Prisma Cloud or Wiz) could replace AWS Config for cross-cloud visibility, though native AWS tools offer tighter ecosystem integration at lower initial complexity.
*   **Compensating Controls:** If KMS CMKs are cost-prohibitive, AWS Managed Keys offer a baseline level of encryption, though lacking granular role separation. 

## Deliverables Checklist
- [x] CloudFormation templates (`iam-roles.yaml`, `s3-audit-bucket.yaml`, `config-rules.yaml`, `cloudtrail.yaml`) [cite: 1]
- [x] MAS TRM and multi-framework mapping document [cite: 1]
- [x] GitHub Actions validation pipeline (`.github/workflows/validate-p1.yml`) [cite: 1]
- [x] Verification screenshots demonstrating live enforcement [cite: 1]

## Questions to Answer in Documentation (Interview Prep)
**Q: Why use permission boundaries instead of just writing tighter IAM policies?**
A: A permission boundary acts as an absolute ceiling. It caps the maximum permissions a role can ever have, protecting against privilege creep introduced by other administrators later on—a capability standard policies cannot achieve [cite: 1].

**Q: Why GOVERNANCE mode Object Lock on the audit bucket, not COMPLIANCE mode?**
A: Compliance mode is completely immutable even to the root user, making it operationally risky for genuine mistakes. Governance mode prevents standard deletion but allows a narrowly-scoped IAM override in highly audited, exceptional cases [cite: 1].

**Q: What is the weakest control in this baseline, and how would you strengthen it?**
A: AWS Config relies on scheduled evaluations, creating a detection delay. To strengthen this, I would integrate EventBridge event-pattern rules for near-real-time detection of high-risk configuration changes [cite: 1].

## Reference Materials
*   [AWS CloudFormation Documentation](https://docs.aws.amazon.com/cloudformation/)
*   [Monetary Authority of Singapore (MAS) TRM Guidelines](https://www.mas.gov.sg/regulation/guidelines/technology-risk-management-guidelines)
*   [AWS Security Best Practices in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)
*   [Amazon S3 Object Lock](https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lock.html)

---
**High-Level Summary**
This project establishes a robust, highly-governed AWS security foundation using native Infrastructure-as-Code. It integrates strict identity boundaries, immutable storage, behavioral threat detection, and cryptographic segregation. Designed for strict regulatory environments like MAS TRM, it demonstrates how isolated account security practices smoothly scale into automated, multi-account enterprise guardrails.
