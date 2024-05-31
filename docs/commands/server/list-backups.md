# list-backups

|**Command** | **Category** |  **Description**| **Synopsis**|
|------------|--------------|-----------------|----------|
|`list-backups`|Server|List the catalog of available backups for a given server|`barman list-backups`|

## Syntax

```bash
barman list-backups <server_name>
```

## Details

Lists the catalog of available backups for a given server.

The returned output will show the backup ID.  For example, the backup ID returned here is *20111104T102647*.
```bash
servername 20111104T102647 - Fri Nov  4 10:26:48 2011 - Size: 17.0 MiB - WAL Size: 100 B
```
!!!TIP
    You can request a full list of the backups of all servers using all as the server name.  For machine-readable output, use the `--minimal` option.