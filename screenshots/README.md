# Screenshot evidence

Screenshots support specific validations in this authorized personal lab. Review and sanitize sensitive identifiers before publication; redact credentials, enrollment secrets, confidential data, or unrelated private information if present. Lab hostnames and the displayed lab account provide correlation context.

Existing filenames are preserved. The sysmon/ and wazuh/ subfolders provide locations for future sanitized evidence; current images remain here.

| File | Evidence and documentation |
| --- | --- |
| [BeningTestSysmon.png](BeningTestSysmon.png) | Rule 100210 definition; [DET-004](../detections/README.md) |
| [ParentProcessNotepad.png](ParentProcessNotepad.png) | Parent ProcessGuid and ProcessId; [IR-001](../investigations/IR-001-sysmon-process-lineage.md) |
| [ChildProcessNotepad.png](ChildProcessNotepad.png) | Matching ParentProcessGuid and parent context |
| [notepad_user.png](notepad_user.png) | Filtered Event ID 1 list and ParentUser |
| [system_notepad..png](system_notepad..png) | SYSTEM header; [setup](../setup/04-sysmon.md) |
| [processid.png](processid.png) | Additional process ID view; insufficient alone for lineage |
| [LocalSysmonWorking.png](LocalSysmonWorking.png) | PowerShell Event ID 1 image, GUID, PID, UTC time |
| [SysmonSendingDetectiontoWazuh.png](SysmonSendingDetectiontoWazuh.png) | WIN11-01 alert row, rule 100210, level 7 |

The existing Screenshot 2026-10-18 180210.png is retained as supplied but is not cited as evidence of successful collection or detection.

Images are partial views. ParentUser does not independently prove the child's User, and an alert row does not show every expanded field. Operator-supplied facts are identified separately in IR-001. No screenshot or event export has been fabricated.
