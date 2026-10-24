# Persistent Storage and Data Placement

**Status: DOCUMENTED PRACTICES. No specific live storage configuration published.**

Relevant technologies include local solid-state storage, network file shares (SMB/NFS), network block storage (iSCSI/LVM), and distributed block volumes such as Ceph/RBD.

## Engineering findings
- Temporary network loss can leave guest filesystems read-only even after the block-storage session reconnects.
- Mount success does not necessarily prove that the intended remote dataset is healthy and writable.
- Distributed replicas improve certain failure scenarios but require storage headroom, quorum and rebuild capacity.
- For application performance, measure latency, small-block operations and reconnection behavior, not just sequential throughput.

## Safe change sequence
Observe the current storage path, estimate capacity and failure-domain requirements, run read-only checks, take a recoverable backup, stage migration, check the actual application and perform a restore test.

No real device paths, initiator IDs, mount credentials or host addresses are included.