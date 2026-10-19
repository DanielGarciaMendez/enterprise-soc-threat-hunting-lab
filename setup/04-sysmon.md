# 04 ? Sysmon endpoint telemetry

**Status: Implemented!**

## Purpose

Sysmon provides endpoint telemetry on WIN11-01; the Wazuh agent collects that telemetry. This milestone completed process creation (Event ID 1), process lineage, and a custom command-line detection in this personal lab.

## Installation

Sysmon was installed on the Windows 11 VM with:

```powershell
.\Sysmon64.exe -accepteula -i
```

The Sysmon service was verified running. Events were inspected under **Applications and Services Logs ? Microsoft ? Windows ? Sysmon ? Operational**. 

## Validation steps

1. Verify the Sysmon service is running.
2. Generate benign process activity with the Notepad command below.
3. Locate Event ID **1 ? Process Create** in the Operational log.
4. Inspect Image, ParentImage, CommandLine, User, ParentUser, ProcessGuid, ParentProcessGuid, and UtcTime.
5. Compare complete process GUID values across related events.
6. Validate centralized collection by triggering the custom rule and reviewing the Wazuh alert.

Event ID 1 is informational process-creation telemetry. It does not inherently indicate malicious execution.

The [filtered Event ID 1 list](../screenshots/notepad_user.png) shows repeated process activity. The [local PowerShell event](../screenshots/LocalSysmonWorking.png) shows the image, ProcessGuid, ProcessId, and UtcTime.

## Notepad process investigation

```powershell
Start-Process notepad.exe
```

Multiple related process events were learned. I was able to see and study a PowerShell-created Notepad process and another event with a parent image under C:\Program Files\WindowsApps\Microsoft.WindowsNotepad_....

ProcessGuid identifies a process instance; ParentProcessGuid identifies its parent instance. Compare the complete values, alongside host and time, rather than assuming similarly named processes are related.

The [parent event](../screenshots/ParentProcessNotepad.png) and [child parent fields](../screenshots/ChildProcessNotepad.png) share GUID **{287feed3-504a-6ad5-b904-000000000500}**, supporting a parent-child relationship. See [IR-001](../investigations/IR-001-sysmon-process-lineage.md).

Event Viewer's header displayed **SYSTEM**, while the operator's EventData observations identified the process user as **WIN11-01\daniel**. Event-recording security context can differ from the user running the process. Read User and ParentUser separately in EventData. The [header image](../screenshots/system_notepad..png) shows SYSTEM; the [parent-context image](../screenshots/notepad_user.png) directly shows ParentUser, rather than the child's User field.

## Wazuh integration

Windows Wazuh Agent **001**, version **4.14.7**, was configured with this ossec.conf collection block:

```xml
<localfile>
  <location>Microsoft-Windows-Sysmon/Operational</location>
  <log_format>eventchannel</log_format>
</localfile>
```

I needed to create this custom alert to show that sysmon and wazuh were properly sending telemetry! 

## Controlled detection test

Custom file **SysmonPowershellTest.xml** was created through the Wazuh Dashboard:

```xml
<group name="windows,sysmon,lab,">
  <rule id="100210" level="7">
    <if_group>sysmon_event1</if_group>
    <field name="win.eventdata.commandLine" type="pcre2">(?i)ExecutionPolicy\s+Bypass</field>
    <description>LAB: PowerShell execution policy bypass</description>
  </rule>
</group>
```

[Rule-definition screenshot](../screenshots/BeningTestSysmon.png).

The benign test command was:

```powershell
powershell.exe -NoProfile -ExecutionPolicy Bypass -Command "Write-Output 'SOC-LAB-TEST'"
```

Wazuh Threat Hunting displayed rule **100210**, level **7**, description **LAB: PowerShell execution policy bypass**, on WIN11-01. See the [alert row](../screenshots/SysmonSendingDetectiontoWazuh.png) and [DET-004](../detections/README.md).

The rule checks command-line text within the sysmon_event1 group.

## Lessons demonstrated

- Validate the endpoint event, then centralized collection and detection.
- Full GUID matching supports lineage analysis; process names alone are insufficient.
- Header security context and process identity can differ.
- Suspicious command lines require user, parent, purpose, and follow-on context.
- Successful rule matching validates a detection path; incident classification requires analysis.

