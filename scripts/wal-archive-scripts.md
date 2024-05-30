Similar to backup scripts, archive scripts can be configured with global configuration options (which can be overridden on a per server basis):

|**Script**|**Description**|
|----------|---------------|
|`pre_archive_script`|Hook script executed before a WAL file is archived by maintenance (usually barman cron), only once, with no check on the exit code|
|`pre_archive_retry_script`|Retry hook script executed before a WAL file is archived by maintenance (usually barman cron), repeatedly until it is successful or aborted|
|`post_archive_retry_script`|Retry hook script executed after a WAL file is archived by maintenance, repeatedly until it is successful or aborted|
|`post_archive_script`|Hook script executed after a WAL file is archived by maintenance, only once, with no check on the exit code|

The script is executed through a shell and can return any exit code. Only in case of a retry script, Barman checks the return code.

Archive scripts share with backup scripts some environmental variables:

|**Variable**|**Description**|
|------------|---------------|
|`BARMAN_CONFIGURATION`|Configuration file used by Barman|
|`BARMAN_ERROR`|Error message, if any (only for the post phase)|
|`BARMAN_PHASE`|Phase of the script, either pre or post|
|`BARMAN_SERVER`|Name of the server|

The following variables are specific to archive scripts:

|**Variable**|**Description**|
|------------|---------------|
|`BARMAN_SEGMENT`|Name of the WAL file|
|`BARMAN_FILE`|Full path of the WAL file|
|`BARMAN_SIZE`|Size of the WAL file|
|`BARMAN_TIMESTAMP`|WAL file timestamp|
|`BARMAN_COMPRESSION`|Type of compression used for the WAL file|