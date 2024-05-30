Pre and post backup delete scripts are supported for Barman version 2.4 and higher.

As previous scripts, backup delete scripts can be configured within global configuration options, and it is possible to override them on a per server basis:

|**Script**|**Description**|
|----------|---------------|
|`pre_delete_script`|Hook script launched before the deletion of a backup, only once, with no check on the exit code|
|`pre_delete_retry_script`|Retry hook script executed before the deletion of a backup, repeatedly until success or abort|
|`post_delete_retry_script`|Retry hook script executed after the deletion of a backup, repeatedly until success or abort|
|`post_delete_script`|Hook script launched after the deletion of a backup, only once, with no check on the exit code|

The script is executed through a shell and can return any exit code. Only in case of a retry script, Barman checks the return code.

Delete scripts uses the same environmental variables of a backup script, plus:

`BARMAN_NEXT_ID`: ID of the next backup (if present)