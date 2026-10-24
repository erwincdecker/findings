# User Applications and Personal Services

**Status: FUNCTIONAL OVERVIEW, not live service configuration.**

Application categories in this lab include document synchronization and sharing, personal photo management, home automation, self-hosted remote support and supporting utility services.

## Practical findings
- **File sync and sharing:** separate account permissions from public-link permissions, and verify anonymous access in a private browsing session.
- **Photo management:** filename normalization and metadata corrections should use dry-run collision checks and immutable originals or backups.
- **Home automation:** local availability, external integration dependencies and device update provenance must be considered separately.
- **Remote support:** authenticate access and verify that installer distribution does not accidentally expose private content.

## Release quality
Each change needs a defined user-visible outcome, minimal write scope, backup, health test and a documented way to reverse it.

No personal files, account data, application endpoints or device inventory are published.