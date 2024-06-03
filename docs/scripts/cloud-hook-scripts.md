# Cloud hook scripts

To use cloud hook scripts, install the `barman-cli-cloud` package on the Barman server.  You can use cloud hook scripts for a post-backup or pre-wal archive.

## As a post-backup

You can use `barman-cloud-backup` as a post backup script for the following Barman backup types:

- Backups taken with `backup_method = rsync`.
- Backups taken with `backup_method = postgres, where backup_compression is not used.

To use the post backup script, add the following to the server configuration in Barman:
```bash
post_backup_retry_script = 'barman-cloud-backup [*OPTIONS*] *DESTINATION_URL* ${BARMAN_SERVER}
```

!!!WARNING
    When running as a hook script `barman-cloud-backup` requires that the status of the backup is `DONE`.  It will fail if the backup has any other status. 
    
    It's recommended to run backups with the `-w / --wait` option so the hook script isn't executed while a backup has a status of `WAITING_FOR_WALS`.

## As a pre-wal archive

Configure `barman-cloud-wal-archive` as a pre-WAL archive script by adding the following to the Barman configuration for a PostgreSQL server:
```bash
pre_archive_retry_script = 'barman-cloud-wal-archive [*OPTIONS*] *DESTINATION_URL* ${BARMAN_SERVER}'
```