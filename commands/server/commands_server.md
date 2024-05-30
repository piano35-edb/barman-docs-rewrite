# Server commands

Server commands work directly on a PostgreSQL server or on its area in Barman.  They are useful to check its status, perform maintenance operations, take backups, and manage the WAL archive.

**Usage**

All commands should be prefaced with `barman`.  For example `barman check`

# Server command index

The following list includes the server commands.

|**Command** | **Category** |  **Description**|
|------------|--------------|-----------------|
|`archive-wal`|Server|Execute maintenance operations on WAL files for a given server|
|`backup`|Server|Take a full backup (base backup) of the given servers|
|`check`|Server|Check the connection to a given server and its configuration|
|`config-update`|Server|Create or update configuration of servers and models|
|`config-switch`|Server|Apply a set of configuration overrides defined through a model to a Barman server|
|`delete`|Server|Delete a given backup|
|`generate-manifest`|Server|Generate a `backup_manifest` file from `backup_id` using backup in barman server|
|`get-wal`|Server|Request any xlog file from its WAL archive|
|`keep`|Server|Flag the specified backup as an archival backup|
|`list-backups`|Server|List the catalog of available backups for a given server|
|`list-files`|Server|List all the files in a particular backup|
|`list-servers`|Server|Show all the configured servers, and their descriptions|
|`lock-directory-cleanup`|Server|Automatically cleans up the `barman_lock_directory` from unused lock files|
|`put-wal`|Server|Receive a WAL file from a remote server and securely store it into the `SERVER_NAME` incoming directory|
|`rebuild-xlogdb`|Server|Regenerate the content of the WAL archive|
|`receive-wal`|Server|Manage the `receive-wal` process|
|`recover`|Server|Recover a backup in a given directory|
|`replication-status`|Server|Reports the status of any streaming client currently attached to the PostgreSQL server|
|`show-servers`|Server|Show the configuration parameters for a given server|
|`status`|Server|Show live information and status of a PostgreSQL server or all servers|
|`switch-wal`|Server|Make the PostgreSQL server switch to another transaction log file (WAL)|
|`sync-info`|Server|Collect information regarding the current status of a Barman server|
|`verify-backup`|Server|Use the `backup_manifest` file from backup and run `pg_verifybackup` against it|