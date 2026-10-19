# Asset inventory — verified lab state

**Last reviewed:** 2026-10-18.

| Host | Platform | Function | Network/identifier | Evidence | State |
| --- | --- | --- | --- | --- | --- |
| `WAZUH-SOC01` | Ubuntu Linux VM | Wazuh Manager, Indexer, Dashboard; Chrony | Host-only `192.168.56.101`; Dashboard over HTTPS | Services/dashboard used successfully; Chrony status screenshots | Online |
| `WIN11-01` | Windows 11 Home `10.0.26200.8037` | Simulated employee workstation; Sysmon installed | Agent ID `001`, Wazuh agent `4.14.7`; displayed agent IP `10.0.2.15`; host-only IP not yet documented | Wazuh Agent 001 Active; Windows Security Event 4625 record 12488; Sysmon Event ID 1 and custom rule 100210 level-7 alert validated | Online |
| `DC01` | Windows Server | Future domain controller / DNS | No verified address | Not provisioned | Planned |

## VirtualBox network topology

- NAT adapter: outbound lab guest networking, including updates.
- Host-only adapter: local connectivity between authorized lab machines and the host.
- Known Wazuh Manager address used in Windows agent configuration: `192.168.56.101`.
- VirtualBox NAT addressing can overlap across separate guests; an agent UI IP is not necessarily its host-only interface address.



