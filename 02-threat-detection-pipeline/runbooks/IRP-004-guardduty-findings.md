### 🟠 Playbook: IRP-004: Suspicious API Call Pattern (GuardDuty severity ≥ 7)
> **Trigger:** EventBridge rule matches any GuardDuty finding with severity ≥ 7
> **Detection layer:** GuardDuty (behavioural) → EventBridge (severity filter — avoids alert fatigue by design)
> **Alert path:** EventBridge → SNS — severity filtering means every alert that reaches a human is worth their attention
> **Immediate action:** Review the finding type and affected resource; correlate against the Dashboard's Logs Insights query for the same window.
> **Rollback:** Depending on finding type — revoke the affected credential/session, isolate the instance's security group, or block the source IP.
> **Escalation:** Security Architect; CISO if the finding indicates likely credential compromise rather than a false positive.
> **Regulatory clock:** Assessment-dependent — MAS TRM 1-hour clock starts if confirmed genuine.