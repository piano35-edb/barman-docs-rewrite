# receive-wal

|**Command** | **Category** |  **Description**| **Synopsis**|
|------------|--------------|-----------------|----------|
|`receive-wal`|Server|Manage the receive-wal process|`barman receive-wal`|


# Details

This command manages the receive-wal process, which uses the streaming protocol to receive WAL files from the PostgreSQL streaming connection.  It starts the stream of transaction logs for a server. The process relies on `pg_receivewal/pg_receivexlog` to receive WAL files from the PostgreSQL servers through the streaming protocol.

`receive-wal` process management
If the command is run without options, a `receive-wal` process will be started. This command is based on the `pg_receivewal` PostgreSQL command.
```bash
barman receive-wal <server_name>
```
!!!NOTE
    The `receive-wal` command is a foreground process.  If the command is run with the `--stop` option, the currently running `receive-wal` process will be stopped.

The `receive-wal` process uses a status file to track last written record of the transaction log. When the status file needs to be cleaned, the `--reset` option can be used.

!!!IMPORTANT
    If you are not using replication slots, you rely on the value of `wal_keep_segments` (or `wal_keep_size` from PostgreSQL version 13.0 onwards). Be aware that under high peaks of workload on the database, the `receive-wal` process might fall behind and go out of sync. As a precautionary measure, Barman currently requires that users manually execute the command with the `--reset` option, to avoid making wrong assumptions.

# Replication slot management
The `receive-wal` process is also useful to create or drop the replication slot needed by Barman for its WAL archiving procedure.  You can use the following options for replication slot management:

# Options

|**Option**|**Description**|
|----------|---------------|
|`--create-slot`|The replication slot named after the `slot_name` configuration parameter will be created on the PostgreSQL server.|
|`--drop-slot`|The previous replication slot will be deleted.|
|`--reset`|Reset the status of `receive-wal`, restarting the streaming from the current WAL file of the server|
|`--stop`|Stop the receive-wal process for the server.|
