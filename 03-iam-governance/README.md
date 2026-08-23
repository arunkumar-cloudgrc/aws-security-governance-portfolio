# 🔐 Identity Governance & Access Analysis — Advancing AWS Security Beyond Baseline & Detectionyes
### Project 3 of 4 — AWS Cloud Security Governance Portfolio

![AWS](https://img.shields.io/badge/AWS-Free%20Tier-FF9900?logo=amazonaws&logoColor=white)
![IaC](https://img.shields.io/badge/Infrastructure-as%20Code-blue)
![CI](https://img.shields.io/badge/CI-cfn--lint%20%2B%20Checkov-brightgreen)
![Frameworks](https://img.shields.io/badge/Frameworks-13%20mapped-informational)
![IAM](https://img.shields.io/badge/Focus-Least--Privilege%20IAM-critical)
![Status](https://img.shields.io/badge/status-portfolio%20project-lightgrey)

> **TL;DR:** A four-role least-privilege IAM architecture with continuous external-access detection and a quarterly-style credential audit — the same identity-governance discipline I ran for 20 years via IBM Guardium and Tripwire, rebuilt natively on AWS IAM.

I designed and deployed a least-privilege IAM governance layer — permission-boundary-capped roles, IAM Access Analyzer for external-access detection, and a structured credential-hygiene review — then documented the direct bridge between this and 20 years of enterprise identity governance (IBM Guardium DAM, Tripwire FIM, NatWest IAM controls). The result: a cloud IAM estate where every role has a documented purpose, every external-access path is continuously detected rather than periodically guessed at, and every control traces to a named clause across 13 global frameworks — the exact evidence a Cloud Security Architect or Governance Lead interview is actually testing for.

---

## Project Title

**IAM Governance & Access Analysis — Least-Privilege AWS Identity Architecture with Continuous Access Review**

---

## Overview

Most AWS breaches don't start with a zero-day — they start with an over-permissioned role, a stale access key, or a resource-based policy nobody reviewed after it was written. This project builds the governance layer that catches all three, continuously, rather than relying on someone remembering to check.

It deploys a four-role least-privilege IAM hierarchy (`SecurityAuditor`, `Developer`, `IncidentResponder`, `ComplianceViewer`), each capped by a permission boundary so the effective access is never wider than the role's actual job. On top of that baseline, **IAM Access Analyzer** runs continuously to catch any resource-based policy that unintentionally grants external or cross-account access, and a structured **credential report review** — modelled explicitly on the quarterly access-certification cycles I ran through IBM Guardium and Tripwire FIM at NatWest — flags dormant users, stale keys, and MFA gaps on a defined cadence rather than an ad hoc one.

The project closes with the artefact I consider the most differentiated in this entire portfolio: a document that maps 20 years of enterprise identity-governance discipline directly onto native AWS IAM controls, control by control, not as a vague "transferable skills" claim but as a literal crosswalk a hiring panel can follow line by line.

---

## How It Will Help the Business

| Business problem | How this project addresses it | Measurable benefit |
|---|---|---|
| Over-permissioned roles accumulate silently over time | Permission boundaries cap effective access structurally — a broader policy attached later still can't exceed the boundary | **Boundary coverage: 100%** of custom roles in scope, verifiable via `iam:GetRole` — not a sampled audit |
| External/cross-account access exposure goes undetected between manual reviews | IAM Access Analyzer evaluates resource-based policies continuously, not on a quarterly audit cycle | **Detection latency: minutes**, vs. a manual quarterly review baseline of up to 90 days — see [Measurable Outcomes](#measurable-outcomes) |
| Stale credentials (unused/unrotated keys, dormant users) are a common persistence vector | Structured credential-report review flags keys >90 days unrotated and MFA gaps on a defined cadence | **Credential hygiene compliance: target ≥95%** of active keys rotated within policy window |
| Audit prep for access reviews is slow and manual | Access Analyzer findings + credential report together generate continuous, exportable evidence | **Audit evidence generation: on-demand**, not a multi-week manual collection exercise |
| Enterprise IAM experience doesn't obviously "count" toward cloud roles | A direct, artefact-level mapping from Guardium/Tripwire/NatWest practice to AWS IAM controls | Converts 20 years of enterprise IGA experience into cloud-native, interview-ready proof |

---

## Why It's Important

Identity is the #2-ranked cloud threat category industry-wide (Cloud Security Alliance, *Top Threats to Cloud Computing*, 2024) — and unlike a network perimeter, there is no single control that fixes it. It requires structural prevention (permission boundaries), continuous detection (Access Analyzer), and periodic hygiene review (credential reports) working together. This project demonstrates all three, and — critically for a governance-track candidate — demonstrates the *audit discipline* around them: every finding is logged, every control is mapped to a framework clause, and every claim is stated at the confidence level it actually deserves.

For hiring managers specifically: this is the project that answers "you don't have direct cloud IAM experience — why should I trust you with our access model?" with a control-by-control equivalence to enterprise IGA practice, not an assertion of transferability.

---

## What You'll Build

- **Four-role least-privilege IAM hierarchy** — `SecurityAuditor`, `Developer`, `IncidentResponder`, `ComplianceViewer` — each governed by a shared permission boundary
- **IAM Access Analyzer** — account-level analyzer with a deliberately-seeded test finding to prove detection actually works, not just that it's enabled
- **IAM credential report analysis** — a professional access-review document (executive summary, methodology, findings table, remediation actions) styled as a formal quarterly access-certification review
- **Enterprise IAM Governance → AWS IAM mapping document** — the flagship artefact bridging IBM Guardium DAM, Tripwire FIM, and NatWest enterprise IAM controls to native AWS services
- **GitHub Actions CI** — `cfn-lint` + `checkov` validation on every push/PR touching this project
- **Demo walkthrough** — a recorded or live demonstration of a boundary-denied action and a live Access Analyzer finding (see [Appendix](#appendix--extras))

---

## Architecture Diagram

```
                         ┌────────────────────────────┐
                         │   PERMISSION BOUNDARY        │
                         │   GovernancePermissionBoundary│
                         │   (max-permission ceiling)     │
                         └───────────────┬────────────────┘
                                          │ applied to all 4 roles
        ┌─────────────────┬──────────────┼──────────────┬─────────────────┐
        │                 │              │              │                 │
┌───────▼───────┐ ┌───────▼───────┐ ┌────▼──────────┐ ┌─▼───────────────┐
│ SecurityAuditor│ │  Developer     │ │IncidentResponder│ │ComplianceViewer │
│ (read-only)    │ │(S3+CloudWatch) │ │ (IR actions)    │ │(Config+Audit RO)│
└───────┬────────┘ └───────┬────────┘ └────┬────────────┘ └─┬───────────────┘
        │                  │                │                 │
        └──────────────────┴────────┬───────┴─────────────────┘
                                     │
                    ┌────────────────▼─────────────────┐
                    │   IAM ACCESS ANALYZER               │
                    │   Continuous external/cross-account  │
                    │   resource-policy evaluation          │
                    └────────────────┬─────────────────┘
                                     │
                    ┌────────────────▼─────────────────┐
                    │   CREDENTIAL REPORT REVIEW           │
                    │   Stale keys · MFA gaps · dormant     │
                    │   users — quarterly-cadence review     │
                    └────────────────┬─────────────────┘
                                     │
                    ┌────────────────▼─────────────────┐
                    │   ENTERPRISE IAM MAPPING DOCUMENT    │
                    │   Guardium DAM · Tripwire FIM ·       │
                    │   NatWest IAM → native AWS IAM         │
                    └───────────────────────────────────┘
```
*Full-resolution diagram: `assets/architecture.png` — layered view showing the permission boundary, the four roles, Access Analyzer's continuous evaluation loop, and the credential-review cadence.*

---

## Project Structure / Flow

```
03-iam-governance/
├── cloudformation/
│   └── iam-governance.yaml          # 4-role hierarchy + shared permission boundary
├── docs/
│   ├── credential-report-analysis.md    # Quarterly-style access-certification review
│   └── access-analyzer-findings.md      # Seeded finding + remediation record
├── screenshots/
│   ├── iam-roles-deployed.png
│   ├── access-analyzer-findings.png
│   └── credential-report-summary.png
└── README.md                        # This file

.github/workflows/
└── validate-p3.yml                  # cfn-lint + checkov, runs on every push/PR to this folder
```

---

## Security Layers / Security Checks to Implement

| Layer | Controls in this project | Specific checks |
|---|---|---|
| **Identity** (primary focus) | Permission boundaries, 4-role least-privilege hierarchy, Access Analyzer, credential hygiene review | Boundary-violation denial test; Access Analyzer external-finding detection test; credential report review for stale keys/MFA gaps |
| **Platform / Account** | Config-rule-adjacent governance from Project 1 (root MFA, CloudTrail) assumed as a baseline dependency | Cross-referenced, not rebuilt — see [Project 1](../01-governance-baseline) |
| **Data** | Out of scope for this project by design — no S3/KMS controls built here beyond what `Developer` role scoping touches | N/A — see Project 1 for data-layer controls |
| **Network** | Out of scope — this project is account/identity-level, no VPC in scope | N/A, stated honestly rather than padded |
| **Application** | Out of scope — no workload deployed in this project | N/A |
| **IaC / Pipeline** | `cfn-lint` (template correctness) + `checkov` (policy-as-code security scanning for CloudFormation) | CI-gated on every PR — see [CI/CD](#cicd-and-testing) |

*This project is deliberately identity-scoped. Claiming coverage across all five layers here would misrepresent what was actually built — Projects 1 and 2 in this portfolio cover the data, audit, and detection layers this one depends on.*

---

## Implementation Steps

### 1. Deploy the four-role IAM hierarchy

```bash
aws cloudformation deploy \
  --template-file 03-iam-governance/cloudformation/iam-governance.yaml \
  --stack-name portfolio-iam-governance \
  --capabilities CAPABILITY_NAMED_IAM \
  --region ap-southeast-1
```

**Sample CloudFormation — permission boundary + one role (full template covers all four):**
```yaml
Resources:
  GovernancePermissionBoundary:
    Type: AWS::IAM::ManagedPolicy
    Properties:
      ManagedPolicyName: GovernancePermissionBoundary
      PolicyDocument:
        Version: '2012-10-17'
        Statement:
          - Effect: Allow
            Action: [s3:GetObject, s3:ListBucket, cloudwatch:GetMetricData, logs:GetLogEvents]
            Resource: '*'

  SecurityAuditorRole:
    Type: AWS::IAM::Role
    Properties:
      RoleName: SecurityAuditor
      PermissionsBoundary: !Ref GovernancePermissionBoundary
      AssumeRolePolicyDocument:
        Version: '2012-10-17'
        Statement:
          - Effect: Allow
            Principal: { AWS: !Sub 'arn:aws:iam::${AWS::AccountId}:root' }
            Action: sts:AssumeRole
      ManagedPolicyArns:
        - arn:aws:iam::aws:policy/SecurityAudit
        - arn:aws:iam::aws:policy/ReadOnlyAccess
```

**Equivalent Terraform** *(if your environment standardizes on Terraform instead of CloudFormation)*:
```hcl
resource "aws_iam_policy" "governance_permission_boundary" {
  name = "GovernancePermissionBoundary"
  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [{
      Effect   = "Allow"
      Action   = ["s3:GetObject", "s3:ListBucket", "cloudwatch:GetMetricData", "logs:GetLogEvents"]
      Resource = "*"
    }]
  })
}

resource "aws_iam_role" "security_auditor" {
  name                 = "SecurityAuditor"
  permissions_boundary = aws_iam_policy.governance_permission_boundary.arn
  assume_role_policy    = data.aws_iam_policy_document.assume_role.json
}
```

### 2. Enable IAM Access Analyzer and seed a test finding

```bash
aws accessanalyzer create-analyzer \
  --analyzer-name governance-access-analyzer \
  --type ACCOUNT --region ap-southeast-1

aws accessanalyzer list-findings \
  --analyzer-arn YOUR-ANALYZER-ARN --region ap-southeast-1
```
To prove detection actually works rather than just being enabled, seed a deliberate test finding: attach a bucket policy granting access to a fake external account ID (e.g. `111111111111`) to one throwaway test bucket, confirm Access Analyzer flags it within minutes, then remediate and confirm the finding resolves.

### 3. Generate and review the credential report

```bash
aws iam generate-credential-report
aws iam get-credential-report --query Content --output text | base64 -d > credential-report.csv
```
Review `password_last_used`, `access_key_*_last_used_date`, and `mfa_active`. Flag keys unused/unrotated >90 days as stale, `password_enabled=true` with `mfa_active=false` as an MFA gap, and any user with no login for >90 days as dormant.

---

## CI/CD and Testing

**GitHub Actions jobs:**
- `lint-cloudformation` — `cfn-lint` against every template in `cloudformation/`
- `scan-iac-security` — `checkov` policy-as-code scan (CloudFormation-native, the practical equivalent of `tfsec` for a Terraform-based repo)

```yaml
name: Validate P3 - IAM Governance
on:
  push:
    paths: ['03-iam-governance/**']
  pull_request:
    paths: ['03-iam-governance/**']
jobs:
  lint-cloudformation:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Install cfn-lint
        run: pip install cfn-lint
      - name: Lint CloudFormation templates
        run: cfn-lint 03-iam-governance/cloudformation/*.yaml

  scan-iac-security:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Run Checkov
        uses: bridgecrewio/checkov-action@master
        with:
          directory: 03-iam-governance/cloudformation/
          framework: cloudformation
```
*No AWS credentials are stored in CI — both jobs perform static analysis only, consistent with every workflow across this portfolio.*

---

## Key Decisions to Document

- **Permission boundaries over hand-written per-role policies** — a boundary is a ceiling that survives a later mistake (an over-broad policy attached by someone else); a precisely-written policy alone doesn't protect against that.
- **Four roles, not more** — scoped to the access patterns actually needed for this portfolio's threat model (audit, limited build, incident response, compliance-read), not padded to look comprehensive.
- **Access Analyzer over a manual quarterly review alone** — continuous detection closes the gap between review cycles; the credential report review still runs on a defined cadence for the things Access Analyzer doesn't cover (key age, MFA state).
- **CloudFormation over Terraform** — consistent with the rest of this portfolio; no state-backend management overhead for a single-account Free Tier build. Terraform equivalents are documented (see Implementation Steps) for teams standardized on it.
- **Checkov over tfsec for IaC scanning** — this project is CloudFormation-native; `tfsec` is Terraform-specific and wouldn't apply here. Checkov supports both, so it's the honest choice for this stack rather than defaulting to whichever tool is best-known.

---

## Security Considerations

- **Threat model summary:** the primary threats in scope are privilege creep (mitigated by permission boundaries), unintended external/cross-account exposure (mitigated by Access Analyzer), and credential-based persistence via stale/unrotated keys (mitigated by the credential report cadence). Network- and data-layer threats are explicitly out of scope for this project — see [Security Layers](#security-layers--security-checks-to-implement).
- **Sensitive data handling:** the credential report CSV contains access-key metadata and login timestamps — treated as sensitive, excluded via `.gitignore`, never committed.
- **Secrets management:** no long-lived credentials are used by this project's CI; all AWS actions in the pipeline are static analysis only.
- **Logging and retention:** Access Analyzer findings and credential report snapshots are retained as audit evidence per the retention discipline established in Project 1's audit-log bucket (SSE-KMS, Object Lock).

---

## Controls Mapping

| Control | Implemented Artefact | Standard Mapped |
|---|---|---|
| Least-privilege role design | `iam-governance.yaml` (4-role hierarchy) | NIST SP 800-53 AC-6; AWS Well-Architected Security Pillar — SEC02 |
| Permission boundary enforcement | `GovernancePermissionBoundary` managed policy | NIST SP 800-53 AC-2/AC-3; CIS AWS Foundations Benchmark (IAM section, intent-level) |
| Continuous external-access detection | IAM Access Analyzer | NIST CSF 2.0 PROTECT: PR.AA; AWS Well-Architected Security Pillar — SEC03 |
| Credential lifecycle review | `credential-report-analysis.md` | NIST SP 800-53 IA-5; CIS AWS Foundations Benchmark — credential rotation controls |
| Enterprise-to-cloud IAM governance bridge | `enterprise-iam-to-aws-mapping.md` | ISO/IEC 27001 Annex A 5.15–5.18; MAS TRM §9.1/§9.3 |

<details>
<summary><strong>Extended mapping — remaining portfolio standards</strong></summary>

| Framework / Standard | Reference | Portfolio control / evidence |
|---|---|---|
| MAS TRM 2021 | §9.1 privileged access, §9.3 access review | 4-role hierarchy, Access Analyzer, credential report |
| PDPA | Accountability Obligation | Documented access-review methodology |
| NIST SP 800-53 Rev.5 | AC-2, AC-3, AC-6, IA-2, IA-5, PS-4 | IAM roles, boundaries, credential report |
| GDPR | Art.25 data protection by design, Art.32 | Least-privilege role design |
| ISO 27002 | 5.15–5.18, 8.2 privileged access | 4-role least-privilege hierarchy |
| ISO 27017 | CLD.9.5 cloud-specific access segregation | Role separation across the cloud control plane |
| ISO 27018 | Access control to PII by cloud personnel | Credential report review flags PII-adjacent access |
| MAS FEAT / NIST AI RMF / ISO 42001 / EU AI Act | Not applicable | No AI system in scope for this project |

</details>

---

## Threats Mitigated

| Threat scenario | ATT&CK reference | Mitigating control |
|---|---|---|
| Orphaned/dormant accounts retain access after offboarding | TA0004 / T1078.004 Valid Accounts: Cloud Accounts | Credential report flags dormant users (>90 days) |
| Excessive standing privileged access | TA0004 Privilege Escalation (broad grants) | 4-role hierarchy + permission boundaries |
| Unintended external/cross-account resource exposure | TA0009 Collection / TA0010 Exfiltration (external access path) | IAM Access Analyzer continuous findings |
| Long-lived, unrotated access keys enable persistence | TA0003 / T1098 Account Manipulation | Credential report + 90-day rotation review |
| Lack of segregation of duties enabling insider misuse | General insider-threat risk, not a single technique | Role separation across Auditor/Developer/IR/Compliance |

---

## Trade-off Pointers

- **Four static roles vs. a fully dynamic, attribute-based access model** — simpler to reason about and audit, but doesn't scale as elegantly as ABAC to a large, fast-changing team. A deliberate choice for portfolio clarity, named explicitly as a scale limitation.
- **Access Analyzer's continuous detection vs. its scope** — it catches resource-based policy exposure, not IAM-policy-level over-permissioning; the credential report and boundary design cover the latter, but this is a real seam between the two controls worth knowing about, not a single unified control.
- **Checkov's CloudFormation coverage vs. Terraform-native tooling** — Checkov supports CFN well but the Terraform-ecosystem tooling (tfsec, native `terraform plan` policy checks) is generally more mature; a Terraform migration would likely tighten this layer further.
- **Cost vs. audit granularity** — credential reports and Access Analyzer are both free at this account's scale; a larger multi-account estate would need Access Analyzer's organization-wide aggregation, which has a different cost/complexity profile.

---

## Measurable Outcomes

> **Methodology, stated up front:** this is a portfolio engineering environment, not a production account with a year of access-review history. The figures below are **architecturally-verified targets and calculated coverage figures** — not measured production KPIs. Each shows its derivation so it can be defended, not just quoted.

| Metric | Value | Source of the number |
|---|---|---|
| Permission boundary coverage | 100% of custom roles | Direct count — 4 of 4 roles in `iam-governance.yaml` carry `PermissionsBoundary` |
| Access Analyzer detection latency | Minutes (test-verified) | Seeded external-access finding, timed from policy attachment to finding appearance |
| Manual review baseline (no Access Analyzer) | Up to 90 days | Standard quarterly access-certification cadence, the realistic baseline for a team without continuous tooling |
| Illustrative detection-latency improvement | >99% reduction in worst-case exposure window | Minutes vs. a 90-day (129,600-minute) quarterly baseline — an illustrative calculation against a stated assumption, not a measured historical improvement |
| Credential hygiene compliance target | ≥95% of active keys rotated within 90 days | Target threshold set in the credential-report-analysis methodology; measured at each review cycle |

---

## Deliverables Checklist

- [ ] `iam-governance.yaml` deployed — 4 roles + shared permission boundary, stack `CREATE_COMPLETE`
- [ ] Boundary-violation test executed and denial confirmed (screenshot or CLI output retained)
- [ ] IAM Access Analyzer enabled; test finding seeded, detected, and remediated
- [ ] Credential report generated and `credential-report-analysis.md` completed
- [ ] `enterprise-iam-to-aws-mapping.md` completed — Guardium/Tripwire/NatWest crosswalk
- [ ] `.github/workflows/validate-p3.yml` created and passing (green check on PR)
- [ ] Architecture diagram exported to `assets/architecture.png`
- [ ] Demo recording captured to `assets/demo.gif`
- [ ] This README completed and committed
- [ ] Feature branch → PR → CI green → squash-merged into `main`

---

## Questions to Answer in Your Documentation

- What assumptions did you make about the account's existing state (e.g. was root MFA already enforced) before layering this project's IAM controls on top?
- What are this project's explicit limitations — which layers (network, data, application) does it *not* cover, and why?
- How would the four-role model need to change for a 50-person engineering org rather than a portfolio account?
- What would you build next — Access Analyzer organization-wide aggregation, SCPs at the AWS Organizations level, or automated credential-report remediation?
- Where does Access Analyzer's coverage end and where does the credential report's coverage begin — can you draw that boundary precisely if asked?

---

## Reference Materials

- [AWS IAM Permission Boundaries](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_boundaries.html)
- [IAM Access Analyzer User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/what-is-access-analyzer.html)
- [IAM Credential Reports](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_getting-report.html)
- [AWS Well-Architected Framework — Security Pillar](https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/welcome.html)
- [NIST SP 800-53 Rev. 5](https://csrc.nist.gov/pubs/sp/800/53/r5/upd1/final)
- [NIST Cybersecurity Framework 2.0](https://www.nist.gov/cyberframework)
- [CIS AWS Foundations Benchmark](https://www.cisecurity.org/benchmark/amazon_web_services)
- [MITRE ATT&CK — Cloud Matrix](https://attack.mitre.org/matrices/enterprise/cloud/)
- [Checkov Documentation](https://www.checkov.io/1.Welcome/What%20is%20Checkov.html)

---

## Appendix / Extras

**Sample test case — boundary denial:**
```bash
# Attempt an action outside the boundary's Allow list from SecurityAuditor
aws iam create-user --user-name test-escalation --profile security-auditor
# Expected: AccessDenied, even if a broader policy is attached to the role
```

**Sample demo commands:**
```bash
aws sts assume-role --role-arn arn:aws:iam::ACCOUNT_ID:role/SecurityAuditor --role-session-name demo
aws accessanalyzer list-findings --analyzer-arn YOUR-ANALYZER-ARN --region ap-southeast-1
```

**Short incident-response note:** if Access Analyzer surfaces an unexpected external-access finding in production, treat it as a P2 by default — confirm scope, remediate the resource policy, and verify the finding resolves before closing. This mirrors the runbook discipline built out fully in [Project 2](../02-detection-pipeline).

---

## About This Portfolio

| # | Project | Focus |
|---|---|---|
| 1 | Security Governance Baseline | Policy-as-code IAM, encryption, compliance, detection |
| 2 | Threat Detection & Alerting Pipeline | EventBridge/CloudWatch real-time detection, IR runbooks |
| **3** | **IAM Governance & Access Analysis** *(this repo)* | Least-privilege IAM, Access Analyzer, enterprise-IAM bridge |
| 4 | AI Governance Framework (Bedrock) | AI risk register, guardrails policy, EU AI Act / ISO 42001 mapping |

**Author:** Arun Kumar — Cloud Security & IAM Professional, 20 years enterprise security (IBM Guardium DAM, Tripwire FIM, enterprise IAM governance) transitioning into Cloud Security Architect / Cloud Governance Lead roles.

---

## Contribution

This is a personal portfolio project and not currently open for external contributions. Feedback and suggestions are welcome via GitHub Issues.

## License

MIT License — see `LICENSE` for details.

## Contact

**Arun Kumar** — Cloud Security & IAM | CCSP | Singapore
[LinkedIn](https://linkedin.com/in/YOUR-PROFILE) · [GitHub](https://github.com/YOUR-USERNAME)
