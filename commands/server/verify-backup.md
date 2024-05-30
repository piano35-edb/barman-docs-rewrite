# verify-backup

|**Command** | **Category** |  **Description**| **Synopsis**|
|------------|--------------|-----------------|----------|
|`verify-backup`|Server|Use the backup_manifest file from backup and run pg_verifybackup against it|`barman verify-backup`|


# Details

The verify-backup command uses `backup_manifest` file from backup and runs `pg_verifybackup` against it.
```bash
barman verify-backup <server_name> <backup_id>
```
This command will call `pg_verifybackup <path_to_backup_manifest> -n` (available on PG>=13) 

`pg_verifybackup` must be installed on the backup server. For rsync backups, it can be used with the `generate-manifest` command.

Either `backup_id` backup id shortcuts can be used.

You can also use `verify SERVER_NAME BACKUP_ID` as an alias for `verify-backup`.