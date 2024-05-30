# keep

|**Command** | **Category** |  **Description**| **Synopsis**|
|------------|--------------|-----------------|----------|
|`keep`|Server|Flag the specified backup as an archival backup|`barman keep \<server_name> \<backup_id>`|


# Details

`keep` flags the specified backup as an archival backup which should be kept forever, regardless of any retention policies in effect. 

If you have a backup which you wish to keep beyond the retention policy of the server then you can make it an archival backup with:

`barman keep \<server_name> \<backup_id> [--target TARGET, --status, --release]`

The allowed values for `TARGET` are:

`full`: The backup can always be used to recover to the latest point in time. To achieve this, Barman will retain all WALs needed to ensure consistency of the backup and all subsequent WALs.
`standalone`: The backup can only be used to recover the server to its state at the time the backup was taken. Barman will only retain the WALs needed to ensure consistency of the backup.

If the `--status` option is provided then Barman will report the archival status of the backup. This will either be the recovery target of full or standalone for archival backups or nokeep for backups which have not been flagged as archival.

If the `--release` option is provided then Barman will release the keep flag from this backup. This will remove its archival status and make it available for deletion, either directly or by retention policy.

Once a backup has been flagged as an archival backup, the behaviour of Barman will change as follows:

- Attempts to delete that backup by ID using barman delete will fail.
- Retention policies will never consider that backup as OBSOLETE and therefore barman cron will never delete that backup.
- The WALs required by that backup will be retained forever. If the specified recovery target is full then all subsequent WALs will also be retained.  This can be reverted by removing the keep flag with `barman keep \<server_name> \<backup_id> --release`.

!!!WARNING
    Once a standalone archival backup is not required by the retention policy of a server `barman cron` will remove the WALs between that backup and the `begin_wal` value of the next most recent backup. This means that while it is safe to change the target from full to standalone, it is not safe to change the target from standalone to full because there is no guarantee the necessary WALs for a recovery to the latest point in time will still be available.