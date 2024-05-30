# check

|**Command** | **Category** |  **Description**| **Synopsis**|
|------------|--------------|-----------------|----------|
|`check`|Server|Check the connection to a given server and its configuration|`barman check`|


# Details

You can check the connection to a given server and the configuration coherence.

!!!tip
    You can use `barman check all` to check all your configured servers.

!!!IMPORTANT
    The `check` command can be considered the most critical feature that Barman implements. It's recommended to integrate it with your alerting and monitoring infrastructure. The `--nagios` option allows you to easily create a plugin for Nagios/Icinga.

# man1
```bash
check-backup SERVER_NAME BACKUP_ID
```
Make sure that all the required WAL files to check the consistency of a physical backup (that is, from the beginning to the end of the full backup) are correctly archived. This command is automatically invoked by the cron command and at the end of every backup operation.
```bash
check-wal-archive SERVER_NAME
```
Check that the WAL archive destination for SERVER_NAME is safe to use for a new PostgreSQL cluster. With no optional args (the default) this will pass if the WAL archive is empty and fail otherwise.

`--timeline [TIMELINE]`
A positive integer specifying the earliest timeline for which associated WALs should cause the check to fail. The check will pass if all WAL content in the archive relates to earlier timelines. If any WAL files are on this timeline or greater then the check will fail.
```bash
check SERVER_NAME
```
Show diagnostic information about SERVER_NAME, including: Ssh connection check, PostgreSQL version, configuration and backup directories, archiving process, streaming process, replication slots, etc. Specify all as SERVER_NAME to show diagnostic information about all the configured servers.

`--nagios`
Nagios plugin compatible output