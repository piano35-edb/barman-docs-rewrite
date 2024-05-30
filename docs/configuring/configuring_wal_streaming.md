Barman can reduce the Recovery Point Objective (RPO) by allowing users to add continuous WAL streaming from a PostgreSQL server, on top of the standard archive_command strategy.

Barman relies on [pg_receivewal](https://www.postgresql.org/docs/current/static/app-pgreceivewal.html).  It exploits the native streaming replication protocol and continuously receives transaction logs from a PostgreSQL server (master or standby). 

!!!note
    Prior to PostgreSQL 10, `pg_receivewal` was named `pg_receivexlog`.

!!!IMPORTANT
    Barman requires that `pg_receivewal` is installed on the same server. It's recommended to install the latest available version of `pg_receivewal`, as it is back compatible. Otherwise, users can install multiple versions of `pg_receivewal` on the Barman server and properly point to the specific version for a server, using the `path_prefix` option in the configuration file.

To enable streaming of transaction logs:

1.  Set up a streaming connection.
2.  Set the `streaming_archiver` option to `on`.

The `cron` command, if the aforementioned requirements are met, transparently manages log streaming through the execution of the `receive-wal` command. This is the recommended scenario.

However, users can manually execute the `receive-wal` command:
```bash
barman receive-wal \<server_name\>
```
!!!NOTE
    The `receive-wal` command is a foreground process.

Transaction logs are streamed directly in the directory specified by the `streaming_wals_directory` configuration option and are then archived by the `archive-wal` command.

Unless otherwise specified in the `streaming_archiver_name` parameter, Barman will set the `application_name` of the WAL streamer process to `barman_receive_wal`, allowing you to monitor its status in the `pg_stat_replication` system view of the PostgreSQL server.

## Replication slots

Replication slots are an automated way to ensure that the PostgreSQL server will not remove WAL files until they were received by all archivers. Barman uses this mechanism to receive the transaction logs from PostgreSQL.

You can find more information about replication slots in the [PostgreSQL manual](https://www.postgresql.org/docs/current/static/warm-standby.html#STREAMING-REPLICATION-SLOTS).

You can even base your backup architecture on streaming connection only. This scenario is useful to configure Docker-based PostgreSQL servers and even to work with PostgreSQL servers running on Windows.

!!!IMPORTANT
    Microsoft Windows support is still experimental, as it is not yet part of our continuous integration system.

## Configuring WAL streaming

First, the PostgreSQL server must be configured to stream the transaction log files to the Barman server.

To configure the streaming connection from Barman to the PostgreSQL server, enable the `streaming_archiver`, as already said, including this line in the server configuration file:
```bash
streaming_archiver = **on**
```
If you plan to use replication slots (recommended), another essential option for the setup of the streaming-based transaction log archiving is the `slot_name` option:
```bash
slot_name = barman
```
This option defines the name of the replication slot that will be used by Barman. It is mandatory if you want to use replication slots.

When you configure the replication slot name, you can manually create a replication slot for Barman with this command:
```sql
barman@backup\$ barman receive-wal --create-slot pg
Creating physical replication slot 'barman' on server 'pg'
Replication slot 'barman' created
Starting with Barman 2.10, you can configure Barman to automatically create the replication slot by setting:
create_slot = auto
```
### Streaming WALs and backups from different hosts (Barman 3.10.0 and later)

Barman uses the connection info defined in `streaming_conninfo` when creating `pg_receivewal` processes to stream WAL segments and uses conninfo when checking the status of replication slots. Because `conninfo` and `streaming_conninfo` are also used when taking backups this default configuration forces Barman to stream WALs and take backups from the same host.

If an alternative configuration is required, such as backups being sourced from a standby with WALs being streamed from the primary, then this can be achieved using the following options:

-   `wal_streaming_conninfo`: A connection string which Barman will use instead of streaming_conninfo when receiving WAL segments via the streaming replication protocol and when checking the status of the replication slot used for receiving WALs.
-   `wal_conninfo`: An optional connection string specifically for monitoring WAL streaming status and performing related checks. If set, Barman will use this instead of `wal_streaming_conninfo` when checking the status of the replication slot.

The following restrictions apply and are enforced by Barman during checks:

-   Connections defined by `wal_streaming_conninfo` and `wal_conninfo` must reach a PostgreSQL instance which belongs to the same cluster reached by the `streaming_conninfo` and `conninfo` connections.
-   The `wal_streaming_conninfo` connection string must be able to create streaming replication connections.
-   Either `wal_streaming_conninfo` *or* `wal_conninfo` (if it is set) must have sufficient permissions to read settings and check replication slot status. The required permissions are one of:
    -   The `pg_monitor` role.
    -   Both the `pg_read_all_settings` and `pg_read_all_stats roles`.
    -   The `superuser` role.

!!!IMPORTANT
    While it is possible to stream WALs from *any* PostgreSQL instance in a cluster there is a risk that WAL segments can be lost when streaming WALs from a standby, if such a standby is unable to keep up with its own upstream source. For this reason it is *strongly recommended* that WALs are always streamed directly from the primary.

### Limitations of partial WAL files with recovery

The standard behaviour of `pg_receivewal` is to write transactional information in a file with `.partial` suffix after the WAL segment name.

Barman expects a partial file to be in the `streaming_wals_directory` of a server. When completed, `pg_receivewal` removes the `.partial` suffix and opens the following one, delivering the file to the archive-wal command of Barman for permanent storage and compression.

In case of a sudden and unrecoverable failure of the master PostgreSQL server, the `.partial` file that has been streamed to Barman contains very important information that the standard archiver (through PostgreSQL's `archive_command`) has not been able to deliver to Barman.

As of Barman 2.10, the `get-wal` command is able to return the content of the current `.partial` WAL file through the `--partial/-P` option. This is particularly useful in the case of recovery, both full or to a point in time. Therefore, in case you run a recover command with `get-wal` enabled, and without `--standby-mode`, Barman will automatically add the `-P` option to `barman-wal-restore` (which will then relay that to the remote `get-wal` command) in the `restore_command` recovery option.

`get-wal` will also search in the incoming directory, in case a WAL file has already been shipped to Barman, but not yet archived.

## WAL archiving via `archive_command`

The `archive_command` is the traditional method to archive WAL files.

The value of this PostgreSQL configuration parameter must be a shell command to be executed by the PostgreSQL server to copy the WAL files to the Barman incoming directory.

This can be done in two ways, both requiring a SSH connection:

-   via `barman-wal-archive` utility (from Barman 2.6)
-   via rsync/SSH (common approach before Barman 2.6)

!!!IMPORTANT
    See the "Concurrent Backup and backup from a standby" section for more detailed information on how Barman supports this feature.

## WAL archiving via `barman-wal-archive`

From Barman 2.6, the **recommended way** to safely and reliably archive WAL files to Barman via `archive_command` is to use the `barman-wal-archive` command contained in the `barman-cli` package.  `barman-cli` must be installed on each PostgreSQL server that is part of the Barman cluster.

Using `barman-wal-archive` instead of rsync/SSH reduces the risk of data corruption of the shipped WAL file on the Barman server. When using rsync/SSH as `archive_command` a WAL file, there is no mechanism that guarantees that the content of the file is flushed and fsync-ed to disk on destination.

For this reason, we have developed the `barman-wal-archive` utility that natively communicates with Barman's `put-wal` command (introduced in 2.6), which is responsible to receive the file, fsync its content and place it in the proper incoming directory for that server. Therefore, `barman-wal-archive` reduces the risk of copying a WAL file in the wrong location/directory in Barman, as the only parameter to be used in the `archive_command` is the server's ID.

For more information on the `barman-wal-archive` command, type `man barman-wal-archive`on the PostgreSQL server.

You can check that `barman-wal-archive` can connect to the Barman server, and that the required PostgreSQL server is configured in Barman to accept incoming WAL files with the following command:
```bash
barman-wal-archive --test backup pg DUMMY
```
Where backup is the host where Barman is installed, **pg** is the name of the PostgreSQL server as configured in Barman and **DUMMY** is a placeholder (`barman-wal-archive` requires an argument for the WAL file name, which is ignored).

If everything is configured correctly you should see the following output:
```bash
Ready to accept WAL files for the server pg
```
Since it uses SSH to communicate with the Barman server, SSH key authentication is required for the postgres user to login as barmanon the backup server. If a port other than the SSH default of 22 should be used then the `--port` option can be added to specify the port that should be used for the SSH connection.

Edit the `postgresql.conf` file of the PostgreSQL instance on the pg database, activate the archive mode and set `archive_command` to use `barman-wal-archive`:
```bash
archive_mode = **on**
wal_level = 'replica'
archive_command = 'barman-wal-archive backup pg %p'
```
Then restart the PostgreSQL server.

## WAL archiving via rsync/SSH

You can retrieve the incoming WALs directory using the `show-servers` Barman command and looking for the `incoming_wals_directory` value:
```bash
barman@backup\$ barman show-servers pg **\|**grep incoming_wals_directory
incoming_wals_directory: /var/lib/barman/pg/incoming
```
Edit the `postgresql.conf` file of the PostgreSQL instance on the pg database and activate the archive mode:
```bash
archive_mode = **on**
wal_level = 'replica'
archive_command = 'rsync -a %p barman@backup:INCOMING_WALS_DIRECTORY/%f'
```
Make sure you change the `INCOMING_WALS_DIRECTORY` placeholder with the value returned by the `barman show-servers` pg command above.

Restart the PostgreSQL server.

In some cases, you might want to add stricter checks to the `archive_command` process. For example, some users have suggested the following one:
```bash
archive_command = 'test \$(/bin/hostname --fqdn) = HOSTNAME \\
&& rsync -a %p barman@backup:INCOMING_WALS_DIRECTORY/%f'
```
Where the `HOSTNAME` placeholder should be replaced with the value returned by `hostname --fqdn`. This *trick* is a safeguard in case the server is cloned and avoids receiving WAL files from recovered PostgreSQL instances.

## Verification of WAL archiving configuration

In order to test that continuous archiving is on and properly working, you need to check both the PostgreSQL server and the backup server. In particular, you need to check that WAL files are correctly collected in the destination directory.

For this purpose and to facilitate the verification of the WAL archiving process, the `switch-wal` command has been developed:
```bash
barman@backup\$ barman switch-wal --force --archive pg
```
The command will force PostgreSQL to switch WAL file and trigger the archiving process in Barman. Barman will wait for one file to arrive within 30 seconds (you can change the timeout through the `--archive-timeout` option). If no WAL file is received, an error is returned.

You can verify if the WAL archiving has been correctly configured using the `barman check` command.