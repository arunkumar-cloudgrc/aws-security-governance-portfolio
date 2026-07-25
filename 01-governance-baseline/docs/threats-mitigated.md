# Threats Mitigated

This section outlines the primary threat scenarios addressed in Project 1, mapped to MITRE ATT&CK references and the AWS controls deployed to mitigate them.

| Threat Scenario | ATT&CK Reference | Mitigating Control |
|-----------------|------------------|--------------------|
| Compromised or misused root credentials | TA0004 / T1078.004 Valid Accounts: Cloud Accounts | MFA enforcement + Config root‑MFA rule + login alarm |
| Accidental or malicious S3 public exposure | TA0009 / T1530 Data from Cloud Storage | S3 Block Public Access + Config public‑access rule + deny‑HTTP policy |
| Audit log tampering or deletion to cover tracks | TA0005 / T1562.008 Impair Defenses: Disable Cloud Logs | CloudTrail log file validation + S3 Object Lock (GOVERNANCE, 7yr) |
| Encryption key misuse or unauthorised decrypt | TA0006 Credential Access (key misuse) | KMS role separation (admin ≠ user) + annual rotation |
| Configuration drift introducing unnoticed gaps | TA0005 Defense Evasion (config weakening) | AWS Config continuous evaluation, 10 managed rules |
| Undetected anomalous account activity | General reconnaissance / lateral movement | Amazon GuardDuty behavioural detection |

---

## Context

- **Root credential misuse**: Enforced MFA and login alarms prevent attackers from exploiting privileged accounts.  
- **S3 exposure**: Public access is blocked by default, with Config rules and deny‑HTTP policies ensuring no accidental leakage.  
- **Audit log tampering**: CloudTrail log validation and S3 Object Lock guarantee log integrity and immutability for 7 years.  
- **Key misuse**: KMS enforces strict role separation and annual rotation to prevent unauthorized decrypt operations.  
- **Configuration drift**: AWS Config continuously evaluates against 10 managed rules, detecting and remediating drift.  
- **Anomalous activity**: GuardDuty provides behavioural detection for reconnaissance, lateral movement, and account misuse.  

---

## Enterprise Scale Consideration

In production, these mitigations are deployed across all accounts via **CloudFormation StackSets** and enforced through **AWS Control Tower guardrails**.  
**AWS Config conformance packs** provide automated evidence for audit submissions, ensuring that threat scenarios are consistently mitigated across the enterprise.