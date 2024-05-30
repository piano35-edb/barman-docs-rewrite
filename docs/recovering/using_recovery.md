# Recovering

Barman offers a variety of recovery methods, including remote recovery, point in time recovery, and recovery of compressed backups.  The `recover` command is used to recover a whole server after a backup is executed using the `backup` command.

!!!warning
    Recovery options can be complex.  Ensure you're familiar with all aspects and options of the `recovery` command before using it!  For a list of recovery commands and options, see  [barman recover](../commands/server/recover.md).

## Syntax
```bash
barman@backup\$ barman recover \<server_name\> \<backup_id\> /path/to/recover/dir
```

!!!Important
    Do not issue a recover command using a target data directory where a PostgreSQL instance is running. In that case, remember to stop it before issuing the recovery. This applies also to tablespace directories.

At the end of the execution of the recovery, the selected backup is recovered locally and the destination path contains a data directory ready to be used to start a PostgreSQL instance.

!!!Important
    Running this command as user **barman**, it will become the database superuser.

!!!Important
    Barman does not currently keep track of symbolic links inside PGDATA (except for tablespaces inside pg_tblspc). It's recommended that system administrators to keep track of symbolic links and to add them to the disaster recovery plans/procedures in case they need to be restored in their original location.

The recovery command has several options that modify the command behavior.

## Remote recovery

Add the `--remote-ssh-command \<COMMAND\>` option to the invocation of the recovery command. Doing this will allow Barman to execute the copy on a remote server, using the provided command to connect to the remote host.

!!!NOTE
    It is advisable to use the **postgres** user to perform the recovery on the remote host.

!!!warning
    Do not issue a recover command using a target data directory where a PostgreSQL instance is running. In that case, remember to stop it before issuing the recovery. This applies also to tablespace directories.

### Limitations of the remote recovery

Remote recovery has the following limitations:

-   Barman requires at least 4GB of free space in the system temporary directory unless the `get-wal' command is specified in the `recovery_option` parameter in the Barman configuration.
-   The SSH connection between Barman and the remote host **must** use the public key exchange authentication method.
-   The remote user **must** be able to create the directory structure of the backup in the destination directory.
-   There must be enough free space on the remote server to contain the base backup and the WAL files needed for recovery.

## Tablespace remapping

Barman is able to automatically remap one or more tablespaces using the recover command with the `--tablespace` option. The option accepts a pair of values as arguments using the `NAME:DIRECTORY` format:

-   `NAME` is the identifier of the tablespace
-   `DIRECTORY` is the new destination path for the tablespace

If the destination directory does not exists, Barman will try to create it (assuming you have the required permissions).

## Point in time recovery

Barman wraps PostgreSQL's Point-in-Time Recovery (PITR), allowing you to specify a recovery target, either as a timestamp, as a restore label, or as a transaction ID.

!!!Important
    The earliest PITR for a given backup is the end of the base backup itself. If you want to recover at any point in time between the start and the end of a backup, you must use the previous backup. From Barman 2.3 you can exit recovery when consistency is reached by using `--target-immediate option`.

The recovery target can be specified using one of the following mutually exclusive options:

-   `\--target-time TARGET_TIME`: to specify a timestamp
-   `\--target-xid TARGET_XID`: to specify a transaction ID
-   `\--target-lsn TARGET_LSN`: to specify a Log Sequence Number (LSN) - requires PostgreSQL 10 or higher
-   `\--target-name TARGET_NAME`: to specify a named restore point previously created with the pg_create_restore_point(name) function
-   `\--target-immediate`: recovery ends when a consistent state is reached (that is the end of the base backup process)

!!!Important
    Recovery target via *time*, *XID* and LSN **must be** subsequent to the end of the backup. If you want to recover to a point in time between the start and the end of a backup, you must recover from the previous backup in the catalogue.

You can use the `--exclusive` option to specify whether to stop immediately before or immediately after the recovery target.

Barman allows you to specify a target timeline for recovery using the `--target-tli` option. This can be set to a numeric timeline ID or one of the special values latest (to recover to the most recent timeline in the WAL archive) and current (to recover to the timeline which was current when the backup was taken). If this option is omitted then PostgreSQL versions 12 and above will recover to the latest timeline and PostgreSQL versions below 12 will recover to the current timeline. For more information about timelines, see the PostgreSQL documentation.

Barman 2.4 added support for `--target-action` option, accepting the following values:

-  `shutdown`: once recovery target is reached, PostgreSQL is shut down
-  `pause`: once recovery target is reached, PostgreSQL is started in pause state, allowing users to inspect the instance
-  `promote`: once recovery target is reached, PostgreSQL will exit recovery and is promoted as a master

!!!Important
    By default, no target action is defined (for back compatibility). The `--target-action` option requires a Point In Time Recovery target to be specified.

For more information on the above settings, see the [PostgreSQL documentation on recovery target settings](https://www.postgresql.org/docs/current/static/runtime-config-wal.html#RUNTIME-CONFIG-WAL-RECOVERY-TARGET).

Barman 2.4 also adds the `--standby-mode` option for the recover command which, if specified, properly configures the recovered instance as a standby by creating a `standby.signal` file (from PostgreSQL versions lower than 12), or by adding `standby_mode = on` to the generated recovery configuration.

More information on Postgresql *standby mode* is available in the official documentation:

-   For Postgres 11 and lower versions [in the standby section of PostgreSQL documentation](https://www.postgresql.org/docs/11/standby-settings.html).
-   For PostgreSQL 12 and greater versions [in the replication section of PostgreSQL documentation](https://www.postgresql.org/docs/current/runtime-config-replication.html#RUNTIME-CONFIG-REPLICATION-STANDBY).

!!!Important
    When `--standby-mode` is used during recovery is necessary for the user to modify the configuration of the recovered instance, allowing the recovered server to connect to the primary once the WAL files replication from Barman is successfully completed. If the recovered instance version is 11 or lower this is achieved by adding the `primary_conninfo` parameter to the `recovery.conf` file. If the recovered instance version is 12 or greater, the `primary_conninfo` parameter needs to be added to the `postgresql.conf` file.

## Fetching WALs from the Barman server

The `barman recover` command can optionally configure PostgreSQL to fetch WALs from Barman during recovery. This is enabled by setting the `recovery_options` global/server configuration option to `get-wal`. If `recovery_options` is not set or is empty then Barman will instead copy the WALs required for recovery while executing the `barman recover` command.

The `--get-wal` and `--no-get-wal` options can be used to override the behaviour defined by recovery_options. Use `--get-wal` with barman recover to enable the fetching of WALs from the Barman server.  Use `--no-get-wal` to disable it.

## Recovering compressed backups

If a backup has been compressed using the `backup_compression` option then barman recover is able to uncompress the backup on recovery. This is a multi-step process:

1.  The compressed backup files are copied to a staging directory on the local or remote server using Rsync.
2.  The compressed files are uncompressed to the target directory.
3.  Config files which need special handling by Barman are copied from the recovery destination, analysed or edited as required, and copied back to the recovery destination using Rsync.
4.  The staging directory for the backup is removed.

Because Barman does not know anything about the environment in which it will be deployed it relies on the `recovery_staging_path` option in order to choose a suitable location for the staging directory.

If you are using the `backup_compression` option you *must* therefore either set `recovery_staging_path` in the global/server config *or* use the `--recovery-staging-path` option with the `barman recover` command. If you do neither of these things and attempt to recover a compressed backup then Barman will fail rather than try to guess a suitable location.