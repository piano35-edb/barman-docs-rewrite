# Concurrent backups

Concurrent backup is a technology that uses the streaming replication protocol, for example, with a tool like `pg_basebackup`.

Normally, during backup operations, Barman uses PostgreSQL native functions `pg_start_backup` and `pg_stop_backup` for concurrent backup.  This is the recommended way of taking backups for PostgreSQL 9.6 and above (though note the functions have been renamed to `pg_backup_start` and `pg_backup_stop` in the PostgreSQL 15 beta).

As well as being the recommended backup approach, concurrent backup also allows the following architecture scenario with Barman: **backup from a standby server**, using rsync.

By default, `backup_options` is set to `concurrent_backup`. If exclusive backup is required for PostgreSQL servers older than version 15 then users should set `backup_options` to `exclusive_backup`.

When `backup_options` is set to `concurrent_backup`, Barman activates the *concurrent backup mode* for a server and follows two rules:

-   `ssh_command` must point to the destination Postgres server
-   `conninfo` must point to a database on the destination Postgres database.

!!!IMPORTANT
    In case of a concurrent backup, currently Barman cannot determine whether the closing WAL file of a full backup has actually been shipped - opposite of an exclusive backup where PostgreSQL itself makes sure that the WAL file is correctly archived. Be aware that the full backup cannot be considered consistent until that WAL file has been received and archived by Barman. Barman 2.5 introduces a new state, called `WAITING_FOR_WALS`, which is managed by the `check-backup` command (part of the ordinary maintenance job performed by the cron command). From Barman 2.10, you can use the `--wait` option with `barman backup` command.

## Concurrent backup of a standby

If backing up a standby, the following configuration options should point to the standby server:

-   `conninfo`
-   `streaming_conninfo` (when using `backup_method = postgres` or `streaming_archiver = on`)
-   `ssh_command` (when using `backup_method = rsync`)

The following config option should point to the primary server:

-   `primary_conninfo`

Barman will use `primary_conninfo` to switch to a new WAL on the primary so that the concurrent backup against the standby can complete without having to wait for a WAL switch to occur naturally.

!!!NOTE
    It is especially important that `primary_conninfo` is set if the standby is to be backed up when there is little or no write traffic on the primary.

As of Barman 3.8.0, If `primary_conninfo` is set, is possible to add for a server a `primary_checkpoint_timeout` option, which is the maximum time (in seconds) for Barman to wait for a new WAL file to be produced before forcing the execution of a checkpoint on the primary. The `primary_checkpoint_timeout` option should be set to an amount of seconds greater of the value of the `archive_timeout` option set on the primary server.

If `primary_conninfo` is not set then the backup will still run however it will wait at the stop backup stage until the current WAL segment on the primary is newer than the latest WAL required by the backup.

Barman currently requires that WAL files and backup data come from the same PostgreSQL server. In the case that the standby is promoted to primary the backups and WALs will continue to be valid however you may wish to update the Barman configuration so that it uses the new standby for taking backups and receiving WALs.

WALs can be obtained from the standby using either WAL streaming or WAL archiving. To use WAL streaming follow the instructions in the [WAL streaming](https://docs.pgbarman.org/release/3.10.0/#wal-streaming) section.

To use WAL archiving from the standby follow the instructions in the [WAL archiving via archive_command](https://docs.pgbarman.org/release/3.10.0/#wal-archiving-via-archive_command) section *and additionally*, set `archive_mode = always` in the PostgreSQL config on the standby server.

!!!NOTE
    With PostgreSQL 10 and earlier Barman cannot handle WAL streaming and WAL archiving being enabled at the same time on a standby. You must therefore disable WAL archiving if using WAL streaming and vice versa. This is because it is possible for WALs produced by PostgreSQL 10 and earlier to be logically equivalent but differ at the binary level, causing Barman to fail to detect that two WALs are identical.