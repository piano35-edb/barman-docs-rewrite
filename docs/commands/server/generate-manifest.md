# generate-manifest

|**Command** | **Category** |  **Description**| **Synopsis**|
|------------|--------------|-----------------|----------|
|`generate-manifest`|Server|Generate a backup_manifest file from backup_id using backup in barman server|`barman generate-manifest`|

## Syntax

```bash
barman generate-manifest <server_name> <backup_id>
```

# Details

This command is useful when backup is created remotely and `pg_basebackup` is not involved and `backup_manifest` file does not exist in backup. It will generate a `backup_manifest` file from `backup_id` using backup in barman server. If the file already exists, the file generation command will cancel.

Either `backup_id` backup id shortcuts can be used.

## Additional information

This command can also be used as `post_backup` hook script as follows:
```bash
post_backup_script=barman generate-manifest ${BARMAN_SERVER} ${BARMAN_BACKUP_ID}
```