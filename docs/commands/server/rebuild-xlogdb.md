# rebuild-xlogdb

|**Command** | **Category** |  **Description**| **Synopsis**|
|------------|--------------|-----------------|----------|
|`rebuild-xlogdb`|Server|Regenerate the content of the WAL archive|`barman rebuild-xlogdb`|


# Details

At any time, you can regenerate the content of the WAL archive for a specific server (or every server, using the `all` shortcut). The WAL archive is contained in the `xlog.db` file and every server managed by Barman has its own copy.

The `xlog.db` file can be rebuilt with the `rebuild-xlogdb` command. This will scan all the archived WAL files and regenerate the metadata for the archive.

# Syntax
```bash
barman rebuild-xlogdb <server_name>
```
!!!tip
    You can rebuild of WAL file metadata for all servers using `all`.