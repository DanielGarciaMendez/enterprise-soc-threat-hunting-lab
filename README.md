# Enterprise SOC, Threat Hunting & Incident Response Lab

> **Status: In progress**
> Personal VirtualBox lab for learning centralized security monitoring, endpoint investigations, detection engineering, and incident response.

## Project overview

I am building and operating a small enterprise-style security lab from the perspective of a SOC analyst. The focus is not just installing tools: it is understanding what telemetry proves, validating detections, correlating sources, investigating competing explanations, and documenting defensible conclusions.

**Investigation workflow:** Telemetry → Detection / hypothesis → Validation → Context and correlation → Assessment → Approved response if warranted → Documentation → Detection improvement.

## Milestone tracker

| Milestone | State | Evidence / next requirement |
| --- | --- | --- |
| 0 — Build Wazuh server and dashboard | **Done** | Wazuh Manager, Indexer, and Dashboard operating on `WAZUH-SOC01` |
| 1 — Windows authentication telemetry and SIEM correlation | **Done, documented** | `WIN11-01` agent Active; real Event 4625 correlated to Wazuh rule 60122 using Windows Record ID **12488**; [investigation](investigations/IR-002-authentication.md) |
| 1b — Redacted screenshots and formal export | **Not yet** | Collect/select sanitized artifacts and verify before publishing |
| 2 — Sysmon process telemetry | **Implemented and validated** | Event ID 1, Notepad GUID lineage, Wazuh forwarding; [IR-001](investigations/IR-001-sysmon-process-lineage.md) |
| 3 — Blind SOC investigations | **Planned** | Safe benign vs adversary-like simulations, analyst unaware of scenario, documented disposition |
| 4 — Domain / identity infrastructure | **Planned** | Only after a DC is actually deployed and verified |
| 5 — Custom detection rules | **Implemented and validated** | Rule 100210; benign test produced a level-7 alert; broader testing pending |
| 5a — Threat hunts | **Planned** | Document hypotheses, queries, evidence, and outcomes |

### Milestone 1 — What was actually accomplished 

- Brought up an all-in-one Wazuh deployment with Manager, Indexer, and Dashboard on Ubuntu.
- Connected a Windows 11 test VM with Wazuh Windows Agent **v4.14.7**. Its agent ID **001** was verified as **Active** in the dashboard.
- Inspected real Windows Security Events **4624** (successful logon), **4625** (failed logon), and **4672** (special privileges assigned at logon).
- Created a disposable local account, `soc-test`, and used an authorized local `runas` test to generate a failed authentication followed by an authorized successful authentication.
- Found a 4625 event in both Event Viewer and Wazuh; confirmed **the same underlying Windows record** by matching Event ID **4625** and **EventRecordID 12488** on `WIN11-01`. Wazuh's matched alert was rule **60122**, level **5**.
- Identified a central-alert timestamp discrepancy, diagnosed the Ubuntu clock using Chrony, and corrected a clock offset of approximately **8 h 44 min**. After the correction, Ubuntu reported **System clock synchronized: yes**.
- Assessed the known test as **benign, authorized lab activity**. No attacker, compromise, malware execution, or production incident was observed.

Read the [full authentication case](investigations/IR-002-authentication.md) and [daily log](docs/daily-log.md). A rule-generated SIEM alert is not automatically an analyst ticket, declared incident, or indication of a successful attack.

### Milestone 2 — Sysmon Process Monitoring and Detection Engineering

- Installed Sysmon on WIN11-01 and validated Event ID **1** process-create telemetry.
- Ran a controlled Notepad experiment; inspected process and parent context and correlated full ProcessGuid/ParentProcessGuid values.
- Distinguished SYSTEM in the Event Viewer header from process identity in EventData.
- Configured Wazuh agent collection of Microsoft-Windows-Sysmon/Operational.
- Created custom rule **100210**, level **7**, in **SysmonPowershellTest.xml**.
- Ran a benign PowerShell ExecutionPolicy Bypass command that printed SOC-LAB-TEST; Threat Hunting displayed **LAB: PowerShell execution policy bypass**.

This demonstrates detection engineering and SIEM validation. Event IDs **3** and **22** remain future work.

Read [IR-001](investigations/IR-001-sysmon-process-lineage.md), [Sysmon setup](setup/04-sysmon.md), and [DET-004](detections/README.md). Evidence: [rule definition](screenshots/BeningTestSysmon.png), [Notepad parent context](screenshots/ChildProcessNotepad.png), [local PowerShell event](screenshots/LocalSysmonWorking.png), and [Wazuh alert](screenshots/SysmonSendingDetectiontoWazuh.png). See the [screenshot inventory](screenshots/README.md) for evidence boundaries.

## Architecture

```text
                          Physical Windows Host
                             Oracle VirtualBox
                        /                       \\
            WAZUH-SOC01 (Ubuntu VM)             WIN11-01 (Windows 11 VM)
            - Wazuh Manager                    - Wazuh Agent v4.14.7 (001)
            - Wazuh Indexer                    - Windows Security audit logs
            - Wazuh Dashboard                  - Sysmon (Event ID 1 validated)
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

**Validated Sysmon detection flow:**

Controlled PowerShell test → Sysmon Event ID 1 on WIN11-01 → Wazuh Agent 001 → Wazuh Manager custom rule 100210 → Wazuh alerts index → Wazuh Dashboard / Threat Hunting.

The diagram simplifies Wazuh ingestion/indexing; not all raw events generate alerts. Windows Security and Sysmon Event ID 1 have validated detection paths. AD/DC remains future work.

### Network notes

| Asset | Role / version | Verified address or identifier | Status |
| --- | --- | --- | --- |
| `WAZUH-SOC01` | Ubuntu VM, Wazuh Manager/Indexer/Dashboard | Host-only **`192.168.56.101`**, HTTPS Dashboard reachable | Running |
| `WIN11-01` | Windows 11 Home, build | Wazuh **Agent 001**, Agent v**4.14.7**; dashboard displayed IP **`10.0.2.15`** | Agent **Active** |
| `DC01` | Future Windows Server / AD / DNS | **No verified address** | Planned |



See [asset inventory](architecture/asset-inventory.md), [environment](setup/01-lab-environment.md), and [Wazuh setup](setup/02-wazuh-siem.md).

## Current technology stack

| Implemented and demonstrated | Planned / not yet verified |
| --- | --- |
| VirtualBox, Ubuntu-based Wazuh VM, Manager, Indexer, Dashboard | Sysmon Event IDs 3 and 22; not independently validated |
| Windows 11 test VM, Wazuh Windows Agent | Windows Server, AD, domain authentication, additional endpoints |
| Windows Security log analysis (4624/4625/4672); Sysmon Event ID 1 and forwarding | Broader detection-as-code workflow and scheduled hunts |
| Built-in rule 60122; custom rule 100210 with benign level-7 alert validation | Full forensic acquisition, malware triage, Velociraptor |


## Evidence and investigations

| ID | Work | State |
| --- | --- | --- |
| **IR-002** | [Controlled authentication failure and SIEM correlation](investigations/IR-002-authentication.md) | **Milestone 1 complete — benign validation case** |
| **IR-001** | [Sysmon process lineage and controlled PowerShell detection](investigations/IR-001-sysmon-process-lineage.md) | **Complete — benign, authorized test activity** |
| IR-003 | Persistence | Planned |
| IR-004 | Endpoint forensics | Planned |
| IR-005 | Multi-stage incident | Planned |


### Detection development status

[DET-004](detections/README.md) documents implemented custom rule **100210** and its successful benign positive test. **Rule 60122 remains a built-in Wazuh rule!!**

### Threat hunting, malware triage, and ATT&CK

[Threat hunts](threat-hunts/README.md), [malware triage](malware-triage/README.md), and [MITRE ATT&CK tracking](mitre-attack/README.md) remain planned. 
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
│   ├── 04-sysmon.md                 # implemented / validated
│   ├── 05-active-directory.md       # planned
│   ├── 06-powershell-logging.md     # planned
│   └── 07-velociraptor.md          # planned
├── investigations/
│   ├── README.md
│   ├── IR-001-sysmon-process-lineage.md # controlled benign case
│   └── IR-002-authentication.md    # controlled benign case
├── detections/README.md           # DET-004 implemented; backlog
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
