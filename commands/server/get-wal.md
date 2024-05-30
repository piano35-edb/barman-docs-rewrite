# get-wal

|**Command** | **Category** |  **Description**| **Synopsis**|
|------------|--------------|-----------------|----------|
|`get-wal`|Server|Request any xlog file from its WAL archive|`barman get-wal`|


# Details

Barman allows users to request any xlog file from its WAL archive through the `get-wal` command:
```bash
barman get-wal [-o OUTPUT_DIRECTORY][-j|-x] <server_name> <wal_id>
```
If the requested WAL file is found in the server archive, the uncompressed content will be returned to STDOUT, unless otherwise specified.

# Options

The following options are available for the `get-wal` command:

|**Option**|**Description**|
|----------|---------------|
|`-o OUTPUT_DIRECTORY`|Allows users to specify a destination directory where Barman will deposit the requested WAL file|
|`-j`|Will compress the output using bzip2 algorithm|
|`-x`|Will compress the output using gzip algorithm|
|`-p SIZE`|Peeks from the archive up to WAL files, starting from the requested file.  `SIZE` must be an integer >= 1. When invoked with this option, `get-wal` returns a list of zero to `SIZE` WAL segment names, one per row.|
|`-P, --partial`|retrieve also partial WAL files (.partial)|
|`-t, --test`|Test both the connection and the configuration of the requested PostgreSQL server in Barman for WAL retrieval. With this option, the `WAL_NAME` mandatory argument is ignored.|
|`keep SERVER_NAME BACKUP_ID`|Flag the specified backup as an archival backup which should be kept forever, regardless of any retention policies in effect.|
|`--target RECOVERY_TARGET`|Specify the recovery target for the archival backup. Possible values for `RECOVERY_TARGET` are:|
||`full`: The backup can always be used to recover to the latest point in time. To achieve this, Barman will retain all WALs needed to ensure consistency of the backup and all subsequent WALs.
||`standalone`: The backup can only be used to recover the server to its state at the time the backup was taken. Barman will only retain the WALs needed to ensure consistency of the backup.|
|`--status`|Report the archival status of the backup. This will either be the recovery target of full or standalone for archival backups or nokeep for backups which have not been flagged as archival.|
|`--release`|Release the keep flag from this backup. This will remove its archival status and make it available for deletion, either directly or by retention policy.|

It is possible to use `get-wal` during a recovery operation, transforming the Barman server into a WAL hub for your servers. This can be automatically achieved by adding the get-wal value to the `recovery_options` global/server configuration option:
```bash
recovery_options = 'get-wal'
```
`recovery_options` is a global/server option that accepts a list of comma separated values. If the keyword get-wal is present during a recovery operation, Barman will prepare the recovery configuration by setting the `restore_command` so that `barman get-wal` is used to fetch the required WAL files. Similarly, one can use the `--get-wal` option for the recover command at run-time.

If `get-wal` is set in `recovery_options` but not required during a recovery operation then the `--no-get-wal` option can be used with the recover command to disable the `get-wal` recovery option.

This is an example of a `restore_command` for a local recovery:
```bash
restore_command = 'sudo -u barman barman get-wal SERVER %f > %p'
```
The `get-wal` command should always be invoked as **barman** user, and it requires the correct permission to read the WAL files from the catalog. This is the reason why we are using `sudo -u barman` in the example.

Setting `recovery_options` to `get-wal` for a remote recovery will instead generate a `restore_command` using the `barman-wal-restore` script. `barman-wal-restore` is a more resilient shell script which manages SSH connection errors.

This script has many useful options such as the automatic compression and decompression of the WAL files and the peek feature, which allows you to retrieve the next WAL files while PostgreSQL is applying one of them. It is an excellent way to optimise the bandwidth usage between PostgreSQL and Barman.

`barman-wal-restore` is available in the `barman-cli` package.

This is an example of a `restore_command` for a remote recovery:
```bash
restore_command = 'barman-wal-restore -U barman backup SERVER %f %p'
```
Since it uses SSH to communicate with the Barman server, SSH key authentication is required for the **postgres** user to login as **barman** on the backup server. If a port other than the SSH default of 22 should be used then the `--port` option can be added to specify the port that should be used for the SSH connection.

You can check that `barman-wal-restore` can connect to the Barman server, and that the required PostgreSQL server is configured in Barman to send WAL files with the following command:
```bash
barman-wal-restore --test backup pg DUMMY DUMMY
```
Where backup is the host where Barman is installed, pg is the name of the PostgreSQL server as configured in Barman and DUMMY is a placeholder (`barman-wal-restore` requires two argument for the WAL file name and destination directory, which are ignored).

If everything is configured correctly you should see the following output:
```bash
Ready to retrieve WAL files from the server pg
For more information on the barman-wal-restore command, type man barman-wal-restore on the PostgreSQL server.
```

