# Local backups

!!!danger
    This feature is not recommended for production usage, as Barman and PostgreSQL reside on the same server and are part of the same single point of failure. 
    
    Some EnterpriseDB customers have requested to add support for local backup to Barman to be used under specific circumstances and, most importantly, under the 24/7 production service delivered by the company. 
    
    Using this feature currently requires installation from sources, or to customize the environment for the postgres user in terms of permissions as well as logging and cron configurations.

Under certain circumstances, Barman can be installed on the same server where the PostgreSQL instance resides, with backup data stored on a separate volume from `PGDATA` and, where applicable, tablespaces. Usually, these volumes reside on network storage appliances, with filesystems like NFS.

!!!warning
    This architecture is not endorsed by EnterpriseDB. For an enhanced business continuity experience of PostgreSQL, with better results in terms of RPO and RTO, it's recommended to use a shared-nothing architecture with a remote installation of Barman, capable of acting like a witness server for replication and monitoring purposes.

The only requirement for local backup is that Barman runs with the same user as the PostgreSQL server, which is normally **postgres**. Given that the community packages install Barman under the **barman** user, this use case requires manual installation procedures that include:

-   cron configurations
-   log configurations, including logrotate

To use local backup for a given server in Barman, set `backup_method` to `local-rsync`. The feature is essentially identical to its rsync equivalent, which relies on SSH instead and operates remotely. With `local-rsync`, file system copy is performed issuing rsync commands locally.  For this reason, it's required that Barman runs with the same user as PostgreSQL.

For example, a portion of the configuration for local backup for a server named **local-pg13**:
```bash
**[local-pg13]**
description = "Local PostgreSQL 13"
backup_method = local-rsync
```