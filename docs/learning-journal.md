# SOC learning journal

# What is WAZUH?
Wazuh is an open-source security monitoring/SIEM platform that collects and analyzes security telemetry from endpoints. In this lab, Wazuh will act as the central location where I can view Windows and Sysmon events, investiagate alerts, correlate activity between systems, and building custom detections. 

Endpoints use a Wazuh agent to send their security data to the Wazuh server, where the data will be analyzed and viewed!


There is a WAZUH agent that has three central compoinets: 
1. Wazuh Server
2. Wazuh Indexer 
3. Wazuh Dashboard 

# What is telemetry?
It is just data that a computer/system generated about what it is doing...

# Why are we using Sysmon if a Wazuh agent can also collect and send data?
The Wazuh agent can collect security information by itself, but Sysmon provides richer Windows endpoint telemetry. Sysmon records detailed process, network, file, and other system activity, while the Wazuh agent collects those Sysmon events and sends them to the Wazuh server for centralized analysis.

## Concepts demonstrated with real events (October 14–15, 2026)

**Event vs alert vs case vs incident.** Windows Event 4625 is a log entry. Wazuh's built-in rule 60122 matched the log and created an indexed level-5 alert. No incident or automatic SOC ticket was proven. One failed password can be ordinary user error.

**Windows logon types.** Type 2 means interactive; the local `runas` test produced Type 2. Type 5 denotes a service logon; a SYSTEM 4624 baseline event involved `services.exe`. Type 10 commonly signals RemoteInteractive/RDP, but no real RDP scenario was performed here.

**Authentication fields.** `0xC000006D` indicates logon failure; `0xC000006A` narrows it to incorrect password. Target identity and requesting/subject identity are not always the same account.

**Network source.** `::1` is IPv6 loopback, equivalent in role to `127.0.0.1` for IPv4. This does not identify a remote attacker. NAT/VPN/proxy addresses cannot be equated to a unique human without supporting records.

**Correlation keys.** Match computer, event channel, Event ID, and Windows EventRecordID to link the original and SIEM records. The `0x3E7` SYSTEM Logon ID can recur and is not unique evidence of one service launch.

**Privilege interpretation.** Event 4672 reports assignment of special privileges to a session; it does not prove a privilege escalation or that a listed privilege was used.

**Clock hygiene.** SIEM alerts can preserve the correct embedded endpoint event time while stamping the received alert with an incorrect manager time. `timedatectl`, `chronyc tracking`, and `chronyc makestep` helped detect and repair that gap.

## Questions to revisit

- How do different Windows authentication methods change the event logon type and source fields?
- When should an indexed low-level authentication alert be enriched, aggregated, or escalated?
- How do raw Windows event archives and Wazuh alerts differ?
- How can I measure collection gaps, latency and false-positive rates?
- How do Sysmon ProcessGuid and Windows logon/session IDs complement each other?
- How do TCP, DNS, TLS, NAT, and VPN telemetry affect attribution?
