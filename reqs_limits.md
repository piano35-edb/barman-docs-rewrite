# Requirements

The following requirements apply to Barman:

**Disk space:** Calculate your required disk space based on the size of the cluster, number of WAL files generated per day, frequency of backups, and your retention policies.

**File system:**  XFS, ext4, or NFS. If you use NFS, keep in mind the following:

The `barman_lock_directory` should be on a non-network filesystem.

-   Use version 4 of the NFS protocol.
-   The file system must be mounted using the hard and synchronous options (hard,sync).

**Network Time Protocol (NTP):** Ensure the time is synchronized between the nodes in your Barman cluster, using NTP for example. For more information on configuring NTP, see [Configuring NTP](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/ch-configuring_ntp_using_ntpd) in the Red Hat documentation.

**Secure Shell (SSH):** Ensure the SSH protocol is allowed between the nodes in your Barman cluster. For more information, see [SSH Essentials](https://www.digitalocean.com/community/tutorials/ssh-essentials-working-with-ssh-servers-clients-and-keys) by Digital Ocean.



# Recommendations

The following general recommendations apply to Barman:

For each node in your Barman cluster, use the same:

- Operating system
- PostgreSQL version
- Disk layout
- Hardware architecture

Using rsync/SSH is recommended in all cases where `pg_basebackup` limitations occur (for example, a very large database that can benefit from incremental backup and deduplication).

# Limitations

The following limitations apply to Barman:
