# 01 — Lab environment and network design

**Status: Implemented for two VMs; identity expansion planned. Updated 2026-10-15.**

## Scope

- Physical Windows host with Oracle VirtualBox.
- `WAZUH-SOC01`: Ubuntu-based all-in-one Wazuh Manager/Indexer/Dashboard VM.
- `WIN11-01`: Windows 11 Home test VM with Wazuh Agent.
- `DC01`, multiple endpoints, and Active Directory are future objectives.

## Networking as verified

Wazuh Dashboard/Manager host-only address: `192.168.56.101`. On the Linux guest, the host-only interface was observed as `192.168.56.101`;

## Completed environment checks

- Wazuh web dashboard was reachable from the physical host at `https://192.168.56.101`.
- Windows Wazuh Agent ID `001` was visible as **Active** in the dashboard.
- Both local and indexed copies of Windows Security record 12488 were correlated.

