Pre and post `wal delete` scripts are supported for Barman version 2.4 and higher.

Similarly to the other hook scripts, `wal delete` scripts can be configured with global configuration options, and is possible to override them on a per server basis:

|**Script**|**Description**|
|----------|---------------|
|`pre_wal_delete_script`|Hook script executed before the deletion of a WAL file|
|`pre_wal_delete_retry_script`|Retry hook script executed before the deletion of a WAL file, repeatedly until it is successful or aborted|
|`post_wal_delete_retry_script`|Retry hook script executed after the deletion of a WAL file, repeatedly until it is successful or aborted|
|`post_wal_delete_script`|Hook script executed after the deletion of a WAL file|

The script is executed through a shell and can return any exit code. Only in case of a retry script, Barman checks the return code.

WAL delete scripts use the same environmental variables as WAL archive scripts.

