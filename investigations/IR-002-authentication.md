# IR-002 — Controlled Windows authentication anomaly

**Date:** October 15, 2026  
**Environment:** Authorized personal VirtualBox SOC lab  
**Endpoint:** `WIN11-01` — Windows 11 Home  
**SIEM:** `WAZUH-SOC01` — Wazuh Manager, Indexer, Dashboard  
**Disposition:** Authorized test activity; no observed compromise  
**Scope:** Foundational event triage and telemetry validation, not a production incident

## Objective

Validate that a Windows failed-logon record can be collected locally, forwarded to Wazuh, and identified as the same underlying event. Distinguish the evidence from conclusions about malicious activity.

## Evidence and correlation

| Property | Observation |
| --- | --- |
| Windows Security event | **4625** (failed logon) |
| Windows Event Record ID | **12488** |
| Target user | `soc-test` (disposable local test account) |
| Logon type | **2** (Interactive) |
| Status / Substatus | `0xC000006D` / `0xC000006A` (authentication failure / incorrect password) |
| Caller process | `C:\Windows\System32\svchost.exe` (`0x670`) |
| Logon process | `seclogo` |
| Source address | `::1` (IPv6 loopback; not an external IP) |
| Windows event time | **2026-10-15 13:11:20.556 UTC** (08:11:20 CDT) |
| Wazuh agent | `WIN11-01`, ID `001` |
| Wazuh alert rule | **60122**, level **5**, `Logon Failure - Unknown user or bad password` |
| Wazuh's embedded Windows record ID | **12488** — verified against Event Viewer |


## Additional authentication baseline

- A controlled correct-password `runas` attempt for `soc-test` produced a local **4624** successful logon with **Type 2** and a new-logon ID of `0x36940C`. The successful event was not separately verified in Wazuh.
- Earlier baseline **4624** and **4672** events involved `NT AUTHORITY\SYSTEM`, Logon ID `0x3E7`, with the 4624 reporting Type **5** (service) and `services.exe`. These records are consistent with SYSTEM service activity; they do not prove a particular service was authorized. `0x3E7` is a well-known SYSTEM logon ID, not a unique identifier for an individual service startup.

## Challanger Faces Through this Mock Investigation

1. An inconsistency arose between the original Windows event time and the Wazuh alert's own timestamp:

| Clock/field | Captured UTC value |
| --- | --- |
| Embedded Windows `systemTime` | **2026-10-15 13:11:20.556** |
| Wazuh alert `timestamp` | **2026-10-15 04:23:56.915** |

The Wazuh alert timestamp was earlier than the Windows creation time; this was not a time-zone conversion issue.

## Assessment

- **Observed:** A failed interactive login using an incorrect password for the test account was recorded on the Windows VM and centrally indexed as a Wazuh level-5 authentication-failure alert.
- **Not demonstrated:** External intrusion, brute-force attack, stolen credentials, malicious privilege escalation, or compromise. `::1` refers to the local machine.
- **Limitations:** Not every authentication event necessarily creates an alert. Rule severity is not equivalent to business risk; rule-provided MITRE ATT&CK tags do not establish that a technique occurred.

## Follow-up

1. Save redacted screenshots and/or sanitized CSV excerpts of Event Viewer and Wazuh evidence (not attached yet).
2. Add Sysmon, confirm its process telemetry in Wazuh, and perform a separate **blind simulated investigation**.

