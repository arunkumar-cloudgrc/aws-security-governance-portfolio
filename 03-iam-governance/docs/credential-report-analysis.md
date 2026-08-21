# Credential Report Analysis

## Executive Summary
This analysis replicates the discipline of a formal quarterly access‑certification review, applied to a cloud‑native IAM control plane instead of an on‑prem enterprise platform. The objective is to evaluate IAM credential hygiene, identify high‑risk identities and credentials, and produce prioritized, auditable remediation actions that align with enterprise controls and compliance frameworks.

---

## Methodology
- **Source:** Analysis derived from the provided `AWS_Credential_Report` exported from AWS IAM.  
- **Scope:** All IAM users, console passwords, MFA status, and access keys reported in the credential export.  
- **Assessment criteria:**  
  - **MFA enforcement:** Console users without MFA are non‑compliant.  
  - **Access key age:** Keys older than **90 days** are considered stale and high risk.  
  - **Dormant accounts:** No console login or programmatic activity **>90 days** flagged for remediation.  
  - **Password hygiene:** Console passwords never used or older than policy thresholds flagged for review.  
- **Risk rating approach:**  
  - **High:** Credentials enabling broad access or external exposure (no MFA, long‑lived keys, dormant privileged users).  
  - **Medium:** Scoped credentials that are stale or misconfigured.  
  - **Low:** Minor hygiene issues with limited impact.  
- **Deliverable:** Findings table (User, Issue, Risk Rating, Recommendation, Controls Mapped) and prioritized remediation actions.

---

## Findings Table

| **User** | **Issue** | **Risk Rating** | **Recommendation** | **Controls Mapped** |
|---|---|---:|---|---|
| `alice.admin` | No MFA enabled for console access | **High** | Enforce MFA via SCP or IAM password policy; require hardware/TOTP MFA | CIS AWS Foundations 1.13; NIST PR.AC-1; ISO 27001 A.9.4 |
| `bob.dev` | Access key active **150 days** | **High** | Rotate or deactivate access keys >90 days; adopt short‑lived STS credentials | CIS AWS Foundations 1.7; NIST PR.AC-4; MAS TRM 5.2 |
| `charlie.ops` | No console login **>180 days** (dormant) | **High** | Disable or delete dormant user; review for orphaned privileges | CIS AWS Foundations 1.1; NIST ID.AM-1; ISO 27001 A.9.2 |
| `diana.readonly` | Password enabled but never used | **Low** | Remove console password; require role assumption for console tasks | CIS AWS Foundations 1.13; NIST PR.AC-1 |
| `eve.svc` | Programmatic key rotated **95 days** ago (no automation) | **Medium** | Implement automated key rotation and alerting for >90 days | CIS AWS Foundations 1.7; NIST PR.IP-4 |
| `frank.support` | MFA enabled but using SMS only | **Medium** | Enforce stronger MFA (TOTP or hardware) via policy/SCP | NIST PR.AC-2; ISO 27001 A.9.4 |

---

## Remediation Actions (Prioritized)

### Immediate (0–7 days)
- **Enforce MFA:** Apply an organization‑level Service Control Policy (SCP) or account‑level IAM password policy that **requires MFA** for console access. Block console access for users without MFA until they enroll.  
- **Disable dormant users:** Identify users with no console or API activity **>90 days**; disable console passwords and deactivate access keys pending owner review. Document decisions in the access‑certification log.

### Short term (7–30 days)
- **Rotate or deactivate stale keys:** Rotate or deactivate access keys older than **90 days**. Replace long‑lived keys with short‑lived STS credentials or IAM roles where possible.  
- **Remove unused console passwords:** For programmatic‑only users, remove console passwords and require role assumption for console tasks.

### Medium term (30–90 days)
- **Automate credential hygiene:** Deploy automated checks (Lambda, AWS Config Rules, CloudWatch Events) to:  
  - Alert on access keys >60 days.  
  - Auto‑disable keys at 90 days with staged notifications.  
  - Flag users with no activity at 60/90/180 day thresholds.  
- **Strengthen MFA controls:** Enforce TOTP or hardware MFA; disallow SMS MFA where feasible.

### Ongoing (quarterly)
- **Quarterly access‑certification cycle:** Run the credential report each quarter, produce an access‑certification package (findings, owner attestations, remediation evidence), and retain artifacts for audit.  
- **Policy and SCP reviews:** Validate SCPs and permission boundaries quarterly to ensure continued enforcement of least‑privilege.

---

## Compliance Mapping (Remediation → Controls)

- **Enforce MFA via SCP / IAM password policy**  
  - **CIS AWS Foundations:** 1.13 (Enable MFA on root and IAM users)  
  - **NIST CSF:** PR.AC-1, PR.AC-2 (Identity management and authentication)  
  - **ISO 27001:** A.9.4 (Authentication management)  
  - **MAS TRM:** Identity and access management controls

- **Rotate or deactivate access keys >90 days**  
  - **CIS AWS Foundations:** 1.7 (Rotate access keys)  
  - **NIST CSF:** PR.IP-4 (Protecting credentials)  
  - **ISO 27001:** A.9.2 (User access management)  
  - **MAS TRM:** Credential lifecycle and key management

- **Disable or delete dormant users (>90 days no login)**  
  - **CIS AWS Foundations:** 1.1 (Inventory of accounts)  
  - **NIST CSF:** ID.AM-1; PR.AC-4 (Asset inventory and access permissions)  
  - **ISO 27001:** A.9.2 (User registration and de‑registration)  
  - **MAS TRM:** Account lifecycle and orphaned account controls

---

## Evidence and Audit Trail Recommendations
- **Record actions:** Capture evidence for each remediation (CloudTrail logs, IAM change history, exported reports, screenshots) and attach to the quarterly certification package.  
- **Owner attestations:** Assign remediation tickets to resource owners and require attestation (approve/deny) before final deletion.  
- **Retention:** Retain credential reports and remediation evidence for at least one audit cycle (recommended 12 months).

---

## Operationalizing the Remediation (Implementation Notes)
- **SCP example (MFA enforcement):** Create an SCP that denies sensitive actions unless `aws:MultiFactorAuthPresent` is true; test in a staging OU before enforcing in production.  
- **Access key automation:** Implement a Lambda triggered by CloudWatch Events that identifies keys >60 days, notifies owners, and rotates/disables at 90 days. Use AWS Secrets Manager or short‑lived STS tokens for service accounts.  
- **Dormant user workflow:** Tag dormant users, create an identity governance ticket, require owner review within 14 days, then disable and schedule deletion after 30 days if no business justification is provided.

---

## Conclusion
Applying a formal quarterly access‑certification discipline to the cloud IAM control plane converts credential hygiene into measurable governance outcomes. The prioritized remediation actions reduce attack surface, improve audit readiness, and align cloud identity controls with enterprise frameworks (CIS, NIST, ISO, MAS TRM). Implementing automation, owner attestations, and evidence retention will ensure sustained improvements and demonstrable compliance.

---

## Appendix: Quick Remediation Checklist
- **Enforce MFA via SCP/IAM policy** — *Immediate*.  
- **Rotate/deactivate access keys >90 days** — *Immediate → Short term*.  
- **Disable/delete dormant users >90 days** — *Immediate*.  
- **Automate checks and alerts for credential hygiene** — *Short → Medium term*.  
- **Run and archive quarterly credential reports with owner attestations** — *Ongoing*.