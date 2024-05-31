Pre and post recovery scripts are supported for Barman version 2.4 and higher.

Similar to other scripts, recovery scripts can be configured within global configuration options.  You can override them on a per-server basis.

## Scripts 

|**Script**|**Description**|
|----------|---------------|
|`pre_recovery_script`|Hook script launched before the recovery of a backup, only once, with no check on the exit code|
|`pre_recovery_retry_script`|Retry hook script executed before the recovery of a backup, repeatedly until success or abort|
|`post_recovery_retry_script`|Retry hook script executed after the recovery of a backup, repeatedly until success or abort|
|`post_recovery_script`|Hook script launched after the recovery of a backup, only once, with no check on the exit code|

The script is executed through a shell and can return any exit code. Only in case of a retry script, Barman checks the return code.

## Variables

Recovery scripts uses the same environmental variables of a backup script, plus the following:

|**Variable**|**Description**|
|------------|---------------|
|`BARMAN_DESTINATION_DIRECTORY`|The directory where the new instance is recovered|
|`BARMAN_TABLESPACES`|Tablespace relocation map (JSON, if present)|
|`BARMAN_REMOTE_COMMAND`|Secure shell command used by the recovery (if present)|
|`BARMAN_RECOVER_OPTIONS`|Recovery additional options (JSON, if present)|

## Cloud snapshot recovery scripts

For example scripts that can be helpful when recovering from a snapshot backup in the cloud, see:

- [An example recovery script for GCP](https://github.com/EnterpriseDB/barman/blob/master/scripts/prepare_snapshot_recovery.py)
- [An example runbook for Azure](https://github.com/EnterpriseDB/barman/blob/master/doc/runbooks/snapshot_recovery_azure.md)