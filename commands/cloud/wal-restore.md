# wal-restore

|**Command** | **Category** |  **Description**| **Synopsis**|
|------------|--------------|-----------------|----------|
|`wal-restore`|WAL|'restore_command' based on Barman's `get-wal`|`barman-wal-restore [*OPTIONS*] *BARMAN_HOST* *SERVER_NAME* *WAL_NAME* *WAL_DEST*`|


# Details
This script can be used as a `restore_command` for PostgreSQL servers, retrieving WAL files using the `get-wal` feature of Barman. An SSH connection will be opened to the Barman host. `barman-wal-restore' allows the integration of Barman in PostgreSQL clusters to improve business continuity.


# Positional arguments

The following positional arguments can be used with the `wal-restore` command:

|**Argument**|**Description**|**Default**|**Example**|
|------------|---------------|-----------|-----------|
|`BARMAN_HOST`|The host of the Barman server.| | |
|`server_name`|The name of the server as configured in Barman.| | |
|`wal_name`|The value of the `%f` keyword (according to `restore_command`).| | |
|`wal_dest`|The value of the `%p` keyword (according to `restore_command`).| | |

# Optional arguments

The following optional arguments can be used with the `wal-restore` command:

|**Argument**|**Description**|**Default**|**Example**|
|------------|---------------|-----------|-----------|
|`\--help`|Show the help message and exit| | |
|`\-V, --version`|Show the version number and exit| | |
|`-U USER, --user USER`|The user used for the ssh connection to the Barman server.|barman| |
|`--port PORT`|The port used for the ssh connection to the Barman server.| | |
|`-s SECONDS, --sleep SECONDS`|Sleep for SECONDS after a failure of `get-wal` request.|0 (nowait)| | 
|`-p JOBS, --parallel JOBS`|Specifies the number of files to peek and transfer in parallel|0 (disabled)| |
|`--spool-dir SPOOL_DIR`|Specifies spool directory for WAL files.|`/var/tmp/walrestore`| | 
|`-P, --partial`|Retrieve also partial WAL files (.partial).| | |
|`-z, --gzip`|Transfer the WAL files compressed with gzip.| | |
|`-j, --bzip2`|Transfer the WAL files compressed with bzip2.| | |
|`-c CONFIG, --config CONFIG`|Configuration file on the Barman server.| | |
|`-t, --test`|Test both the connection and the configuration of the requested PostgreSQL server in Barman to make sure it is ready to receive WAL files. With this option, the `WAL_NAME` and `WAL_DEST` mandatory arguments are ignored.| | |



# Exit status

|**Exit code**|**Description**|
|-------------|---------------|
|0|Success|
|1|The remote get-wal command failed, most likely because the requested WAL could not be found.|
|2|The SSH connection to the Barman server failed.|
|Other non-zero code|Failure|


