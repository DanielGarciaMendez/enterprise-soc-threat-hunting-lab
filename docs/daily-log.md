# Engineering daily log

## October 18?19, 2026 ? Sysmon process telemetry and custom detection

**Work period:** October 18 local; the supplied UTC event and index dates extend into October 19. This entry records the completed session, not a separate future session.

- Installed Sysmon on WIN11-01 using .\Sysmon64.exe -accepteula -i; verified the service running.
- Inspected the Sysmon Operational channel and Event ID 1 process-create fields in Event Viewer.
- Ran Start-Process notepad.exe. Investigated Image, ParentImage, CommandLine, User, ParentUser, ProcessGuid, ParentProcessGuid, and UtcTime.
- Compared full GUID values across Notepad events; the parent ProcessGuid matched the child's ParentProcessGuid.
- Distinguished SYSTEM in the Event Viewer header from WIN11-01\daniel in process event data; the parent-context screenshot directly shows ParentUser.
- Configured the Wazuh Windows agent to collect Microsoft-Windows-Sysmon/Operational using eventchannel.
- Created SysmonPowershellTest.xml through the Dashboard: rule 100210, sysmon_event1 prerequisite, command-line match for ExecutionPolicy followed by whitespace and Bypass, level 7.
- Ran the benign PowerShell test with -NoProfile -ExecutionPolicy Bypass and Write-Output 'SOC-LAB-TEST'.
- Confirmed local Event ID 1 telemetry and the Threat Hunting alert: WIN11-01, rule 100210, level 7, LAB: PowerShell execution policy bypass.
- Recorded displayed alert timestamp Oct 18, 2026 @ 20:31:37.144 and supplied index wazuh-alerts-4.x-2026.10.19. Preserved UTC and local values without treating endpoint and alert timestamps as identical.

**Result:** Validated the tested endpoint ? agent ? custom rule ? alert path. Disposition: benign, authorized lab simulation.

**Artifacts:** [IR-001](../investigations/IR-001-sysmon-process-lineage.md), [Sysmon setup](../setup/04-sysmon.md), [DET-004](../detections/README.md), and [screenshot inventory](../screenshots/README.md).

**Open work:** Event IDs 3 and 22, negative tests, alternate syntax, noise measurements, and complete sanitized event/alert exports remain unvalidated or pending.
