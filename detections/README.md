# Detection engineering

**Status: One custom detection implemented and validated in an authorized personal lab.**

## Built-in Wazuh rule observed

- **Rule 60122** ? ?Logon Failure - Unknown user or bad password?; level 5; matched Windows Security Event 4625 for soc-test in the controlled authentication lab. Source EventRecordID **12488** confirmed end-to-end.
- This is **Wazuh's existing rule**, not a custom rule written in this project.
- One routine failure can match it; an indexed alert is not equivalent to a security incident or automatic paging threshold.
- See [IR-002](../investigations/IR-002-authentication.md).

## DET-004 ? PowerShell ExecutionPolicy Bypass via Sysmon Event 1

| Property | Validated state |
| --- | --- |
| Status | Implemented and validated with a benign positive test |
| Source | Custom Wazuh rule **100210**, file **SysmonPowershellTest.xml**, created through the Dashboard |
| Prerequisite | Sysmon Event ID 1 forwarded through Wazuh eventchannel collection |
| Endpoint | WIN11-01, Wazuh Agent 001 |
| Match | sysmon_event1 group; case-insensitive ExecutionPolicy followed by whitespace and Bypass in win.eventdata.commandLine |
| Result | Level **7**, **LAB: PowerShell execution policy bypass** |
| Disposition | Intentionally triggered benign, authorized lab alert |

### Rule definition

```xml
<group name="windows,sysmon,lab,">
  <rule id="100210" level="7">
    <if_group>sysmon_event1</if_group>
    <field name="win.eventdata.commandLine" type="pcre2">(?i)ExecutionPolicy\s+Bypass</field>
    <description>LAB: PowerShell execution policy bypass</description>
  </rule>
</group>
```

The rule field is win.eventdata.commandLine; the indexed alert exposes data.win.eventdata.commandLine.

### Validation

```powershell
powershell.exe -NoProfile -ExecutionPolicy Bypass -Command "Write-Output 'SOC-LAB-TEST'"
```

The resulting process event was collected and matched to rule 100210. Evidence: [rule content](../screenshots/BeningTestSysmon.png), [local Sysmon event](../screenshots/LocalSysmonWorking.png), and [Threat Hunting row](../screenshots/SysmonSendingDetectiontoWazuh.png). See [IR-001](../investigations/IR-001-sysmon-process-lineage.md) for supplied expanded alert fields.


## Detection backlog

| ID | Hypothesis | Status |
| --- | --- | --- |
| DET-001 | Broader suspicious PowerShell behavior with parent and command-line context | Planned; distinct from DET-004's narrow match |
| DET-002 | Multiple authentication failures within a tested time window | Planned; no custom threshold demonstrated |
| DET-003 | Unexpected scheduled-task creation | Planned |
| DET-004 | PowerShell ExecutionPolicy Bypass via Sysmon Event 1 | **Implemented and validated** |
| DET-005 | Suspicious process-to-network patterns | Planned; network telemetry not independently validated |

