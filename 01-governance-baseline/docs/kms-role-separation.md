# KMS Key — Role Separation

- Alias: alias/portfolio-data-key
- Key administrators: portfolio-admin IAM user (rotate/disable rights, no decrypt)
- Key users: GovernanceAuditorRole (decrypt rights, no admin rights)
- Automatic annual key rotation: enabled
- Rationale: Segregation of Duties — the admin who manages the key cannot
  use it to read data; the role that reads data cannot change key settings.
  Extends the SoD discipline from Guardium/Tripwire FIM governance work.
