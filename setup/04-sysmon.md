# 04 — Sysmon endpoint telemetry

**Status: PLANNED — not installed or verified as of 2026-10-15.**

## Purpose

Enhance process and network visibility on `WIN11-01` for event-driven SOC investigations and blind, controlled scenarios.

## Definition of done

- [ ] Retrieve Sysmon from Microsoft's official Sysinternals distribution.
- [ ] Inspect and document the chosen configuration before installing it in the personal Windows VM.
- [ ] Verify Sysmon service and `Microsoft-Windows-Sysmon/Operational` event channel.
- [ ] Generate a harmless process action and locate Event ID **1** (process creation).
- [ ] Inspect actual process GUID, image, parent image, command line, user and timestamp.
- [ ] Configure Wazuh eventchannel forwarding for Sysmon and confirm a matching event across both systems.
- [ ] Check whether Event IDs **3** (network) and **22** (DNS) are enabled before claiming those detections.
- [ ] Capture sanitized evidence and publish an independent short process investigation.

## Investigation considerations

A PowerShell process, long command line, or unusual parent does not alone establish malicious intent. Look at the execution context, user, authorized purpose, child processes and follow-on network/file activity.

