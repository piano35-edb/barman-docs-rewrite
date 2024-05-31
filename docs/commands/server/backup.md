# backup

|**Command** | **Category** |  **Description**| **Synopsis**|
|------------|--------------|-----------------|----------|
|`backup`|Server|Perform maintenance operations on WAL files and backups|`barman backup`|


## Details

The backup command takes a full backup (base backup) of the given servers. It has several options that let you override the corresponding configuration parameter for the new backup. 

You can perform a full backup for a given server with:
```bash
barman backup <server_name>
```

!!!TIPs
    You can use `barman backup all` to sequentially backup all your configured servers.

    You can use `barman backup` **server_1_name** **server_2_name** to sequentially backup both both servers.

Barman 2.10 introduced the `-w/--wait` option for the backup command. When set, Barman temporarily saves the state of the backup to `WAITING_FOR_WALS`, then waits for all the required WAL files to be archived before setting the state to DONE and proceeding with post-backup hook scripts. 

If the `--wait-timeout` option is provided, Barman will stop waiting for WAL files after the specified number of seconds, and the state will remain in `WAITING_FOR_WALS`.  The `cron` command will continue to check that missing WAL files are archived, then label the backup as done.

## Arguments

|**Argument**|**Description**|**Default**|
|-------------|--------------|-----------|
|`delete SERVER_NAME BACKUP_ID`|Delete the specified backup. Backup ID shortcuts can be used||
|`--name`|A friendly name for this backup which can be used in place of the backup ID in barman commands.||
|`--immediate-checkpoint`|Forces the initial checkpoint to be done as quickly as possible. Overrides value of the parameter `immediate_checkpoint`, if present in the configuration file.||
|`--no-immediate-checkpoint`|Forces to wait for the checkpoint. Overrides value of the parameter immediate_checkpoint, if present in the configuration file.
|`--reuse-backup [INCREMENTAL_TYPE]`|Overrides reuse_backup option behaviour. Possible values for `INCREMENTAL_TYPE` are: `off`: do not reuse the last available backup; `copy`: reuse the last available backup for a server and create a copy of the unchanged files (reduce backup time);  `link`: reuse the last available backup for a server and create a hard link of the unchanged files (reduce backup time and space); `link` is the default target if `--reuse-backup` is used and `INCREMENTAL_TYPE` is not explicit.|
|`--retry-times`|Number of retries of base backup copy, after an error. Used during both backup and recovery operations. Overrides value of the parameter basebackup_retry_times, if present in the configuration file.||
|`--no-retry`|Same as `--retry-times 0`||
|`--retry-sleep`|Number of seconds of wait after a failed copy, before retrying. Used during both backup and recovery operations. Overrides value of the parameter `basebackup_retry_sleep`, if present in the configuration file.||
|`-j, --jobs`|Number of parallel workers to copy files during backup. Overrides value of the parameter `parallel_jobs`, if present in the configuration file.||
|`--jobs-start-batch-period`|The time period in seconds over which a single batch of jobs will be started. Overrides the value of parallel_jobs_start_batch_period, if present in the configuration file. Defaults to 1 second.|1 second|
|`--jobs-start-batch-size`|Maximum number of parallel workers to start in a single batch. Overrides the value of `parallel_jobs_start_batch_size`, if present in the configuration file. Defaults to 10 jobs.||
|`--bwlimit KBPS`|Maximum transfer rate in kilobytes per second. A value of 0 means no limit. Overrides `bandwidth_limit` configuration option.|undefined|
|`--wait, -w`|Wait for all required WAL files by the base backup to be archived||
|`--wait-timeout`|The time, in seconds, spent waiting for the required WAL files to be archived before timing out||
|`--manifest`|Forces the creation of a backup manifest file at the end of a backup. Overrides value of the parameter `autogenerate_manifest`, from the configuration file. Works with rsync backup method and strategies only||
|`--no-manifest`|Disables the automatic creation of a backup manifest file at the end of a backup. Overrides value of the parameter `autogenerate_manifest`, from the configuration file. Works with rsync backup method and strategies only||

## Backup ID shortcuts

Rather than using the timestamp backup ID, you can use any of the following shortcuts/aliases to identity a backup for a given server.

|**Shortcut**|**Description**|
|------------|---------------|
|`first`|Oldest available backup for that server, in chronological order|
|`last`|Latest available backup for that server, in chronological order|
|`latest`|Same as last|
|`oldest`|Same as first|
|`last-failed`|Latest failed backup, in chronological order|

Using those keywords with Barman commands allows you to execute actions without knowing the exact ID of a backup for a server. For example, to remove the oldest backup available in the catalog and reclaim disk space, you can issue:
```bash
barman delete \<server_name> oldest
```
Additionally, if backup was taken with the`--name \<friendly_name>` option, you can use the friendly name in place of the backup ID to refer to that specific backup.