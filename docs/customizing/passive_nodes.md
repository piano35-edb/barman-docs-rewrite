You can configure a server as passive node by adding the following option to the server configuration:
```bash
primary_ssh_command = ssh barman@primary_barman
```
This option specifies the SSH connection parameters to the primary server, identifying the source of the backup data for the passive server.

If you invoke Barman with the `-c/--config` option and want to use the same option when the passive node invokes Barman, add the following option on the primary node:
```bash
forward_config_path = **true**
```
## Node synchronization

When a node is marked as passive, Barman imposes the following restrictions:

-  It's excluded from standard maintenance operations.
-  Direct operations to PostgreSQL are forbidden, including `barman backup`.

Synchronization between a passive server and its primary is automatically managed by `barman cron`, which will transparently invoke:

1.  `barman sync-info --primary`:  Collects synchronization information
2.  `barman sync-backup`:  Creates a local copy of every backup that is available on the primary node
3.  `barman sync-wals`:  Copies locally all the WAL files available on the primary node

## Manual synchronization

Although `barman cron` automatically manages passive/primary node synchronization, you can manually trigger synchronization of a backup with the following command:
```bash
barman sync-backup \<server_name\> \<backup_id\>
```
When `sync-backup` is launched, Barman will use the `primary_ssh_command` to connect to the master server.  If the backup is present on the remote machine, Barman will begin to copy all the files using rsync. 

!!!note
    Only one single synchronization process per backup is allowed.

WAL files also can be synchronized with the following command:
```bash
barman sync-wals \<server_name\>
```