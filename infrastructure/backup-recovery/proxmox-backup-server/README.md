# Proxmox Backup Server

**Status: FUNCTIONAL PRACTICE, NOT A LIVE BACKUP REPORT.**

Proxmox Backup Server can provide deduplicated backup storage for virtual machines and containers. Backups must be evaluated for freshness, datastore integrity, consistency, retention, and independent restoration.

**Finding:** storage-session failure can affect a backup appliance just as it affects production workloads. Recovery checks must include underlying filesystem health and datastore access. A completed scheduled backup does not prove a valid restore.

All datastore identifiers and actual backup destinations are intentionally private.