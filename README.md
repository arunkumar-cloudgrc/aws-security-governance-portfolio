# 🛡️ AWS Cloud Security & AI Governance Portfolio

> **Four projects tracing one discipline across two control planes** — establish a baseline, monitor continuously, flag deviation, remediate on a cadence, and defend the evidence to an auditor. Projects 1–3 apply it to AWS infrastructure. Project 4 applies it to a high-risk AI system.

**Arunkumar Devaraj** — Security Specialist (Vice President), NatWest Group
20+ years enterprise IT · 12+ years in security within a global regulated bank · CCSP · CAIGS
[LinkedIn](https://linkedin.com/in/arunkumar-cloudgrc) · Targeting AI Governance, AI Risk, AI Assurance and Technology Risk roles

---

## ⚠️ Scope — stated once, per project

**Projects 1–3 are deployed AWS builds.** Infrastructure-as-code, real resources, real console evidence, built on AWS Free Tier.

**Project 4 is a governance design.** A worked enterprise example with 44 versioned artifacts — *designed and documented, not operated in production.* No production deployment, no operational telemetry, no measured business outcome.

That distinction is stated in each project's own README and maintained throughout. AI Governance defines what must be true before release and ensures those conditions remain governed throughout the lifecycle — drawing that line deliberately is the competence, not a concession.

---

## Portfolio at a Glance

| # | Project | What it demonstrates | Evidence type |
|---|---|---|---|
| **P1** | **[Security Governance Baseline](./01-governance-baseline)** | Policy-as-code control baseline with traceability across 13 frameworks | ✅ Deployed |
| **P2** | **[Threat Detection & Alerting Pipeline](./02-threat-detection-pipeline)** | Event-driven detection, alerting and documented incident response | ✅ Deployed |
| **P3** | **[IAM Governance & Access Analysis](./03-iam-governance)** | Least-privilege identity governance and access certification discipline | ✅ Deployed |
| **P4** | **[AI Governance Framework](./04-ai-governance-and-technical-lifecycle)** | Full AI governance lifecycle for a high-risk credit decisioning system | 📐 Designed |

---

## P1 · Security Governance Baseline

**📂 [Open project →](./01-governance-baseline)**

Infrastructure-as-code governance baseline establishing regulatory-requirement-to-evidence traceability.

- Least-privilege **IAM with permission boundaries** — a ceiling that survives a later over-broad policy attachment
- **Immutable audit logging** — S3 Object Lock, 7-year retention, versioning, TLS-only bucket policy
- **AWS Config** managed rules with continuous compliance evaluation
- **Multi-region CloudTrail** with log file validation for tamper detection
- **KMS with enforced role separation** — key administrator ≠ key user
- **GuardDuty** behavioural threat detection

**Frameworks mapped:** MAS TRM 2021 · ISO 27001 · ISO 27002/27017/27018 · NIST SP 800-53 Rev.5 · NIST CSF 2.0 · GDPR · PDPA
**Key artifact:** CloudFormation templates + 13-framework control mapping · CI validation via GitHub Actions (`cfn-lint`)

---

## P2 · Threat Detection & Alerting Pipeline

**📂 [Open project →](./02-threat-detection-pipeline)**

Event-driven detection layer over the P1 baseline, with the response process written rather than assumed.

- **EventBridge rules** — root login, IAM access key creation, S3 public-access change, high-severity GuardDuty findings
- **CloudWatch alarms** with SNS notification
- **Consolidated security dashboard** aggregating compliance and detection metrics
- **Incident response runbooks** tracking the MAS Notice on Technology Risk Management 1-hour incident-notification requirement alongside GDPR Article 33's 72-hour personal-data-breach notification requirement

**Frameworks mapped:** MAS TRM Ch.10 · NIST CSF 2.0 Detect/Respond · NIST SP 800-53 SI-4, IR-4, IR-6 · ISO 27001 A.5.24–5.28 · GDPR Art.33 · PDPA
**Design point:** detection speed supports regulatory incident response. Faster detection shortens the time needed to identify and assess potentially reportable incidents. The MAS Notice on Technology Risk Management includes a 1-hour notification requirement for relevant discovered system malfunctions; under GDPR Article 33, the 72-hour period applies once the controller is aware that a personal-data breach has occurred and notification is required.

---

## P3 · IAM Governance & Access Analysis

**📂 [Open project →](./03-iam-governance)**

Least-privilege identity architecture with the access-certification discipline applied to a cloud control plane.

- **Four-role least-privilege hierarchy** with permission boundaries — Security Auditor, Developer, Incident Responder, Compliance Viewer
- **IAM Access Analyzer** with external-access findings review and remediation
- **Credential report analysis** in formal access-review format — stale keys, MFA gaps, dormant accounts
- **Enterprise-IAM-to-AWS mapping document** bridging IBM Guardium DAM, Tripwire FIM and enterprise IAM governance to AWS-native controls

**Frameworks mapped:** MAS TRM Ch.9 · ISO 27001 A.5.15–5.18 · NIST SP 800-53 AC-2/AC-3/AC-6 · NIST CSF PR.AA · GDPR Art.25/32 · CIS
**Why it matters:** Access Analyzer is proactive and continuous; the Policy Simulator is a manual what-if. One finds problems you didn't know to look for; the other confirms a hypothesis you already have.

---

## P4 · AI Governance Framework — Credit Decisioning Assistant

**📂 [Open project →](./04-ai-governance-and-technical-lifecycle)**  ·  **[Browse the 44-artifact register →](./04-ai-governance-and-technical-lifecycle/artifacts)**

📐 **Governance design, not a production deployment.**

A complete AI governance lifecycle for a system classified **high-risk under EU AI Act Annex III(5)(b)** — creditworthiness assessment of natural persons.

### The architecture decision everything depends on

**Hybrid:** a deterministic gradient-boosted model produces the score and decision. A retrieval-grounded foundation model converts the SHAP drivers into a policy-cited rationale — and **never produces the score.** That separation preserves customer-actionable attribution on the scoring layer, supports the designed GDPR Article 22 governance position, and enables deterministic fallback.

### The signature governance decision

In the worked scenario, counterfactual fairness testing returns a **disparate impact ratio of 0.67** against a threshold of **0.80 declared before testing.** The governance design therefore records a Gate 2 block. The scenario traces the disparity to postal district in retrieval-corpus metadata rather than the scoring feature set, applies structural remediation and models a re-test at **0.89**, with release **11 scenario-days later**.

**The block record remains above the later release record** so the evidence chain demonstrates that the governance gate is capable of returning "no." A gate log containing only approvals cannot demonstrate that authority.

### By the numbers

| Metric | Value | Evidence level |
|---|---|---|
| Governance artifacts | **44** | Documented |
| Stage gates | **5** (D1–D7 mapping); worked scenario demonstrates one recorded block and no designed bypass | Documented design |
| Risks tracked / open | **20 / 12** open by design | Documented design |
| Failure modes analysed | **18** (FM-01 to FM-18) | Documented design |
| Threats modelled | **12** — OWASP LLM Top 10 (2025 mapping baseline) · MITRE ATLAS | Documented design |
| Controls with effectiveness-testing methods specified | **12 of 17 (71%)** | Design-time definition |
| Governance maturity | **2.6 / 5** across 15 dimensions | Self-assessment |
| Independent assurance | **1 / 5** — no independent audit evidence exists | Self-assessment |

**Frameworks mapped:** EU AI Act · MAS TRM 2021 · MAS FEAT · Singapore PDPA · IMDA Model AI Governance Framework · GDPR (incl. Art.22) · NIST AI RMF 1.0 · ISO/IEC 42001 · ISO/IEC 23053 · OWASP LLM Top 10 (**A-39 mapping baseline: 2025; current 2026 edition reviewed separately**) · MITRE ATLAS · MITRE PANOPTIC · CSA AI Controls Matrix

**Deliverables:** [44-artifact register](./04-ai-governance-and-technical-lifecycle/artifacts) · dual-track lifecycle case study · AI Governance Lifecycle Playbook

> The two figures worth leading with are the uncomfortable ones. **Until a third line has tested this, the framework is a statement of what it intends, verified by its author** — and that qualifies every other score in the assessment.

---

## The Thread Across All Four

| Discipline | On-premise origin | Cloud (P1–P3) | AI (P4) |
|---|---|---|---|
| Establish a baseline | Tripwire FIM | Config rules, IaC baseline | Corpus version-pinning, pre-declared thresholds |
| Monitor continuously | Guardium DAM | CloudTrail, GuardDuty, EventBridge | Drift, calibration, fairness, override band |
| Flag deviation | FIM change alerts | Config non-compliance, Access Analyzer | Grounding block-on-fail, two-sided override band |
| Remediate on cadence | Quarterly access review | Credential report cycle | Quarterly responsible model review |
| Defend the evidence | Audit rule design | Immutable logging, 7-year retention | 44-artifact chain, gate approval log |

**The methodology is unchanged. Only the control plane moved** — first to cloud infrastructure, now to AI systems.

---

## Credentials & Background

**Certifications:** CCSP, (ISC)² · Certified AI Governance Specialist (CAIGS) · IBM InfoSphere Guardium · Tripwire Enterprise · ITIL v3 · Certified ScrumMaster
**Education:** MBA (Systems), Periyar University · B.Sc. Computer Technology, K.S. Rangasamy College of Technology

**Current role:** Security Specialist (VP), NatWest Group — directing the Group's global Database Activity Monitoring and File Integrity Monitoring control estate across 700+ enterprise databases spanning AWS, GCP and private cloud, in a multi-jurisdiction banking environment. Owns traceability from regulatory requirement through policy and control design to audit-ready evidence.

---

## Cost

Projects 1–3 built entirely on **AWS Free Tier — target $0.00**, with a zero-spend budget alarm and documented teardown for every billable component. Project 4 required no AWS spend.

> Cost discipline is itself a governance control. P4's vector store was selected on the basis that it carries no standing capacity charge — an architecture with no idle resource has nothing to forget about, so it fails safe. The saving is a consequence of that property, not the reason for it.
