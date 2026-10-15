# Investigation automation — future work

**Status: No completed or tested automation scripts committed for this milestone.**

Planned utilities:

- `Triage-Endpoint.ps1`: authorized, read-only collection of process, service, scheduled-task and selected log metadata.
- `log_summary.py`: generate aggregate counts/timelines from **redacted** exported lab telemetry.
- Optional local scenario launcher for **safe, blind** test activity; review source and scope before execution. Do not run against external systems.

Before promoting a script from planned to implemented, document its inputs, permissions, observed sample output, error handling, and side effects. Do not publish secrets or unredacted event exports.
