# Safe Update Automation

**Status: ENGINEERING PROCESS; specific deployments are not certified by this page.**

Update automation should preserve runtime configuration, verify application-specific behavior and provide a tested rollback route.

## Gate sequence
VERIFY → SCOPE → STAGE / DRY RUN → BACKUP / ROLLBACK → SOURCE REVIEW → DEPLOY → HEALTHCHECK → COMPARE → CLOSE.

## Findings
- Image updates can silently alter defaults; blindly recreating containers from partial command-line arguments loses important runtime settings.
- Compare volumes, permissions, ports, devices, networks and restart policies before replacing a running service.
- Keep a concurrency lock and maintenance signal to prevent watchdog interference.
- A passed syntax check is not a successful deployment. Live behavior and rollback readiness are separate exit criteria.
- Run update checks only for workloads that are intended to be on.

## Evidence
Store redacted public lessons here. Commit exact source revisions and operational evidence to access-controlled systems.