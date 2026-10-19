# Enterprise SOC, Threat Hunting & Incident Response Lab

> **Status: In progress | October 15, 2026**  
> Personal VirtualBox lab for learning centralized security monitoring, endpoint investigations, detection engineering, and incident response.

## Project overview

I am building and operating a small enterprise-style security lab from the perspective of a SOC analyst. The focus is not just installing tools: it is understanding what telemetry proves, validating detections, correlating sources, investigating competing explanations, and documenting defensible conclusions.

**Investigation workflow:** Telemetry → Detection / hypothesis → Validation → Context and correlation → Assessment → Approved response if warranted → Documentation → Detection improvement.

Some parts of that workflow are future learning goals. I only mark capabilities as implemented after collecting evidence that they work.

## Milestone tracker

| Milestone | State | Evidence / next requirement |
| --- | --- | --- |
| 0 — Build Wazuh server and dashboard | **Done** | Wazuh Manager, Indexer, and Dashboard operating on `WAZUH-SOC01` |
| 1 — Windows authentication telemetry and SIEM correlation | **Done, documented** | `WIN11-01` agent Active; real Event 4625 correlated to Wazuh rule 60122 using Windows Record ID **12488**; [investigation](investigations/IR-002-authentication.md) |
| 1b — Redacted screenshots and formal export | **Not yet** | Collect/select sanitized artifacts and verify before publishing |
| 2 — Sysmon process telemetry | **Planned** | Install Sysmon; verify Event 1 and Wazuh forwarding; inspect process lineage |
| 3 — Blind SOC investigations | **Planned** | Safe benign vs adversary-like simulations, analyst unaware of scenario, documented disposition |
| 4 — Domain / identity infrastructure | **Planned** | Only after a DC is actually deployed and verified |
| 5 — Custom detection rules and threat hunts | **Planned** | Publish source, testing, false-positive analysis, and query evidence |

### Milestone 1 — What was actually accomplished 

- Brought up an all-in-one Wazuh deployment with Manager, Indexer, and Dashboard on Ubuntu.
- Connected a Windows 11 test VM with Wazuh Windows Agent **v4.14.7**. Its agent ID **001** was verified as **Active** in the dashboard.
- Inspected real Windows Security Events **4624** (successful logon), **4625** (failed logon), and **4672** (special privileges assigned at logon).
- Created a disposable local account, `soc-test`, and used an authorized local `runas` test to generate a failed authentication followed by an authorized successful authentication.
- Found a 4625 event in both Event Viewer and Wazuh; confirmed **the same underlying Windows record** by matching Event ID **4625** and **EventRecordID 12488** on `WIN11-01`. Wazuh's matched alert was rule **60122**, level **5**.
- Identified a central-alert timestamp discrepancy, diagnosed the Ubuntu clock using Chrony, and corrected a clock offset of approximately **8 h 44 min**. After the correction, Ubuntu reported **System clock synchronized: yes**.
- Assessed the known test as **benign, authorized lab activity**. No attacker, compromise, malware execution, or production incident was observed.

Read the [full authentication case](investigations/IR-002-authentication.md) and [daily log](docs/daily-log.md). A rule-generated SIEM alert is not automatically an analyst ticket, declared incident, or indication of a successful attack.

## Architecture

```text
                          Physical Windows Host
                             Oracle VirtualBox
                        /                       \\
            WAZUH-SOC01 (Ubuntu VM)             WIN11-01 (Windows 11 VM)
            - Wazuh Manager                    - Wazuh Agent v4.14.7 (001)
            - Wazuh Indexer                    - Windows Security audit logs
            - Wazuh Dashboard                  - Sysmon [NOT YET INSTALLED]
            - Chrony
                        ^                         |
                        |    Wazuh agent data     |
                        +-------------------------+
```

**Logical flow for the validated case:**

```text
Controlled local sign-in attempt
    → Windows Security Event 4625 on WIN11-01
    → Wazuh Windows Agent (001)
    → Wazuh Manager detection rule 60122
    → Wazuh alerts index
    → Wazuh Dashboard / Threat Hunting
    → Analyst correlation to EventRecordID 12488
```

The diagram simplifies internal Wazuh ingestion/indexing; it does not imply that **all** raw Windows events generate alerts. Sysmon telemetry and any future AD/DC path are not part of the currently validated pipeline.

### Network notes

| Asset | Role / version | Verified address or identifier | Status |
| --- | --- | --- | --- |
| `WAZUH-SOC01` | Ubuntu VM, Wazuh Manager/Indexer/Dashboard | Host-only **`192.168.56.101`**, HTTPS Dashboard reachable | Running |
| `WIN11-01` | Windows 11 Home, build `10.0.26200.8037` | Wazuh **Agent 001**, Agent v**4.14.7**; dashboard displayed IP **`10.0.2.15`** | Agent **Active** |
| `DC01` | Future Windows Server / AD / DNS | **No verified address** | Planned |

The VirtualBox design uses NAT for outbound access and a host-only adapter for lab connectivity. The agent-displayed `10.0.2.15` is **not** asserted to be the Windows VM's host-only address. Older `10.10.10.x` entries were design placeholders, not measured VM addresses; do not use them as deployed addresses.

See [asset inventory](architecture/asset-inventory.md), [environment](setup/01-lab-environment.md), and [Wazuh setup](setup/02-wazuh-siem.md).

## Current technology stack

| Implemented and demonstrated | Planned / not yet verified |
| --- | --- |
| VirtualBox, Ubuntu-based Wazuh VM, Wazuh Manager, Indexer, Dashboard | Sysmon, Microsoft-Windows-Sysmon event channel ingestion |
| Windows 11 test VM, Wazuh Windows Agent | Windows Server, AD, domain authentication, additional endpoints |
| Windows Security log collection and analysis (4624/4625/4672) | Custom Wazuh rules, detection-as-code, scheduled hunts |
| Wazuh rule 60122 matching a 4625 test event | Full forensic acquisition, malware triage, Velociraptor |
| Chrony clock troubleshooting and sync recovery | Autonomous scenario generator, SOAR, response automation |

PowerShell and the local `runas` utility were used for lab administration/testing. A mention of a tool does not mean a detection, automation script, or investigation utilizing it is complete.

## Evidence and investigations

| ID | Work | State |
| --- | --- | --- |
| **IR-002** | [Controlled authentication failure and SIEM correlation](investigations/IR-002-authentication.md) | **Milestone 1 complete — benign validation case** |
| IR-001 | Suspicious PowerShell / process lineage | Planned, dependent on Sysmon |
| IR-003 | Persistence | Planned |
| IR-004 | Endpoint forensics | Planned |
| IR-005 | Multi-stage incident | Planned |

The report documents collected facts, interpretation, the clock-drift issue, limitations, and the disposition. The `IR-002` identifier follows the project's existing planned case numbering. It does **not** imply that IR-001 or other cases are completed.

### Detection development status

[Detection planning](detections/README.md) is separate from using a built-in Wazuh rule. **Rule 60122 was supplied by Wazuh; no custom rule has been written or tested yet.** Future goals include process anomalies, login patterns, scheduled tasks, network indicators, and rule-quality testing.

### Threat hunting, malware triage, and ATT&CK

[Threat hunts](threat-hunts/README.md), [malware triage](malware-triage/README.md), and [MITRE ATT&CK tracking](mitre-attack/README.md) remain planned. A default rule's ATT&CK tag is **not proof** that its technique occurred in this case. The authentication lab was an authorized benign test.

## Project structure

```text
enterprise-soc-threat-hunting-lab/
├── README.md
├── architecture/
│   └── asset-inventory.md
├── setup/
│   ├── 01-lab-environment.md
│   ├── 02-wazuh-siem.md
│   ├── 03-windows-agent.md
│   ├── 04-sysmon.md                 # planned
│   ├── 05-active-directory.md       # planned
│   ├── 06-powershell-logging.md     # planned
│   └── 07-velociraptor.md          # planned
├── investigations/
│   ├── README.md
│   └── IR-002-authentication.md    # controlled benign case
├── detections/README.md           # plan, not deployed detections
├── threat-hunts/README.md         # plan, not completed hunts
├── malware-triage/README.md       # future work
├── mitre-attack/README.md         # mapping policy; no confirmed attack mapping
├── scripts/README.md              # planned utilities
├── screenshots/README.md          # evidence/sanitization guidance
└── docs/
    ├── daily-log.md
    ├── learning-journal.md
    ├── lessons-learned.md
    └── interview-notes.md
```


