### 🔴 Playbook: IRP-001: IAM Access Key Creation
> **Trigger:** `SECURITY-iam-policy-change-detected` alarm fires, or the IAM-key-creation EventBridge rule matches
> **Detection layer:** CloudWatch Alarm (metric filter) + EventBridge (event pattern)
> **Alert path:** CloudWatch Alarm → SNS → email to on-call within 5 minutes
> **Immediate action:** Confirm the change against the approved change calendar. No matching ticket → treat as unauthorized.
> **Rollback:** Compare against the last-known-good IAM policy version; re-attach via `aws iam set-default-policy-version`; disable the key/session if attached to a suspicious principal.
> **Escalation:** Security Architect → CISO if privilege escalation is suspected (e.g. `AdministratorAccess` added, or a permission boundary removed).
> **Regulatory clock:** MAS TRM Section 13 (1-hour internal threshold) starts at detection.