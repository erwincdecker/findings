# Virtualization and Availability

**Status: ARCHITECTURAL FINDING. No current runtime health asserted.**

The compute layer uses a hypervisor-cluster model with virtual machines and Linux containers. It supports workload isolation, resource right-sizing, placement decisions and recovery of services.

## Findings
- High availability improves service recovery after node failure, but does not replace backup or restoration.
- Quorum and placement policy are necessary for reliable failover; capacity should be measured under a node-loss scenario.
- Workload migration is only complete after validating persistent storage, ownership, application health and backup coverage.
- Swap pressure, storage latency and shared network dependencies can dominate nominal CPU measurements.

## Proposed validation flow
Inventory resources privately, capture a baseline, simulate failover, verify dependencies, stage controlled migration, validate application functions and assess rollback readiness.

Related: [Storage](../storage/README.md), [Recovery](../backup-recovery/README.md).