# Enterprise SOC, Threat Hunting & Incident Response Lab

> A hands-on enterprise security lab focused on SIEM monitoring, Windows endpoint telemetry, Active Directory, threat hunting, incident response, detection engineering, and digital forensics.

## Overview

A project about small enterprise security environment and operating it from the perspective of a SOC analyst.

The goal of this project is to develop practical experience collecting and analyzing endpoint and identity telemetry, investigating suspicious behavior, creating detections, performing threat hunts, mapping activity to MITRE ATT&CK, and documenting incident response findings.

The core workflow of the project is:

**Telemetry → Detection / Hypothesis → Triage → Investigation → Scope → MITRE ATT&CK → Containment → Remediation → Detection Improvement → Reporting**

This repository documents both the technical environment and the investigations performed throughout the project.

---

## Lab Architecture

```text
                    Physical Windows Host
                            |
                    VirtualBox Hypervisor
                            |
             +--------------+--------------+
             |                             |
       WAZUH-SOC01                     WIN11-01
       Ubuntu Server                   Windows 11
             |                             |
       Wazuh Server                   Wazuh Agent
       Wazuh Indexer                  Sysmon [Planned]
       Wazuh Dashboard
             |
             |
         DC01 [Planned]
         Windows Server
         10.10.10.5
         Active Directory / DNS
```

### Network Design

Each core VM uses:

* **NAT adapter** — Internet access for updates and software installation
* **Host-Only adapter** — Isolated communication between lab systems

---

## Current Assets

| Host        | Operating System    | IP          | Role                   | Status         |
| ----------- | ------------------- | ----------- | ---------------------- | -------------- |
| WAZUH-SOC01 | Ubuntu Server 24.04 | 10.10.10.10 | SIEM / Wazuh Server    | ✅ Running      |
| WIN11-01    | Windows 11          | 10.10.10.20 | Employee Endpoint      | 🚧 In Progress |
| DC01        | Windows Server      | 10.10.10.5  | Active Directory / DNS | ⏳ Planned      |

---

## Technology Stack

### Currently Implemented

* VirtualBox
* Ubuntu Server 24.04
* Wazuh Server
* Wazuh Indexer
* Wazuh Dashboard
* Linux

### Planned

* Windows 11
* Wazuh Windows Agent
* Sysmon
* Windows Server
* Active Directory
* DNS
* PowerShell
* MITRE ATT&CK
* Velociraptor
* Python

---

## Project Progress

* [x] Create Ubuntu Server VM
* [x] Configure WAZUH-SOC01
* [x] Install Wazuh Server
* [x] Install Wazuh Indexer
* [x] Install Wazuh Dashboard
* [x] Verify Wazuh Dashboard access
* [ ] Create WIN11-01
* [ ] Configure Windows lab networking
* [ ] Install Wazuh Windows Agent
* [ ] Confirm WIN11-01 appears Active in Wazuh
* [ ] Generate baseline Windows events
* [ ] Create clean VM snapshots

### Upcoming

* [ ] Sysmon & Endpoint Telemetry
* [ ] Active Directory & Identity Telemetry
* [ ] PowerShell, MITRE ATT&CK & Detection Engineering
* [ ] First Incident Response Investigation

---

## Wazuh Architecture

The Wazuh deployment consists of three components:

### Wazuh Agent

Installed on monitored endpoints such as `WIN11-01`.

The agent collects security information from the endpoint and forwards relevant telemetry to the Wazuh server.

### Wazuh Server

Receives and analyzes security data from endpoints.

It applies rules and detection logic to determine whether activity should generate security alerts.

### Wazuh Indexer

Stores and organizes security events so that they can be efficiently searched and analyzed.

### Wazuh Dashboard

Provides the graphical interface used by the analyst to:

* Review alerts
* Search security events
* Investigate endpoint activity
* Filter telemetry
* View dashboards
* Analyze historical activity

---

## Data Flow

```text
Windows Activity
      |
      v
Windows Event Logs / Sysmon
      |
      v
Wazuh Agent
      |
      v
Wazuh Server
      |
      v
Wazuh Indexer
      |
      v
Wazuh Dashboard
      |
      v
SOC Analyst Investigation
```

---

## Detections

Detection engineering work will be documented in the [`detections/`](detections/) directory.

Planned detections include:

| ID      | Detection                   | Status    |
| ------- | --------------------------- | --------- |
| DET-001 | Suspicious PowerShell       | ⏳ Planned |
| DET-002 | Authentication Failures     | ⏳ Planned |
| DET-003 | Scheduled Task Activity     | ⏳ Planned |
| DET-004 | Custom Wazuh Rule           | ⏳ Planned |
| DET-005 | Suspicious Network Activity | ⏳ Planned |

---

## Threat Hunts

Threat-hunting investigations will be documented in the [`threat-hunts/`](threat-hunts/) directory.

| ID     | Hunt                    | Status    |
| ------ | ----------------------- | --------- |
| TH-001 | PowerShell Activity     | ⏳ Planned |
| TH-002 | Persistence             | ⏳ Planned |
| TH-003 | Authentication Activity | ⏳ Planned |

---

## Incident Investigations

Completed incident investigations will be documented in the [`investigations/`](investigations/) directory.

| ID     | Investigation          | Status    |
| ------ | ---------------------- | --------- |
| IR-001 | Suspicious PowerShell  | ⏳ Planned |
| IR-002 | Authentication Anomaly | ⏳ Planned |
| IR-003 | Persistence            | ⏳ Planned |
| IR-004 | Endpoint Forensics     | ⏳ Planned |
| IR-005 | Multi-Stage Incident   | ⏳ Planned |

---

## MITRE ATT&CK Coverage

Observed behaviors and detections will be mapped to MITRE ATT&CK only when supported by evidence collected in the lab.

Coverage will be documented in:

[`mitre-attack/coverage.md`](mitre-attack/coverage.md)

---

## Automation

Planned automation includes:

### PowerShell

`Triage-Endpoint.ps1`

Endpoint triage script designed to collect:

* Running processes
* Services
* Network connections
* Scheduled tasks
* User information
* Selected Windows Event Logs

### Python

`log_summary.py`

Utility for summarizing exported security telemetry by fields such as:

* Event ID
* Process
* User
* Host
* Destination

---

## Documentation

Additional documentation is maintained throughout the project:

```text
docs/
├── daily-log.md
├── learning-journal.md
├── lessons-learned.md
└── interview-notes.md
```

### Learning Journal

The learning journal tracks:

* Questions I encounter
* Concepts I initially do not understand
* Answers discovered during the project
* Troubleshooting lessons
* Technical concepts I want to revisit

---

## Current Learning Topics

Some of the concepts being developed through this project include:

* SIEM architecture
* Security telemetry
* Endpoint monitoring
* Windows Event Logs
* Process lineage
* Sysmon
* Active Directory
* Authentication telemetry
* PowerShell investigation
* Detection engineering
* Threat hunting
* MITRE ATT&CK
* Incident response
* Digital forensics

---

## Troubleshooting Experience

The lab also documents technical failures and troubleshooting rather than hiding them.

Examples encountered during initial deployment include:

* Wazuh repository connectivity issues
* DNS resolution problems
* GPG signing-key configuration
* Wazuh Indexer package installation
* Linux permissions
* VirtualBox networking

These troubleshooting experiences are documented because understanding **why something failed and how it was diagnosed** is an important part of operating security infrastructure.

---

## Repository Structure

```text
enterprise-soc-threat-hunting-lab/
│
├── README.md
│
├── architecture/
│   ├── architecture-diagram.png
│   ├── network-diagram.png
│   └── asset-inventory.md
│
├── setup/
│   ├── 01-lab-environment.md
│   ├── 02-wazuh-siem.md
│   ├── 03-windows-agent.md
│   ├── 04-sysmon.md
│   ├── 05-active-directory.md
│   ├── 06-powershell-logging.md
│   └── 07-velociraptor.md
│
├── detections/
├── threat-hunts/
├── investigations/
├── scripts/
├── malware-triage/
├── mitre-attack/
├── screenshots/
│
└── docs/
    ├── daily-log.md
    ├── learning-journal.md
    ├── lessons-learned.md
    └── interview-notes.md
```

---

## Safety & Ethics

All security testing, simulations, and investigations in this repository are performed exclusively on systems and virtual machines that I own and control.

The lab is isolated from third-party, school, employer, and public systems.

No adversary-emulation activity is conducted against unauthorized targets.

---

## Project Status

**Status:** 🚧 In Progress
**Started:** August 2026

Current milestone:

**Building the endpoint → Wazuh SIEM telemetry pipeline.**
