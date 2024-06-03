# Requirements

## Disk space

The most **critical** requirement for a Barman server is the amount of disk space available. You should plan the required disk space based on:
- The size of the cluster
- Number of WAL files generated per day
- Frequency of backups
- Backup retention policies

## Filesystem

Barman developers regularly test Barman with **XFS** and **ext4**. 

### Using with NFS

Like [PostgreSQL](https://www.postgresql.org/docs/current/creating-cluster.html#CREATING-CLUSTER-FILESYSTEM), Barman does nothing special for NFS. 

The following requirements apply for safely using Barman with NFS:

-   The `barman_lock_directory` should be on a non-network filesystem.
-   Use version 4 of the NFS protocol.
-   The file system must be mounted using the hard and synchronous options (hard,sync).