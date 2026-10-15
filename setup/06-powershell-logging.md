# 06 — PowerShell logging

**Status: PLANNED — no PowerShell 4104 collection has been verified.**

## Goal

Collect and interpret script-block logging on the isolated Windows lab VM alongside Sysmon process lineage.

## Definition of done

- [ ] Confirm the existing audit and logging policy on `WIN11-01`.
- [ ] Enable suitable local script-block logging only after reviewing privacy, volume and test impact.
- [ ] Execute an intentionally benign, non-destructive script.
- [ ] Find the corresponding Event **4104**, if configured.
- [ ] Correlate PowerShell log content, user, timestamp and Sysmon process creation.
- [ ] Verify selected events in Wazuh and document any visibility limitations.

