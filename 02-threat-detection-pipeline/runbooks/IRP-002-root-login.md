### 🔴 Playbook: IRP-002: Root Account Login Detected
> **Trigger:** `SECURITY-root-login-detected` alarm fires (Period=60s)
> **Detection layer:** CloudWatch Alarm on the CloudTrail root-login metric
> **Alert path:** SNS → email — P1-critical by definition, since root bypasses every IAM permission boundary and SCP from Project 1
> **Immediate action:** Confirm via CloudTrail (`userIdentity.type=Root`); check source IP against known corporate ranges; escalate immediately if unrecognised.
> **Rollback:** Rotate root password; revoke active root access keys; preserve CloudTrail logs for the session window.
> **Escalation:** Security Architect → CISO → Legal if data exposure is confirmed.
> **Regulatory clock:** MAS TRM 1-hour threshold if customer data or critical systems were accessed.