# Investigations and triage records

## Verified reports

| ID | Title | Disposition | Evidence |
| --- | --- | --- | --- |
| [IR-002](IR-002-authentication.md) | Controlled authentication anomaly / Windows-to-Wazuh correlation | **Benign, authorized test** | Security Event 4625, EventRecordID `12488`, rule `60122`; associated clock troubleshooting |

`IR-002` reflects the ID assigned to authentication work in the original project plan. **IR-001 has not been completed** and its absence is intentional.

## Planned investigations

- IR-001 — Suspicious PowerShell / process lineage: requires Sysmon and controlled blind scenarios.
- IR-003 — Scheduled-task or service-based persistence: planned simulation only.
- IR-004 — Endpoint forensics: planned.
- IR-005 — Multi-stage controlled investigation: planned.

## Minimum reporting standard

Every completed report should explicitly separate: hypothesis, raw observations, event provenance and timestamps, correlation keys, benign alternatives, confidence, impact, disposition, escalation decision and unknowns. Label simulated activity as simulated. A SIEM rule hit is not a confirmed incident.

Publish only sanitized exports/screenshots. Never expose real usernames, credentials, sensitive SIDs, or enrollment keys.
