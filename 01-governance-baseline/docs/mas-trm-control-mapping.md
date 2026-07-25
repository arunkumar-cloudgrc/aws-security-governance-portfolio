# MAS TRM Control Mapping Document

This document maps Monetary Authority of Singapore (MAS) Technology Risk Management (TRM) requirements to AWS governance controls deployed in Project 1. Each control is implemented via CloudFormation templates and validated through AWS Config rules, IAM policies, and service configurations.

| MAS TRM Section | Requirement                  | AWS Control Deployed                                | Implementation                                                                 |
|-----------------|------------------------------|----------------------------------------------------|---------------------------------------------------------------------------------|
| 9.1.1           | MFA on privileged accounts   | Config: ROOT_ACCOUNT_MFA_ENABLED                   | Automated compliance check, alerts on violation                                |
| 9.1.2           | Least-privilege access       | IAM permission boundaries                          | GovernancePermissionBoundary caps all custom roles                             |
| 9.1.3           | Access key rotation          | Config: ACCESS_KEYS_ROTATED                        | Flags keys unused/unrotated >90 days                                            |
| 9.2.1           | Data access controls         | S3 Block Public Access + bucket policy             | All public access blocked; HTTP denied by policy                               |
| 8.3.1           | Encryption at rest           | SSE-KMS on audit S3 bucket                         | KMS CMK with annual rotation enforced                                          |
| 8.3.2           | Key management               | Config: CMK_BACKING_KEY_ROTATION_ENABLED           | Automated rotation compliance                                                  |
| 11.2.1          | Audit logging                | CloudTrail multi-region trail                      | All management events, log file validation enabled                             |
| 11.2.2          | Log integrity                | CloudTrail log file validation                     | SHA-256 digest files for tamper detection                                      |
| 11.2.3          | Log retention                | S3 Object Lock — GOVERNANCE mode, 7 years          | Immutable audit logs meeting MAS retention expectations                        |
| 10.1.1 / 12.1.1 | Threat detection             | Amazon GuardDuty                                   | Behavioural threat detection across all API activity                           |

---

## Enterprise Scale Consideration

In a production multi-account environment, these controls would be deployed across all accounts using **CloudFormation StackSets** from a management account. They would be enforced as **non-negotiable baselines** via **AWS Control Tower guardrails**, ensuring consistent governance across the enterprise.  

Additionally, **AWS Config conformance packs** would provide automated compliance evidence, streamlining MAS TRM audit submissions. This approach ensures that governance is not dependent on individual account administrators but centrally managed and uniformly applied.

---

## Context

- **Regulatory Drivers**: Each control is mapped directly to MAS TRM sections, with cross-references to ISO 27001, NIST 800-53, GDPR, and PDPA where applicable.  
- **Governance Discipline**: Controls enforce **least privilege**, **encryption at rest**, **immutable audit logging**, and **continuous compliance monitoring**.  
- **Audit Readiness**: Automated checks (Config rules, GuardDuty findings, CloudTrail validation) provide evidence trails for regulators and auditors.  
- **Scalability**: Designed for **enterprise rollout** via StackSets and Control Tower, ensuring uniformity across hundreds of accounts.  

> *Enterprise scale consideration: In a production multi-account environment, these CloudFormation templates would be deployed via StackSets from the management account to all member accounts, ensuring uniform governance controls without relying on individual account administrators.*
