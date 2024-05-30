# Cloud hook scripts

Install the `barman-cli-cloud` package on the Barman server.

It is possible to use `barman-cloud-backup` as a post backup script for the following Barman backup types:

- Backups taken with `backup_method = rsync`.
- Backups taken with `backup_method = postgres` where backup_compression is not used.

To do so, add the following to a server configuration in Barman:
```bash
post_backup_retry_script = 'barman-cloud-backup [*OPTIONS*] *DESTINATION_URL* ${BARMAN_SERVER}
```

!!!WARNING
    When running as a hook script `barman-cloud-backup` requires that the status of the backup is `DONE` and it will fail if the backup has any other status. For this reason it is recommended backups are run with the `-w / --wait` option so that the hook script is not executed while a backup has status `WAITING_FOR_WALS`.

Configure barman-cloud-wal-archive as a pre WAL archive script by adding the following to the Barman configuration for a PostgreSQL server:
```bash
pre_archive_retry_script = 'barman-cloud-wal-archive [*OPTIONS*] *DESTINATION_URL* ${BARMAN_SERVER}'
```