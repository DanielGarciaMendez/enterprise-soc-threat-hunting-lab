# MITRE ATT&CK evidence mapping

**Status: No attack technique has been independently confirmed in the lab.**

Some built-in Wazuh alerts contain rule-supplied ATT&CK tags. Those are detector metadata and do not prove the technique actually occurred. The completed authentication case involved intentionally incorrect credentials entered by the owner; it was **benign**, not confirmed credential attack activity.

## Mapping policy

Only map a tactic/technique when observed behavior and independent corroboration support it; keep hypotheses marked as hypotheses. For each future mapping include technique, exact source records, environment, observed behavior, alternatives, and confidence. Avoid claiming wide ATT&CK coverage merely from generating common Windows events.
