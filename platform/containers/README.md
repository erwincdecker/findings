# Container Platforms and Service Operations

**Status: ARCHITECTURAL NOTES.**

The application layer includes Docker containers and Linux containers, each with distinct update, storage and restart semantics.

## Findings
- Container state is more than an image tag: networks, volumes, devices, environment and restart policy must be preserved through upgrades.
- Application-specific health tests matter more than a generic running-process check.
- Stateful updates need consistency-aware backups and rollback plans.
- Maintenance and watchdog processes must coordinate so a watchdog does not undo an intentional update.
- Removing an obsolete orchestration layer should follow workload and state migration, not precede it.

## Review checklist
Image provenance, configuration parity, backup state, restart behavior, dependency availability, health endpoint behavior and rollback rehearsal.

Related: [Update Automation](../../automation/updates/README.md).