The most critical requirement for a Barman server is the amount of disk space available. You are recommended to plan the required disk space based on the size of the cluster, number of WAL files generated per day, frequency of backups, and retention policies.

Barman developers regularly test Barman with XFS and ext4. Like [PostgreSQL](https://www.postgresql.org/docs/current/creating-cluster.html#CREATING-CLUSTER-FILESYSTEM), Barman does nothing special for NFS. The following points are required for safely using Barman with NFS:

-   The barman_lock_directory should be on a non-network filesystem.
-   Use version 4 of the NFS protocol.
-   The file system must be mounted using the hard and synchronous options (hard,sync).