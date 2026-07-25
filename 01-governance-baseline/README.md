# 🔐 Enterprise AWS Security Governance: Mapping Cloud Controls to Global Standards

## Overview

A fully documented, deployed security baseline for a single AWS account. It deploys a complete, single-account security baseline for a financial-services-style AWS environment, entirely on AWS Free Tier, entirely as Infrastructure-as-Code.

Rather than treating "security baseline" as a checklist of services turned on, this project treats it as a **governance artefact**: every control is deployed with a named regulatory driver, every control has an evidence trail, and every control is mapped to the specific framework clause it satisfies. The goal is to demonstrate the way a Cloud Security Architect or Cloud Governance Lead actually thinks ; regulatory requirement → control design → technical implementation → auditable evidence ; not just "here are some AWS services I turned on." By aligning with OWASP Top 10:2025 and CSA Top Threats 2025, this project demonstrates that governance controls are not just compliance artifacts ; they actively remediate real attack scenarios.

**Author context:** Built by a 20-year enterprise security practitioner (IBM Guardium DAM, Tripwire FIM, enterprise IAM governance) transitioning that governance discipline into a native AWS control plane.

---

## How It Will Help the Business

| Business problem | How this project addresses it |
|---|---|
| Manual security reviews don't scale across accounts/regions | Every control is CloudFormation ; repeatable, version-controlled, deployable via StackSets across an entire AWS Organization |
| Audit prep is slow and evidence-gathering is manual | Config + CloudTrail generate **continuous, automated compliance evidence** instead of point-in-time manual attestations |
| Regulatory scope keeps expanding (MAS TRM → GDPR → EU AI Act as firms go multi-jurisdiction) | Controls are mapped once, against 13 frameworks simultaneously, so expanding into a new jurisdiction is a mapping exercise, not a rebuild |
| Cloud misconfiguration remains one of the most common root causes of breach | Guardrails (permission boundaries, Block Public Access, mandatory encryption) prevent entire classes of misconfiguration by design, not by review |
| Security review is often a bottleneck to shipping | A CI-validated (`cfn-lint`) IaC baseline means new infrastructure changes are checked automatically before merge, not manually before launch |

---

## Why It's Important

Financial institutions operate at the intersection of MAS TRM (technology risk governance), GDPR, PDPA (data protection), and the EU AI Act when serving global customers or entities. A Cloud Security Architect in this space isn’t hired to master a single framework; they’re hired to reconcile several at once, codify them into enforceable controls, and produce evidence that regulators or auditors can actually inspect.

---

## What You'll Build

- **IAM governance layer** ; a `GovernancePermissionBoundary` managed policy + two least-privilege roles (`GovernanceAuditorRole`, `GovernanceSecurityOpsRole`)
- **Immutable audit-log store** ; SSE-KMS encrypted S3 bucket, versioning enabled, **Object Lock (GOVERNANCE mode, 7-year retention)**, HTTP access explicitly denied
- **Compliance-as-code** ; 10 AWS Config managed rules, each named and mapped to a specific MAS TRM clause
- **Threat detection** ; Amazon GuardDuty with S3 Protection and EC2 Malware Protection enabled
- **Immutable audit trail** ; multi-region AWS CloudTrail, log file validation enabled
- **Key management with role separation** ; a customer-managed KMS key where the admin role cannot decrypt and the decrypt role cannot administer
- **Governance documentation** ; a 13-framework control-mapping document, this README, and a GitHub Actions CI pipeline

---

## Architecture Diagram

```mermaid
flowchart TB
    subgraph IAM["🔑 Identity Layer"]
        PB["GovernancePermissionBoundary\n(max-permission ceiling)"]
        AR["GovernanceAuditorRole\n(read-only)"]
        SR["GovernanceSecurityOpsRole\n(detection & response)"]
        PB --> AR
        PB --> SR
    end

    subgraph DATA["🗄️ Data Protection Layer"]
        S3["S3 Audit Log Bucket\nSSE-KMS · Versioned\nObject Lock (GOVERNANCE, 7yr)\nBlock Public Access: ON"]
        KMS["KMS Customer-Managed Key\nAdmin ≠ User (role separation)\nAnnual auto-rotation"]
        KMS -->|encrypts| S3
    end

    subgraph AUDIT["📜 Audit & Logging Layer"]
        CT["AWS CloudTrail\nMulti-region · Log file validation"]
        CT -->|delivers logs to| S3
    end

    subgraph COMPLY["✅ Compliance-as-Code Layer"]
        CFG["AWS Config\n10 managed rules\nmas-trm-* naming convention"]
        CFG -.evaluates.-> IAM
        CFG -.evaluates.-> DATA
        CFG -.evaluates.-> AUDIT
    end

    subgraph DETECT["🛡️ Detection Layer"]
        GD["Amazon GuardDuty\nBehavioural threat detection\nS3 + EC2 Malware Protection"]
        CT -.feeds.-> GD
    end

    AR -.audits.-> COMPLY
    SR -.responds to.-> DETECT

    style IAM fill:#1F3864,color:#fff
    style DATA fill:#2E74B5,color:#fff
    style AUDIT fill:#2E74B5,color:#fff
    style COMPLY fill:#548235,color:#fff
    style DETECT fill:#C00000,color:#fff
```

*Diagram note: this is the account-level governance baseline ; it deploys ahead of, and independently from, any application workload VPC. See [Security Considerations](#security-considerations) for explicit scope boundaries.*

---

## Security Layers Implemented

| Layer | Control | Check performed |
|---|---|---|
| **1. Identity** | Permission boundaries on every custom role | Config rule confirms no role exceeds the boundary; boundary caps effective permissions regardless of attached policy |
| **2. Privileged access** | Root account MFA | `ROOT_ACCOUNT_MFA_ENABLED` Config rule + CloudWatch alarm on root login |
| **3. Data at rest** | SSE-KMS on all audit data | `s3-bucket-server-side-encryption-enabled` Config rule |
| **4. Data in transit** | TLS-only bucket policy | `s3-bucket-ssl-requests-only` Config rule + explicit Deny-HTTP statement |
| **5. Data exposure** | Public access blocking | `S3_BUCKET_LEVEL_PUBLIC_ACCESS_PROHIBITED` Config rule, all 4 Block Public Access settings enabled |
| **6. Audit integrity** | Immutable, validated logs | CloudTrail log file validation + S3 Object Lock GOVERNANCE mode |
| **7. Key governance** | Segregation of duties on encryption | KMS key policy ; admin principal excluded from `kms:Decrypt` |
| **8. Credential hygiene** | Access key rotation | `access-keys-rotated` Config rule, 90-day threshold |
| **9. Behavioural detection** | Anomaly/threat detection | GuardDuty across CloudTrail, VPC Flow, and DNS logs |
| **10. Configuration drift** | Continuous compliance evaluation | 10 AWS Config managed rules re-evaluated automatically on change |

---

## Project Structure

```
01-governance-baseline/
├── cloudformation/
│   ├── iam-roles.yaml           # Permission boundary + Auditor/SecurityOps roles
│   ├── s3-audit-bucket.yaml     # Encrypted, immutable audit-log bucket
│   ├── config-rules.yaml        # 10 managed Config rules, MAS TRM-mapped
│   └── cloudtrail.yaml          # Multi-region trail with log file validation
├── docs/
│   ├── mas-trm-control-mapping.md
│   └── kms-role-separation.md
├── screenshots/
│   ├── root-mfa-enabled.png
│   ├── iamadmin-mfa-enabled.png
│   ├── iam-roles.png
│   ├── iam-permission-boundary.png
│   ├── s3-encryption.png
│   ├── config-compliance-dashboard.png
│   ├── guardduty-active.png
│   ├── cloudtrail.png
│   └── kms-key-policy.png
└── README.md                    # This file

.github/workflows/
└── validate-p1.yml              # cfn-lint CI, runs on every push/PR to this folder
```

**Flow:** First enable MFA on the root account, create an iamadmin role for administrative tasks, with MFA enforced. , IAM roles/boundary deployed (identity foundation) → S3 audit bucket + KMS key (where evidence lands) → CloudTrail (start generating evidence) → Config rules (start evaluating compliance) → GuardDuty (start detecting threats) → documentation (make it auditable) → CI (make it enforceable going forward).

---

## Implementation Steps

> Full click-by-click console steps and complete AWS CLI commands live in the portfolio build guide ; this is the summary view for anyone reviewing the repo.

1. **Deploy IAM roles with permission boundaries** ; `aws cloudformation deploy --template-file cloudformation/iam-roles.yaml --stack-name governance-iam-roles --capabilities CAPABILITY_NAMED_IAM`
2. **Deploy the encrypted, immutable S3 audit bucket** ; SSE-KMS, versioning, Object Lock GOVERNANCE mode, deny-HTTP bucket policy
3. **Enable AWS Config and deploy 10 managed rules** ; console-enabled recorder + `config-rules.yaml` stack
4. **Enable GuardDuty and CloudTrail** ; 30-day GuardDuty trial + multi-region CloudTrail with log file validation
5. **Create the KMS key with role separation** ; admin role manages the key, a separate role is granted decrypt-only
6. **Write the MAS TRM / 13-framework control-mapping document** ; the artefact that turns this from a lab exercise into a governance deliverable
7. **Push via a feature branch + PR, gated by CI** ; `cfn-lint` runs automatically before merge (see `.github/workflows/validate-p1.yml`)

---

## Key Decisions to Document

These are the design decisions worth being able to explain out loud ; each one reflects a real trade-off, not an arbitrary choice:

- **Permission boundaries vs. tighter IAM policies** ; a boundary is a ceiling, not a grant. It protects against privilege creep introduced by *other* administrators after the fact, which a well-written policy alone cannot.
- **Object Lock GOVERNANCE mode vs. COMPLIANCE mode** ; GOVERNANCE allows a narrowly-scoped, audited override; COMPLIANCE is immutable even to root. Chose GOVERNANCE for a reversible portfolio deployment; flagged COMPLIANCE as the stronger choice for a production regulatory log store.
- **SSE-KMS vs. SSE-S3 encryption** ; SSE-KMS was chosen specifically because it enables the role-separation control (a key policy with distinct admin/user principals). SSE-S3 offers encryption at rest but no equivalent access-segregation lever.
- **Console-enabled Config recorder vs. CloudFormation-managed recorder** ; the guide explicitly avoids deploying a Config recorder via CloudFormation in a personal account, since it can conflict with a console-enabled recorder. Documenting *why* a more "IaC-pure" approach was deliberately not used is itself a governance decision worth recording.
- **Single-account Free Tier scope vs. multi-account production reality** ; this project intentionally stays inside one account; the enterprise-scale path (StackSets, Control Tower, Config conformance packs) is documented rather than built, since building it would require an AWS Organization outside Free Tier scope.

---

## Security Considerations

- **Root account is never used for daily work** ; MFA-enforced, credentials rotated out, iamadmin IAM user used instead.
- **Least privilege is enforced structurally, not just by policy wording** ; permission boundaries mean a mis-scoped attached policy still can't exceed the ceiling.
- **Blast radius reduction** ; the Auditor and SecurityOps roles are scoped to read-only / detection-response actions respectively; neither can modify the audit trail.
- **Immutability of evidence** ; Object Lock + CloudTrail log file validation together mean tampering is both prevented (Lock) and detectable (validation hash chain) if attempted.
- **Explicit scope boundary ; what this project does *not* cover:** this is an **account-level governance baseline**, not workload/network security. There is no VPC, no application layer, and no runtime workload protection in this project ; that's a deliberate scope decision, not an oversight. Network and workload controls would sit in a separate project layered on top of this baseline.

---

## Controls Mapping

*Mapped against 13 frameworks and standards. Where a framework has no genuine bearing on this project, that's stated explicitly rather than forced.*

| Framework / Standard | Reference | Portfolio control / evidence |
|---|---|---|
| MAS TRM 2021 | 9.1.1 MFA, 8.3 encryption, 11.2 logging | Config root-MFA rule, KMS SSE, CloudTrail |
| ISO 27001 | Annex A 5.15, 8.24, 8.15 | IAM roles, KMS, CloudTrail |
| PDPA | Protection Obligation (security arrangements) | SSE-KMS + Block Public Access on audit bucket |
| NIST SP 800-53 Rev.5 | AC-2, AU-9, SC-12/SC-13, CM-6 | IAM account mgmt, S3 Object Lock, KMS, Config |
| NIST CSF 2.0 | PROTECT: PR.AA, PR.DS | IAM permission boundaries + KMS encryption |
| GDPR | Art.32 security of processing, Art.5(1)(f) | Encryption at rest/in transit, access controls |
| ISO 27002 | 5.15 Access control, 8.24 Cryptography, 8.15 Logging | IAM, KMS, CloudTrail |
| ISO 27017 | CLD.9.5.1 segregation, CLD.12.4.5 monitoring | KMS role separation, Config compliance monitoring |
| ISO 27018 | 11.1 PII retention/disposal in logs | S3 Object Lock retention policy, lifecycle rules |

---

## Threats Mitigated

*Tagged against MITRE ATT&CK (Enterprise/Cloud matrix).*

| Threat scenario | ATT&CK reference | Mitigating control |
|---|---|---|
| Compromised or misused root credentials | TA0004 / T1078.004 Valid Accounts: Cloud Accounts | MFA enforcement + Config root-MFA rule + login alarm |
| Accidental or malicious S3 public exposure | TA0009 / T1530 Data from Cloud Storage | S3 Block Public Access + Config public-access rule + deny-HTTP policy |
| Audit log tampering or deletion to cover tracks | TA0005 / T1562.008 Impair Defenses: Disable Cloud Logs | CloudTrail log file validation + S3 Object Lock (GOVERNANCE, 7yr) |
| Encryption key misuse or unauthorised decrypt | TA0006 Credential Access (key misuse) | KMS role separation (admin ≠ user) + annual rotation |
| Configuration drift introducing unnoticed gaps | TA0005 Defense Evasion (config weakening) | AWS Config continuous evaluation, 10 managed rules |
| Undetected anomalous account activity | General reconnaissance / lateral movement | Amazon GuardDuty behavioural detection |

---

## Threat Model Alignment ; OWASP & CSA

This baseline remediates risks highlighted in **OWASP Top 10:2025** and **CSA Top Threats to Cloud Computing 2025**, bridging compliance frameworks with industry threat models.

| Threat Model | Category | AWS Control Implemented | How It Mitigates |
|--------------|----------|-------------------------|------------------|
| **OWASP A01: Broken Access Control** | Unauthorized privilege escalation | IAM permission boundaries + MFA enforcement + Config rules (root MFA, unused access keys, no inline policies) | Enforces least‑privilege, caps maximum permissions, and ensures privileged accounts are protected. |
| **OWASP A02: Security Misconfiguration** | Misconfigured cloud resources expose data | S3 Block Public Access + HTTPS‑only bucket policy + Config rules (S3 encryption, CloudTrail enabled, KMS rotation) | Eliminates accidental public exposure, enforces encryption, and ensures audit logging is always enabled. |
| **CSA Identity & Access Risks** | IAM weaknesses and credential misuse | IAM permission boundaries + enforced MFA + KMS role separation | Strong identity governance reduces risk of compromised credentials and unauthorized decrypt operations. |
| **CSA Misconfigurations & Human Error** | Cloud misconfigurations exploited in breaches | AWS Config continuous evaluation + CloudFormation baseline | Detects drift, enforces secure defaults, and prevents recurring misconfiguration patterns. |
| **CSA Logging & Monitoring Gaps** | Delayed detection of malicious activity | CloudTrail log validation + GuardDuty behavioural detection | Provides immutable audit logs and anomaly detection for faster incident response. |
| **CSA Supply Chain & Shared Responsibility** | Weaknesses in shared cloud governance | CloudFormation StackSets + Control Tower guardrails | Enforces non‑negotiable baselines across accounts, reducing long‑term risk from inconsistent governance. |

---

### Key Takeaway
By aligning with **OWASP Top 10:2025** and **CSA Top Threats 2025**, this project demonstrates that governance controls are not just compliance artifacts ; they actively **remediate real attack scenarios**.  
This makes the portfolio globally credible and recruiter‑ready.

---

## Trade-off Pointers

Honest architectural trade-offs ; the kind of thing that separates "I deployed some services" from "I understand the design space":

- **Broad managed policies + a permission boundary vs. hand-written granular policies** ; using AWS managed policies (`SecurityAudit`, `ReadOnlyAccess`) inside a boundary trades some precision for maintainability; the boundary is what keeps the trade-off safe.
- **GOVERNANCE vs. COMPLIANCE Object Lock** ; reversibility vs. absolute immutability; the right answer depends on whether you can tolerate zero operator error against the value of being able to correct a mistake.
- **Single AWS account (Free Tier) vs. AWS Organization / multi-account** ; this project simplifies to fit Free Tier constraints; production scale requires StackSets, Control Tower guardrails, and Config conformance packs, all documented but not built here.
- **cfn-lint static validation only, no deploy-from-CI** ; CI never holds AWS credentials. This trades "fully automated deployment" for "zero long-lived cloud credentials in a public repository's CI pipeline" ; a deliberate security-over-convenience choice.

---

## Deliverables Checklist

- [ ] `iam-roles.yaml` deployed ; stack status `CREATE_COMPLETE`
- [ ] `s3-audit-bucket.yaml` deployed ; SSE-KMS, versioning, Object Lock all confirmed in console
- [ ] AWS Config enabled + 10 managed rules deployed and evaluated
- [ ] CloudTrail multi-region trail created, log file validation confirmed
- [ ] GuardDuty enabled, sample findings generated and reviewed, then suspended to avoid post-trial cost
- [ ] KMS key created with documented role separation (admin ≠ decrypt user)
- [ ] 7 screenshots captured, account ID and ARNs redacted (see redaction checklist)
- [ ] `mas-trm-control-mapping.md` / 13-framework mapping doc written
- [ ] `.github/workflows/validate-p1.yml` created and passing (green check on PR)
- [ ] This README completed and committed
- [ ] Feature branch → PR → CI green → squash-merged into `main`

---

## Questions to Answer in Your Documentation

**Q: Why permission boundaries instead of just writing tighter IAM policies?**
A permission boundary is a ceiling, not a grant ; it caps the maximum permissions a role can ever have, even if someone later attaches a broader managed policy to it. That protects against privilege creep introduced by other administrators after the fact, which a well-written policy alone can't do.

**Q: Why GOVERNANCE mode Object Lock on the audit bucket, not COMPLIANCE mode?**
COMPLIANCE mode is immutable even to the root account for the retention period ; safer against tampering, riskier operationally. GOVERNANCE mode still blocks ordinary deletion but allows a narrowly-scoped, audited override. Chose the reversible option for a portfolio project; would weigh COMPLIANCE more seriously for a production regulatory log store.

**Q: How would you scale this baseline to 50 AWS accounts?**
Deploy the same CloudFormation templates as StackSets from the management account, enforce them as non-negotiable guardrails through AWS Control Tower, and use Config conformance packs to generate consolidated compliance evidence across every account for audit submissions.

**Q: What's the weakest control in this baseline, and how would you strengthen it?**
AWS Config evaluates on a schedule, not in real time, so there's a detection lag between a misconfiguration occurring and Config flagging it. Paired with EventBridge event-pattern rules in Project 2 for near-real-time detection of the highest-risk changes.

**Q: How does this map to ISO 27001 certification evidence in practice?**
Each Annex A control needs an auditable evidence trail, not just a policy statement. CloudTrail and Config both generate that evidence continuously and automatically ; A.8.15 (logging) is evidenced by the CloudTrail configuration and log file validation status, not a manual log-review spreadsheet.

---

## Reference Materials

**AWS documentation**
- [IAM Permission Boundaries](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_boundaries.html)
- [Amazon S3 Object Lock](https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lock-managing.html)
- [AWS Config ; What Is AWS Config](https://docs.aws.amazon.com/config/latest/developerguide/WhatIsConfig.html)
- [AWS CloudTrail User Guide](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-user-guide.html)
- [Amazon GuardDuty User Guide](https://docs.aws.amazon.com/guardduty/latest/ug/what-is-guardduty.html)
- [AWS KMS Developer Guide](https://docs.aws.amazon.com/kms/latest/developerguide/overview.html)
- [AWS CloudFormation User Guide](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/Welcome.html)

**Regulatory & framework sources**
- [MAS Technology Risk Management Guidelines (2021)](https://www.mas.gov.sg/regulation/guidelines/technology-risk-management-guidelines)
- [PDPA Overview ; PDPC Singapore](https://www.pdpc.gov.sg/overview-of-pdpa/the-legislation/personal-data-protection-act)
- [NIST SP 800-53 Rev. 5](https://csrc.nist.gov/pubs/sp/800/53/r5/upd1/final)
- [NIST Cybersecurity Framework 2.0](https://www.nist.gov/cyberframework)
- [ISO/IEC 27001](https://www.iso.org/standard/27001)
- [MITRE ATT&CK ; Cloud Matrix](https://attack.mitre.org/matrices/enterprise/cloud/)
- [OWASP Top 10:2025](https://owasp.org/Top10/2025/)
- [CSA Top Threats to Cloud Computing - Deep Dive 2025](https://cloudsecurityalliance.org/research/working-groups/top-threats)
  
---

**Author:** Arunkumar Devaraj ; Cloud Security Architect | IAM & Governance | CCSP, 12 years enterprise security (IBM Guardium DAM, Tripwire FIM, enterprise IAM governance) transitioning into Cloud Security Architect / Cloud Governance Lead roles.
