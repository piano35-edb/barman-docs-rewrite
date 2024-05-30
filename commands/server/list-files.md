# list-files

|**Command** | **Category** |  **Description**| **Synopsis**|
|------------|--------------|-----------------|----------|
|`list-files`|Server|List all the files in a particular backup|`barman list-files`|

# Syntax

```bash
list-files [OPTIONS] SERVER_NAME BACKUP_ID
```
# Details

List all the files in a particular backup, identified by the server name and the backup ID.  The files include base backup and required WAL files.

# Options
|**Argument**|**Description**|**Default**|
|-------------|--------------|-----------|
|`--target TARGET_TYPE`|Choose the content of the list for a given backup|standalone|

The allowed values for `TARGET_TYPE` are:

- `data`: lists just the data files;
- `standalone`: lists the base backup files, including required WAL files;
- `wal`: lists all the WAL files between the start of the base backup and the end of the log / the start of the following base backup (depending on whether the specified base backup is the most recent one available);
- `full`: same as data + wal.


!!!IMPORTANT
    The `list-files` command facilitates interaction with external tools, and can therefore be extremely useful to integrate Barman into your archiving procedures.