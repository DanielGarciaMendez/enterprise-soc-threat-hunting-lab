# Detection engineering

**Status: No custom detections implemented or validated as of 2026-10-15.**

## Built-in Wazuh rule observed

- **Rule 60122** — “Logon Failure - Unknown user or bad password”; level 5; matched Windows Security Event 4625 for `soc-test` in the controlled lab. Source EventRecordID **12488** confirmed end-to-end.
- This is **Wazuh's existing rule**, not a custom rule written in this project.
- One routine failure can match this rule; an indexed alert is not equivalent to a security incident, SOC case or automatic paging threshold.

## Detection backlog

| ID | Hypothesis | Status |
| --- | --- | --- |
| DET-001 | Potentially suspicious PowerShell process behavior with parent/command-line context | Planned; requires Sysmon |
| DET-002 | Multiple authentication failures within a tested time window and meaningful context | Planned; no custom rule or threshold demonstrated |
| DET-003 | Unexpected scheduled-task creation | Planned |
| DET-004 | Controlled custom Wazuh rule with documented tests and noise analysis | Planned |
| DET-005 | Suspicious process-to-network patterns | Planned; requires appropriate telemetry |

## Criteria before calling a detection implemented

Document the rule source and logic, logging prerequisites, benign expected cases, positive and negative test results, severity rationale, false-positive analysis, and an evidence screenshot/record from the lab. Correlation of a built-in Wazuh rule does not satisfy this custom-rule milestone.
