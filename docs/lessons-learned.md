# Lessons learned / troubleshooting retrospective

**Reviewed:** 2026-10-15. Findings describe an owned, isolated lab and must not be presented as production incident response.

## 1. Agent installation can succeed while configuration is invalid

**Symptom:** Windows Wazuh service failed to stay operational.  
**Observed cause:** Manager `<address>` was `0.0.0.0`, and agent logs indicated an invalid server address.  
**Corrective action:** Set Manager address to `192.168.56.101` in agent configuration; confirm service and Dashboard agent state.  
**Verified outcome:** `WIN11-01` agent ID `001` Active.

**Lesson:** Check configuration, logs and server route before reinstalling.

## 2. A connected agent is not proof that a particular event reached the SIEM

**Symptom:** Initially only endpoint-agent connectivity was proven.  
**Test:** Generate a controlled Windows 4625 and locate the original EventRecordID in Event Viewer.  
**Verified outcome:** Wazuh rule 60122 contained the same original Windows EventRecordID `12488`.

**Lesson:** Confirm provenance with unique source-record identifiers, not generic alert counts.

## 3. Time synchronization is essential to incident timelines

**Symptom:** Wazuh alert timestamp differed from embedded Windows event UTC by about 8 h 47 min.  
**Investigation:** Ubuntu UTC timezone was correct, but clock was not synchronized. The active time client was Chrony, so `timedatectl timesync-status` was not available. `chronyc tracking` showed roughly 31,459 seconds slow.  
**Corrective action:** `sudo chronyc makestep`.  
**Verified outcome:** `200 OK`; `timedatectl` subsequently reported synchronized clock and active NTP.

**Lesson:** Validate both event generation time and ingestion/alert time. Time-zone conversion alone does not explain drift. Historical records remain historical.

## 4. The rule match does not prove an attack

**Observed:** Wazuh matched an authorized local password failure to Level-5 rule 60122.  
**Conclusion:** This was a benign test, not a confirmed breach. The low-level alert did not demonstrate a production ticket or analyst page.  
**Lesson:** Separate detection accuracy from maliciousness, investigation urgency, and incident classification.

## 5. Avoid overstating training work

Real work completed: Wazuh deployment, Windows event collection, rule match, event-ID correlation, time troubleshooting, and basic triage. **Still pending:** Sysmon, blind cases, custom rules, Active Directory, threat hunts, and actual malware forensics.
