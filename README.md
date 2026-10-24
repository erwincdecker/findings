# Homelab Findings

An **English-language, public collection** of architectural notes, troubleshooting lessons, quality practices and upstream feature proposals for self-hosted services.

This is neither a live infrastructure inventory nor a deployment source. Operational details and evidence remain private. See the [publication policy](PUBLICATION_POLICY.md).

## Functional areas

| Domain | Topics |
| --- | --- |
| [Architecture](architecture/README.md) | [Capability map](architecture/FUNCTIONAL_MAP.md) and cross-domain boundaries |
| [Compute](infrastructure/compute/README.md) | Virtualization and high availability |
| [Storage](infrastructure/storage/README.md) | Network filesystems, block storage and [Ceph](infrastructure/storage/ceph/README.md) |
| [Backup and recovery](infrastructure/backup-recovery/README.md) | Restore testing and [Proxmox Backup Server](infrastructure/backup-recovery/proxmox-backup-server/README.md) |
| [Networking](networking/ingress/README.md) | Reverse proxy and secure ingress |
| [Security](security/README.md) | Monitoring, authentication and [proxy security](security/npm-sentinel/README.md) |
| [Containers](platform/containers/README.md) | Runtime preservation, updates and health checks |
| [Media](media/README.md) | [Lidarr](media/music/lidarr/README.md), [Jellyfin](media/video/jellyfin/README.md), [Arr applications](media/automation/arr-stack/README.md), [Transmission](media/downloads/transmission/README.md) |
| [Applications](applications/README.md) | [Nextcloud](applications/files/nextcloud/README.md), [Immich](applications/photos/immich/README.md), [Home Assistant](applications/home-automation/home-assistant/README.md), [RustDesk](applications/remote-access/rustdesk/README.md) |
| [Integration](integration/README.md) | Event messaging and [NATS JetStream](integration/nats-jetstream/README.md) |
| [Automation](automation/agents/README.md) | Agent orchestration and [updates](automation/updates/README.md) |
| [Operations](operations/README.md) | Change management, project coordination and documentation |
| [Engineering](engineering/quality/README.md) | Quality assurance and release gates |

## Featured finding

[Lidarr: opt-in automatic partial album imports](media/music/lidarr/README.md) explains a reproducible problem and suggests both a built-in import policy (preferred) and an optional dedicated API, while preserving Lidarr's normal download-client cleanup.

All published findings should distinguish **DOCUMENTED**, **OBSERVED**, **PROPOSED** and **NOT VERIFIED**. A design or source commit is not proof of a deployed and tested outcome.
