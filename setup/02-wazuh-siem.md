# 02 — Wazuh SIEM deployment and health verification

**Status: Operational and verified as of 2026-10-15.**

## Implemented services

- Wazuh Manager — receives agent telemetry and evaluates Wazuh rules.
- Wazuh Indexer — indexes alert data for search and analysis.
- Wazuh Dashboard — interface for agents, Threat Hunting, and searchable alerts.

The three components are running on the Ubuntu VM `WAZUH-SOC01`. The dashboard was reached at `https://192.168.56.101`.

## Verified end-to-end test

The Windows agent `WIN11-01` (ID `001`) was Active, and Wazuh showed a rule-60122 level-5 alert originating from the Windows Security Event ID 4625 test. Wazuh's embedded Windows event record ID was **12488**, matching the source event in local Windows Event Viewer. See [IR-002](../investigations/IR-002-authentication.md).


## Health checklist for future sessions

1. Inspect Wazuh service status and Dashboard accessibility.
2. Confirm agent ID and freshness of the `WIN11-01` keep-alive.
3. Generate one authorized benign log event and verify its **Windows event ID and EventRecordID**, not merely the alert count.
4. Compare endpoint `systemTime` and SIEM `timestamp` in UTC.
5. If events are absent, check configured Windows event channels and time-range filters before reinstalling anything.


