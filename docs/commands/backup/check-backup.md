# check-backup

|**Command** | **Category** |  **Description**| **Synopsis**|
|------------|--------------|-----------------|----------|
|`check-backup`|Server|Perform maintenance operations on WAL files and backups|`barman check-backup`|

# Syntax
```bash
barman check-backup \<server_name> \<backup_id>
```
# Details

Check that all required WAL files for the consistency of a full backup have been correctly archived by Barman with the `check-backup` command.

!!!important
    This command is automatically invoked by cron and at the end of a backup operation. This means that, under normal circumstances, you should never need to execute it.

If one or more WAL files from the start to the end of the backup haven't been archived yet, Barman will label the backup as **WAITING_FOR_WALS**. The `cron` command will continue to check that missing WAL files are archived, then label the backup as **DONE**.

If the first required WAL file is missing at the end of the backup, the backup will be marked as **FAILED**. You should verify that WAL archiving *(whether via streaming or `archive_command`)* is working before executing a backup operation - especially when backing up from a standby server.

## Supported versions
 The `check-backup` command is supported for Barman version 2.5 and higher.