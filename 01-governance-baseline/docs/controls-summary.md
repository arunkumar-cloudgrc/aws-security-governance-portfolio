# Controls Summary

This section summarizes the governance and security controls deployed in Project 1. Each control is mapped to its corresponding AWS service, ensuring compliance with MAS TRM, ISO, NIST, GDPR, and PDPA requirements.

| Control | AWS Service |
|---------|-------------|
| Least‑privilege IAM roles with permission boundaries | AWS IAM |
| Encrypted, versioned, immutable audit‑log storage | Amazon S3 (SSE‑KMS, Object Lock, Versioning) |
| Continuous configuration compliance evaluation | AWS Config (10 managed rules) |
| Immutable, integrity‑validated audit trail | AWS CloudTrail (multi‑region, log file validation) |
| Behavioural threat detection | Amazon GuardDuty |
| Centralised key management with role separation | AWS KMS |
| Repeatable, version‑controlled governance baseline | AWS CloudFormation |

---

## Context

- **IAM**: Enforces least‑privilege access through permission boundaries, ensuring no custom role exceeds governance limits.  
- **S3 with SSE‑KMS + Object Lock**: Provides encrypted, immutable storage for audit logs, meeting MAS TRM and GDPR retention requirements.  
- **AWS Config**: Continuously evaluates compliance against 10 managed rules, automatically flagging violations.  
- **CloudTrail**: Delivers a multi‑region, integrity‑validated audit trail with SHA‑256 digests for tamper detection.  
- **GuardDuty**: Adds behavioural threat detection across all API activity, strengthening operational security.  
- **KMS**: Implements centralised encryption governance with strict role separation (admins manage keys, auditors use keys).  
- **CloudFormation**: Ensures governance baselines are repeatable, version‑controlled, and scalable across accounts.  

---

## Enterprise Scale Consideration

In a production multi‑account environment, these controls would be deployed via **CloudFormation StackSets** from the management account.  
They would be enforced as **non‑negotiable baselines** through **AWS Control Tower guardrails**, with **AWS Config conformance packs** providing automated compliance evidence for audit submissions.
