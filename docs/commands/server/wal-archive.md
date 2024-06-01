# wal-archive

|**Command** | **Category** |  **Description**| **Synopsis**|
|------------|--------------|-----------------|----------|
|`wal-archive`|Server|`archive_command` based on Barman's `put-wal`|`barman-wal-archive [*OPTIONS*] *BARMAN_HOST* *SERVER_NAME* *WAL_DEST*`|


## Details
This script can be used in the `archive_command` of a PostgreSQL server to ship WAL files to a Barman host using the `put-wal` command. An SSH connection will be opened to the Barman host. `barman-wal-archive` allows the integration of Barman in PostgreSQL clusters to improve business continuity.


## Positional arguments

The following positional arguments can be used with the `wal-archive` command:

|**Argument**|**Description**|**Default**|**Example**|
|------------|---------------|-----------|-----------|
|`BARMAN_HOST`|The host of the Barman server.| | |
|`server_name`|The name of the server as configured in Barman.| | |
|`wal_path`|The value of the `%p` keyword (according to `archive_command`).| | |

## Optional arguments

The following optional arguments can be used with the `wal-archive` command:

|**Argument**|**Description**|**Default**|**Example**|
|------------|---------------|-----------|-----------|
|`\--help`|Show the help message and exit| | |
|`\-V, --version`|Show the version number and exit| | |
|`-U USER, --user USER`|The user used for the ssh connection to the Barman server.|barman| |
|`--port PORT`|The port used for the ssh connection to the Barman server.| | |
|`-c CONFIG, --config CONFIG`|Configuration file on the Barman server.| | |
|`-t, --test`|Test both the connection and the configuration of the requested PostgreSQL server in Barman to make sure it is ready to receive WAL files. With this option, the `WAL_NAME` and `WAL_DEST` mandatory arguments are ignored.| | |

## Exit status

|**Exit code**|**Description**|
|-------------|---------------|
|0|Success|
|Other non-zero code|Failure|


