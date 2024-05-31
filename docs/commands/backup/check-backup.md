# check-backup

|**Command** | **Category** |  **Description**| **Synopsis**|
|------------|--------------|-----------------|----------|
|`check-backup`|Server|Perform maintenance operations on WAL files and backups|`barman check-backup`|

# Syntax
```bash
barman check-backup \<server_name> \<backup_id>
```
# Details

With Barman version 2.5 and higher, you can check that all required WAL files for the consistency of a full backup have been correctly archived by Barman with the `check-backup` command.


!!!important
    This command is automatically invoked by cron and at the end of a backup operation. This means that, under normal circumstances, you should never need to execute it.

In case one or more WAL files from the start to the end of the backup have not been archived yet, Barman will label the backup as `WAITING_FOR_WALS`. The cron command will continue to check that missing WAL files are archived, then label the backup as DONE.

In case the first required WAL file is missing at the end of the backup, such backup will be marked as FAILED. It is therefore important that you verify that WAL archiving (whether via streaming or `archive_command`) is properly working before executing a backup operation - especially when backing up from a standby server.