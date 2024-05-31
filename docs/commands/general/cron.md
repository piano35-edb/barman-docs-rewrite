# Cron

|**Command** | **Category** |  **Description**| **Synopsis**|
|------------|--------------|-----------------|----------|
|`barman cron`|General|Perform maintenance operations on WAL files and backups|`barman cron`|


# Details

Barman doesn't include a long-running daemon or service file, therefore, there's no need to use  `systemctl start`, `service start`, or similar commands.  Instead, the `barman cron` subcommand is provided to perform background "steady-state" backup operations.

You can perform maintenance operations, on both WAL files and backups, using `barman cron`.

!!!Note
    `barman cron` should be executed in a *cron script*. It's recommendend to schedule `barman cron` to run every minute. If you installed Barman using the rpm or debian package, a cron entry that executes every minute will be created for you.

    `barman cron` only runs background maintenance tasks. It's not responsible for running scheduled backups. Any regularly-scheduled backup jobs that you require must be scheduled separately, for example, in another cron entry which runs `barman backup all`.

`barman cron` executes WAL archiving operations concurrently on a server basis.  This enforces retention policies on servers that have:

- `retention_policy` not empty and valid.
- `retention_policy_mode` set to auto.

The `cron` command ensures that WAL streaming is started for servers that have requested it, by transparently executing the `receive-wal` command.

In order to stop the operations started by the `cron` command, comment out the cron entry and execute:

```bash
barman receive-wal --stop SERVER_NAME
```
!!!tip
    Use `barman list-servers` to ensure you cover all of your servers.

## Additional options

`--keep-descriptors`
Keep the stdout and the stderr streams of the Barman subprocesses attached to this one. This is useful for Docker based installations.