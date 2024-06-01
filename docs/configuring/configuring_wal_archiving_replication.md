
## Archiving features

### WAL compression

The `barman cron` command will compress WAL files if the compression option is set in the configuration file. This option allows five values:

|**Value**|**Description**|**Note**|
|---------|---------------|--------|
|`bzip2`| Used for Bzip2 compression|Requires the `bzip2` utility|
|`gzip`| Used for Gzip compression|Requires the `gzip` utility|
|`pybzip2`|Used for Bzip2 compression|Uses Python's internal compression module|
|`pygzip`|Used for Gzip compression|Uses Python's internal compression module|
|`pigz`|Used for Pigz compression|Requires the `pigz` utility|
|`custom`|Used for custom compression|Requires configuring following options: `custom_compression_filter`, `custom_decompression_filter`, `custom_compression_magic`|

!!!info
    All methods but `pybzip2` and `pygzip` require `barman archive-wal` to fork a new process.

### Synchronous WAL streaming

Barman can also reduce the Recovery Point Objective to zero, by collecting the transaction WAL files like a synchronous standby server would.

To configure such a scenario, the Barman server must be configured to archive WALs via the streaming connection, and the `receive-walprocess` should figure as a synchronous standby of the PostgreSQL server.

Use the following steps to configure it:

1. Retrieve the application name of the Barman `receive-wal` process with the `show-servers` command:
```bash
barman@backup\$ barman show-servers pg**\|**grep streaming_archiver_name
streaming_archiver_name: barman_receive_wal
```

2. Add the application name to the `postgresql.conf` file as a synchronous standby:
```bash
synchronous_standby_names = 'barman_receive_wal'
```
3. Restart the PostgreSQL server for the configuration to be reloaded.

!!!success
    If the server has been configured correctly, the `replication-status` command should show the `receive_wal` process as a synchronous streaming client:
```sql
[root@backup \~]\# barman replication-status pg
Status of streaming clients for server 'pg':
Current xlog location on master: 0/9000098
Number of streaming clients: 1
1\. *\#1 Sync WAL streamer*
Application name: barman_receive_wal
Sync stage : 3/3 Remote write
Communication : TCP/IP
IP Address : 139.59.135.32 / Port: 58262 / Host: -
User name : streaming_barman
Current state : streaming (sync)
Replication slot: barman
WAL sender PID : 2501
Started at : 2016-09-16 10:33:01.725883+00:00
Sent location : 0/9000098 (diff: 0 B)
Write location : 0/9000098 (diff: 0 B)
Flush location : 0/9000098 (diff: 0 B)
```
!!!IMPORTANT
    This is only an example of configuration, to show you that Barman is eligible to be a synchronous standby node. It doesn't imply to use ONLY Barman. You can read [*"Synchronous Replication"*](https://www.postgresql.org/docs/current/static/warm-standby.html#SYNCHRONOUS-REPLICATION) from the PostgreSQL documentation for further information on this topic.

## Catalog management features

### Minimum redundancy safety

You can define the minimum number of periodic backups for a PostgreSQL server, using the global/per server configuration option  `minimum_redundancy`.  It is set to 0 by default.

When set to any number greater than 0, Barman makes sure that at any time, you'll have at least that number of backups in a server catalog.

This can protect from accidental `barman delete` operations.

!!!IMPORTANT
    Make sure that your retention policy settings don't collide with minimum redundancy requirements. Regularly check Barman's log for messages on this topic.

## Retention policies

Barman supports retention policies for backups.

A backup retention policy is a user-defined policy that determines how long backups and related archive logs (Write Ahead Log segments) need to be retained for recovery procedures.

Barman can retain the periodic backups required to satisfy the current retention policy and any archived WAL files required for the complete recovery of those backups.

You can define a retention policy in terms of backup redundancy (how many periodic backups) or a recovery window (how long).

### Retention policy based on redundancy

With a redundancy-based retention policy, you can determine how many periodic backups to keep. A redundancy-based retention policy is contrasted with retention policies that use a recovery window.

### Retention policy based on recovery window

A recovery window is one type of Barman backup retention policy, in which you specify a period of time and Barman ensures retention of backups and/or archived WAL files required for point-in-time recovery to any time during the recovery window. The interval always ends with the current time and extends back in time for the number of days specified by the user. 

For example, if the retention policy is set for a recovery window of seven days, and the current time is 9:30 AM on Friday, Barman retains the backups required to allow point-in-time recovery back to 9:30 AM on the previous Friday.

### Scope

Retention policies can be defined for:

-   **PostgreSQL periodic base backups**: through the `retention_policy` configuration option
-   **Archive logs for Point-In-Time-Recovery**: through the `wal_retention_policy` configuration option

!!!IMPORTANT
    In a temporal dimension, archive logs must be included in the time window of periodic backups.

The two typical use cases are full or partial point-in-time recovery.

**Full point in time recovery scenario**

- The base backups and archive logs share the same retention policy, allowing you to recover at any point in time from the first available backup.

**Partial point in time recovery scenario**

- The base backup retention policy is wider than that of archive logs.  You can keep full or weekly backups of the last six months, but archive logs for the last four weeks *(granting to recover at any point in time starting from the last four periodic weekly backups)*.

!!!info
    Barman implements only the **full point in time recovery** scenario, by constraining the `wal_retention_policy` option to `main`.

### How retention policies work

Retention policies in Barman can be:

-   **automated**: Enforced by `barman cron`.
-   **manual**: Barman reports obsolete backups and allows you to delete them.

!!!info
    Barman doesn't implement manual enforcement. This feature is planned for future versions.

### Configuration and syntax

Retention policies can be defined through the following configuration options:

|**Option**|**Description**|
|----------|---------------|
|`retention_policy`|For base backup retention|
|`wal_retention_policy`|For archive logs retention|
|`retention_policy_mode`|Can only be set to `auto` *(retention policies are automatically enforced by the `barman cron` command)*|

These configuration options can be defined both at a global level and a server level, allowing maximum flexibility in a multi-server environment.

### Syntax for `retention_policy`

The general syntax for a base backup retention policy through `retention_policy` is:
```bash
retention_policy = {REDUNDANCY value \| RECOVERY WINDOW OF value {DAYS \| WEEKS \| MONTHS}}
```
Where:

-   Syntax is case insensitive
-   Value is an integer and is \> 0

**redundancy retention policy**

- `value` must be greater than or equal to the server minimum redundancy level.  If that value isn't assigned, a warning is generated. The first valid backup is the value of the backup in a reverse-ordered time series.

**recovery window policy**

- The point of recoverability is: *(current time - window - the first valid backup is the first available backup before the point of recoverability)*. Its value in a reverse-ordered time series must be greater than or equal to the server minimum redundancy level.  If it isn't assigned to that value, a warning is generated.

By default, `retention_policy` is empty *(no retention enforced)*.

### Syntax for `wal_retention_policy`

The only allowed value for `wal_retention_policy` is the value `main`.  It maps the retention policy of archive logs to that of base backups.

## Setting up

Before proceeding, ensure you've properly configured PostgreSQL on **pg** to accept streaming replication connections from the Barman server.  For more information, see the following sections in the PostgreSQL documentation:

-   [Role attributes](https://www.postgresql.org/docs/current/static/role-attributes.html)
-   [The pg_hba.conf file](https://www.postgresql.org/docs/current/static/auth-pg-hba-conf.html)
-   [Setting up standby servers using streaming replication](https://www.postgresql.org/docs/current/static/protocol-replication.html)

Use the following steps to set it up:

1. Configure the `wal_level` parameter to ensure that all the useful information necessary for a backup to be coherent are included in the transaction log file:
```bash
wal_level = 'replica'\|'logical'
```
2. Restart the PostgreSQL server for the configuration to be reloaded.


## Server configuration file

Create a new file, called `pg.conf` in the `/etc/barman.d` directory, with the following content:
```bash
**[pg]**
description = "Our main PostgreSQL server"
conninfo = host=pg user=barman dbname=postgres
backup_method = postgres
*\# backup_method = rsync*
```
The `conninfo` option is set accordingly to the section *"Preliminary steps: PostgreSQL connection"*.

If you plan to use the streaming connection for WAL archiving or to create a backup of your server, add the `streaming_conninfo` parameter in your server configuration file:
```bash
streaming_conninfo = host=pg user=streaming_barman dbname=postgres
```
This value must be chosen accordingly as described in the section *"Preliminary steps: PostgreSQL connection"*.