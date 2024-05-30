You can configure a server as passive node by adding the following option to the server configuration:
```bash
primary_ssh_command = ssh barman@primary_barman
```
This option specifies the SSH connection parameters to the primary server, identifying the source of the backup data for the passive server.

If you are invoking barman with the `-c/--config` option and you want to use the same option when the passive node invokes barman on the primary node then add the following option:
```bash
forward_config_path = **true**
```
## Node synchronization

When a node is marked as passive, Barman imposes the following restrictions:

-  It is excluded from standard maintenance operations.
-  Direct operations to PostgreSQL are forbidden, including `barman backup`.

Synchronization between a passive server and its primary is automatically managed by `barman cron`, which will transparently invoke:

1.  `barman sync-info --primary`, in order to collect synchronisation information
2.  `barman sync-backup`, in order to create a local copy of every backup that is available on the primary node
3.  `barman sync-wals`, in order to copy locally all the WAL files available on the primary node

## Manual synchronization

Although Barman cron automatically manages passive/primary node synchronisation, it's possible to manually trigger synchronization of a backup through:
```bash
barman sync-backup \<server_name\> \<backup_id\>
```
Launching `sync-backup`, Barman will use the `primary_ssh_command` to connect to the master server.  If the backup is present on the remote machine, Barman will begin to copy all the files using rsync. 

!!!note
    Only one single synchronization process per backup is allowed.

WAL files also can be synchronized with the following command:
```bash
barman sync-wals \<server_name\>
```