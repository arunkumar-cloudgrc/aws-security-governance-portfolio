# Compliance and Framework Mapping

This document maps Project 1 governance controls to major regulatory and cybersecurity frameworks.  
AI‑specific standards are intentionally marked **not applicable** — Project 1 has no AI component, and forcing a mapping would appear as padding to any reviewer.

| Framework / Standard | Reference | Portfolio Control / Evidence |
|-----------------------|------------|-------------------------------|
| MAS TRM 2021 | 9.1.1 MFA, 8.3 Encryption, 11.2 Logging | Config root‑MFA rule, KMS SSE, CloudTrail |
| ISO 27001 | Annex A 5.15, 8.24, 8.15 | IAM roles, KMS, CloudTrail |
| PDPA | Protection Obligation (security arrangements) | SSE‑KMS + Block Public Access on audit bucket |
| NIST SP 800‑53 Rev. 5 | AC‑2, AU‑9, SC‑12/SC‑13, CM‑6 | IAM account management, S3 Object Lock, KMS, Config |
| NIST CSF 2.0 | PROTECT: PR.AA, PR.DS | IAM permission boundaries + KMS encryption |
| GDPR | Art. 32 Security of Processing, Art. 5(1)(f) | Encryption at rest/in transit, access controls |
| ISO 27002 | 5.15 Access Control, 8.24 Cryptography, 8.15 Logging | IAM, KMS, CloudTrail |
| ISO 27017 | CLD.9.5.1 Segregation, CLD.12.4.5 Monitoring | KMS role separation, Config compliance monitoring |
| ISO 27018 | 11.1 PII Retention/Disposal in Logs | S3 Object Lock retention policy, lifecycle rules |

---

## Context

Each framework emphasizes **security, governance, and auditability**. Project 1 demonstrates how AWS native services can satisfy overlapping requirements across multiple standards:

- **MAS TRM**: Core regulatory driver for Singapore financial institutions — implemented through Config rules, KMS encryption, and CloudTrail logging.  
- **ISO 27001/27002/27017/27018**: International standards for information security, cloud governance, and privacy — enforced through IAM, KMS, and S3 Object Lock.  
- **NIST SP 800‑53 Rev. 5 and NIST CSF 2.0**: U.S. federal and industry frameworks emphasizing access control, encryption, and continuous monitoring — mapped via IAM boundaries, Config, and GuardDuty.  
- **GDPR and PDPA**: Data protection and privacy laws — addressed through encryption at rest/in transit and strict access controls on audit buckets.  

---

## Enterprise Scale Consideration

In a production multi‑account environment, these controls would be deployed via **CloudFormation StackSets** from a management account, enforced as **non‑negotiable baselines** through **AWS Control Tower guardrails**.  
**AWS Config conformance packs** would provide automated compliance evidence for audit submissions across frameworks, ensuring consistent governance without relying on individual account administrators.
