# replication-status

|**Command** | **Category** |  **Description**| **Synopsis**|
|------------|--------------|-----------------|----------|
|`replication-status`|Server|Reports the status of any streaming client currently attached to the PostgreSQL server|`barman replication-status`|

## Syntax

```bash
barman replication-status <server_name>
```

## Details

The `replication-status` command reports the status of any streaming client currently attached to the PostgreSQL server, including the `receive-wal` process of your Barman server (if configured).

!!!tip
    You can request a full status report of the replica for all your servers using `all` as the server name.
    
    For machine-readable output, use the `--minimal` option.

## Options

|**Option**|**Description**|**Default**|**Allowed Values**|
|----------|---------------|----------|---------------|
|`--minimal`|Machine readable output|False||
|`--target TARGET_TYPE`||all|`hot-standby`: lists only hot standby servers
||||`wal-streamer`: lists only WAL streaming clients, such as `pg_receivewal`
||||`all`: any streaming client|
|`--source SOURCE_TYPE`||`backup-host`|`backup-host`: list clients using the backup conninfo for a server
||||`wal-host`: list clients using the WAL streaming conninfo for a server|