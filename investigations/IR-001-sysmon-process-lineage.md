# IR-001 ? Sysmon process lineage and controlled PowerShell detection

## Environment

| Asset | Role and identifiers |
| --- | --- |
| WIN11-01 | Windows 11 VM; Wazuh Agent **001**, version **4.14.7**, Active; agent-displayed IP **10.0.2.15** |
| WAZUH-SOC01 | Ubuntu VM; Wazuh Manager, Indexer, Dashboard; host-only IP **192.168.56.101** |
| Endpoint source | Microsoft-Windows-Sysmon/Operational, Event ID **1** |
| Detection | Custom file SysmonPowershellTest.xml, rule **100210**, level **7** |

The displayed agent IP is not asserted to be its host-only interface address.

## Objective

Document Sysmon process-creation analysis and validate centralized detection through Wazuh using a benign PowerShell command.

## Scope

Authorized personal lab activity on the Windows endpoint and Wazuh server only: installation, local process inspection, lineage correlation, forwarding, custom rule creation, and positive alert validation. 

## Evidence sources

- Sysmon Operational log, Event ID 1.
- Related Notepad events and full ProcessGuid/ParentProcessGuid values.
- Dashboard custom rule definition.
- Local PowerShell process-create event.
- Wazuh Threat Hunting alert row.
- Additional observations and expanded alert fields supplied by the lab operator.

Screenshots support the fields visible in each image. Expanded alert fields below were supplied separately; the alert-row screenshot does not display all of them. No raw event export is included.

## Procedure

1. Install Sysmon on WIN11-01 and verify its service is running:

```powershell
.\Sysmon64.exe -accepteula -i
```

2. Generate a harmless Notepad launch:

```powershell
Start-Process notepad.exe
```

3. Inspect Event ID 1. Review Image, ParentImage, CommandLine, User, ParentUser, ProcessGuid, ParentProcessGuid, and UtcTime.
4. Compare full GUID values across related events.
5. Configure Wazuh agent collection in ossec.conf:

```xml
<localfile>
  <location>Microsoft-Windows-Sysmon/Operational</location>
  <log_format>eventchannel</log_format>
</localfile>
```

6. Create SysmonPowershellTest.xml through the Wazuh Dashboard:

```xml
<group name="windows,sysmon,lab,">
  <rule id="100210" level="7">
    <if_group>sysmon_event1</if_group>
    <field name="win.eventdata.commandLine" type="pcre2">(?i)ExecutionPolicy\s+Bypass</field>
    <description>LAB: PowerShell execution policy bypass</description>
  </rule>
</group>
```

7. Run the benign detection test:

```powershell
powershell.exe -NoProfile -ExecutionPolicy Bypass -Command "Write-Output 'SOC-LAB-TEST'"
```

8. Verify the local process event and the resulting level-7 rule 100210 alert in Threat Hunting.

See [Sysmon setup](../setup/04-sysmon.md) and [DET-004](../detections/README.md).

## Findings

### Notepad process creation and lineage

Sysmon captured multiple related Notepad events. The operator observed a PowerShell-created Notepad process and another event with a parent path under C:\Program Files\WindowsApps\Microsoft.WindowsNotepad_....

The screenshots support a concrete GUID correlation:

| Evidence | Field | Value |
| --- | --- | --- |
| [Parent event](../screenshots/ParentProcessNotepad.png) | ProcessGuid | {287feed3-504a-6ad5-b904-000000000500} |
| Parent event | ProcessId | 7984 |
| [Child parent context](../screenshots/ChildProcessNotepad.png) | ParentProcessGuid | {287feed3-504a-6ad5-b904-000000000500} |
| Child parent context | ParentProcessId | 7984 |
| Child parent context | ParentUser | WIN11-01\daniel |

The full GUID match supports a parent-child relationship. Names alone would not establish this linkage. The child-parent screenshot does not show that child's own ProcessGuid, so a complete multi-process tree is not reconstructed here. The PowerShell launch relationship is an operator-supplied observation rather than a separately visible PowerShell-parent screenshot.

### Header context and process identity

The [Event Viewer header](../screenshots/system_notepad..png) displays **SYSTEM**. Operator observations identify the process User in EventData as **WIN11-01\daniel**; the [parent-context image](../screenshots/notepad_user.png) directly shows **ParentUser** as that account.

Event-recording security context is not necessarily the executing user's context. Inspect User and ParentUser separately. ParentUser identifies the parent account and is not a substitute for the child's User field.

### Local and centralized PowerShell validation

The [local PowerShell event](../screenshots/LocalSysmonWorking.png) shows Event ID **1**, the PowerShell image, ProcessGuid, ProcessId **5084**, and UtcTime. That image does not expose the complete command line.

The [rule image](../screenshots/BeningTestSysmon.png) shows rule **100210**, its regex, and level **7**. The [Threat Hunting row](../screenshots/SysmonSendingDetectiontoWazuh.png) shows **WIN11-01**, rule **100210**, level **7**, and **LAB: PowerShell execution policy bypass**.

Operator-supplied expanded alert fields:

| Field | Recorded value |
| --- | --- |
| index | wazuh-alerts-4.x-2026.10.19 |
| agent.id | 001 |
| agent.name | WIN11-01 |
| data.win.system.channel | Microsoft-Windows-Sysmon/Operational |
| data.win.system.eventID | 1 |
| data.win.eventdata.image | C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe |
| data.win.eventdata.user | WIN11-01\daniel |
| data.win.eventdata.parentImage | C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe |
| rule.id | 100210 |
| rule.level | 7 |
| rule.description | LAB: PowerShell execution policy bypass |
| timestamp (displayed) | Oct 18, 2026 @ 20:31:37.144 |

Recorded data.win.eventdata.commandLine:

```powershell
"C:\WINDOWS\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -ExecutionPolicy Bypass -Command "Write-Output 'SOC-LAB-TEST'"
```

The supplied result demonstrates collection and custom matching for the controlled process event. A raw alert export containing ProcessGuid would strengthen independent record-level correlation beyond the screenshots and supplied fields.


## Disposition

**Benign, authorized lab simulation.** The test successfully validated Sysmon Event ID 1 collection and custom Wazuh rule 100210. 


## What did I learn?

1. Confirm endpoint telemetry before validating centralized detection.
2. Correlate full GUID values and host/time context.
3. Read process identity from EventData.
4. Document prerequisites, rule logic, test command, results, and evidence boundaries.
5. Separate telemetry, detection logic, alerts, and incident assessment.

## Evidence appendix

| Evidence | Purpose |
| --- | --- |
| [Rule definition](../screenshots/BeningTestSysmon.png) | File, rule ID, regex, level, description |
| [Parent event](../screenshots/ParentProcessNotepad.png) | Parent GUID and PID |
| [Child parent context](../screenshots/ChildProcessNotepad.png) | ParentProcessGuid, ParentImage, ParentCommandLine, ParentUser |
| [Filtered Operational log](../screenshots/notepad_user.png) | Repeated Event ID 1 activity |
| [SYSTEM header](../screenshots/system_notepad..png) | Event Viewer recording context |
| [Local PowerShell event](../screenshots/LocalSysmonWorking.png) | Endpoint process-create telemetry |
| [Wazuh alert row](../screenshots/SysmonSendingDetectiontoWazuh.png) | Successful custom level-7 alert |

See [screenshot inventory](../screenshots/README.md).
