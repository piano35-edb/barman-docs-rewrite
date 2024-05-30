Backup scripts can be configured with the following global configuration options (which can be overridden on a per server basis):

|**Script**|**Description**|
|----------|---------------|
|`pre_backup_script`|Hook script executed before a base backup, only once, with no check on the exit code|
|`pre_backup_retry_script`|Retry hook script executed before a base backup, repeatedly until success or abort|
|`post_backup_retry_script`|Retry hook script executed after a base backup, repeatedly until success or abort|
|`post_backup_script`|Hook script executed after a base backup, only once, with no check on the exit code|

The script definition is passed to a shell and can return any exit code. Only in case of a retry script, Barman checks the return code.

The shell environment will contain the following variables:

|**Variable**|**Description**|
|------------|---------------|
|`BARMAN_BACKUP_DIR`|Backup destination directory|
|`BARMAN_BACKUP_ID`|ID of the backup|
|`BARMAN_CONFIGURATION`|Configuration file used by Barman|
|`BARMAN_ERROR`|Error message, if any (only for the post phase)|
|`BARMAN_PHASE`|Phase of the script, either pre or post|
|`BARMAN_PREVIOUS_ID`|ID of the previous backup (if present)|
|`BARMAN_RETRY`|1 if it is a retry script, 0 if not|
|`BARMAN_SERVER`|Name of the server|
|`BARMAN_STATUS`|Status of the backup|
|`BARMAN_VERSION`|Version of Barman|
