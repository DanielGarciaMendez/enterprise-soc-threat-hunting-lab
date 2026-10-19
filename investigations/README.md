# Investigations and triage records

## Completed reports

| ID | Title | Disposition | Evidence |
| --- | --- | --- | --- |
| [IR-001](IR-001-sysmon-process-lineage.md) | Sysmon process lineage and controlled PowerShell detection | **Benign, authorized test activity** | Sysmon Event ID 1, ProcessGuid/ParentProcessGuid correlation, custom rule 100210, level-7 alert |
| [IR-002](IR-002-authentication.md) | Controlled authentication anomaly / Windows-to-Wazuh correlation | **Benign, authorized test** | Security Event 4625, EventRecordID 12488, built-in rule 60122; associated clock troubleshooting |

Identifiers follow the original project plan rather than completion order. These reports document controlled lab validations, not real incident response engagements.

## Planned investigations

- IR-003 ? Scheduled-task or service-based persistence: planned simulation only.
- IR-004 ? Endpoint forensics: planned.
- IR-005 ? Multi-stage controlled investigation: planned.

## Minimum reporting standard

Separate observations, provenance and timestamps, correlation keys, interpretation, benign alternatives, disposition, and unknowns. Label simulations explicitly. A SIEM rule hit is not a confirmed incident.

Publish sanitized evidence; review credentials, private identifiers, SIDs, and enrollment keys before publication. See [evidence guidance](../screenshots/README.md).
