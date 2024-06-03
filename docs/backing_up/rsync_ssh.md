# Backup with rsync/SSH

Backup over rsync was the only available method before Barman version 2.0.  It's the only backup method that supports the incremental backup feature. 

To take a backup using rsync, add the following parameters to the Barman server configuration file:
```bash
backup_method = rsync
ssh_command = ssh postgres@pg
```
The `backup_method` option activates the rsync backup method, and the `ssh_command` option is used to correctly create an SSH connection from the Barman server to the PostgreSQL server.

!!!IMPORTANT
    You won't be able to start a backup if WAL is not being correctly archived to Barman, either through the archiver or the `streaming_archiver`.

To check if the server configuration is valid, you can use the `barman check` command:
```bash
barman@backup\$ barman check pg
```
To take a backup, use the `barman backup` command:
```bash
barman@backup\$ barman backup pg
```