# archive-wal

|**Command** | **Category** |  **Description**| **Synopsis**|
|------------|--------------|-----------------|----------|
|`archive-wal`|Server|Perform maintenance operations on WAL files and backups|`barman archive-wal`|


## Details

The `archive-wal` command execute maintenance operations on WAL files for a given server. This operations include processing of the WAL files received from either the streaming connection, the `archive_command` or both.

It gets any incoming xlog file (both through standard `archive_command` and streaming replication, where applicable), and moves them in the WAL archive for that server. If necessary, it can apply compression when requested.

!!!IMPORTANT
    The `archive-wal command`, even if it can be directly invoked, is designed to be started from the `cron` general command.
