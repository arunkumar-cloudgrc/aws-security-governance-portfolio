### 🔴 Playbook: IRP-003: S3 Public Access Change
> **Trigger:** EventBridge S3 rule matches `PutBucketPublicAccessBlock`, `DeletePublicAccessBlock`, or `PutBucketAcl`
> **Detection layer:** EventBridge — typically single-digit seconds after CloudTrail delivers the event
> **Alert path:** EventBridge → SNS — treated as a live data-exposure event, not a future risk (CSA's #1-ranked cloud threat)
> **Immediate action:** Immediately re-apply S3 Block Public Access; identify who/what made the change and why.
> **Rollback:** Restore the bucket policy to last-known-good; if the bucket held sensitive data, treat as a potential breach pending investigation.
> **Escalation:** Security Architect → Data Protection Officer if personal data may have been exposed.
> **Regulatory clock:** GDPR Art.33 (72-hour) and PDPA mandatory notification clocks start at detection if personal data exposure is confirmed.