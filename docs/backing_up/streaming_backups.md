

Barman can backup a PostgreSQL server using the streaming connection, relying on `pg_basebackup`.

!!!IMPORTANT
    Barman requires that `pg_basebackup` is installed in the same server. It's recommended to install the last available version of `pg_basebackup`, as it's backwards-compatible. You can install multiple versions of `pg_basebackup` on the Barman server and point to the specific version for a server, using the `path_prefixoption` in the configuration file.

To successfully backup your server with the streaming connection, use **postgres** as your backup method:
```bash
backup_method = postgres
```
!!!warning
    You won't be able to start a backup if WAL is not being correctly archived to Barman, either through the archiver or the `streaming_archiver`.

To check if the server configuration is valid, use the `barman check` command:
```bash
barman@backup\$ barman check pg
```
To start a backup, use the `barman backup` command:
```bash
barman@backup\$ barman backup pg
```