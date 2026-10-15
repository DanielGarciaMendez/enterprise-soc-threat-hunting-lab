# 03 — Windows agent setup and validation

**Status: Installed, Active, and forwarding Windows Security authentication alerts as of 2026-10-15.**

## Endpoint

- Computer: `WIN11-01`
- OS: Windows 11 Home, `10.0.26200.8037`
- Wazuh Windows Agent: `4.14.7`
- Wazuh Agent ID: `001`
- Manager address configured: `192.168.56.101`
- Dashboard agent state: **Active**


## Telemetry validation

- Local Windows Security events examined: 4624, 4625, 4672.
- Controlled failed logon for `soc-test` produced 4625 with SubStatus `0xC000006A`.
- Wazuh displayed an alert matching Windows EventRecordID `12488`, rule 60122.
- Verified source address of this failed login: `::1` IPv6 loopback.


See [Authentication correlation](../investigations/IR-002-authentication.md).
