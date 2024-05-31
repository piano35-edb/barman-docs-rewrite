# Configuring a new server

To configure a new server, use the following steps. The following conventions are used:

|**Name**|**Description**|
|--------|---------------|
|`pg`|The server ID and host name where PostgreSQL is installed|
|`backup`|The host name where Barman is located|
|`barman`|The user running Barman on the backup server (identified by the parameter `barman_user` in the configuration)|
|`postgres`|The user running PostgreSQL on the `pg` server|

!!!important
    A server in Barman must refer to the same PostgreSQL instance for the whole backup and recoverability history *(i.e. the same system identifier)*.  
    
    If you perform an upgrade of the instance, for example, with **pg_upgrade**, don't reuse the same server definition in Barman.

## Requirements

Before setting up your PostgreSQL server in Barman, ensure you've met the following requirements:

- You've chosen a defined strategy for WAL archiving.
- You've chosen a defined strategy for backups methods.

## PostgreSQL connection

This connection is required by Barman in order to coordinate its activities with the server, as well as for monitoring purposes.

Ensure one of the following:

 - The backup server can connect to the PostgreSQL server on **pg** as **superuser**.
 - The correct set of privileges are granted to the user that connects to the database.

## Create a user

To create a specific superuser in PostgreSQL, named **barman**, use the following command:
```sql
postgres@pg\$ createuser -s -P barman
```

Alternatively, create a normal user with the required set of privileges with the following commands:
```sql
postgres@pg\$ createuser -P barman
```
```sql
GRANT EXECUTE ON FUNCTION pg_start_backup(text, boolean, boolean) to barman;
GRANT EXECUTE ON FUNCTION pg_stop_backup() to barman;
GRANT EXECUTE ON FUNCTION pg_stop_backup(boolean, boolean) to barman;
GRANT EXECUTE ON FUNCTION pg_switch_wal() to barman;
GRANT EXECUTE ON FUNCTION pg_create_restore_point(text) to barman;
GRANT pg_read_all_settings TO barman;
GRANT pg_read_all_stats TO barman;
```
## Differences between versions

- In PostgreSQL 15 beta and higher PostgreSQL versions, the functions `pg_start_backup` and `pg_stop_backup` have been renamed and have different signatures. Replace the first three lines in the previous command block with:
```sql
GRANT EXECUTE ON FUNCTION pg_backup_start(text, boolean) to barman;
GRANT EXECUTE ON FUNCTION pg_backup_stop(boolean) to barman;
```
- For PostgreSQL version 13 and lower without a real superuser, the `--force` option of the `barman switch-wal` command won't work.  
- For PostgreSQL version 14 or higher, you can grant the `pg_checkpoint` role, so you can use this feature without a superuser:
```sql
GRANT pg_checkpoint TO barman;
```
!!!important
    The `createuser` command will prompt for a password.  It's recommended to add the password to the `\~barman/.pgpass` file on the backup server. For more information, see ["The Password File"](https://www.postgresql.org/docs/current/static/libpq-pgpass.html) in the PostgreSQL documentation.
    
## Client authentication

You can choose your preferred client authentication method from those offered by PostgreSQL. For more information, see ["Client Authentication"](https://www.postgresql.org/docs/current/static/client-authentication.html) in the PostgreSQL documentation.

## Verify connection

Run the following command as the **barman** user on the **backup** host to verify that it can connect to PostgreSQL on the **pg** host:

```bash
barman@backup\$ psql -c 'SELECT version()' -U barman -h pg postgres
```
Save the output *(user name, host name and database name)* to use within the `conninfo` option for your server configuration.  

For example:
```bash
**[pg]**
*; ...*
conninfo = host=pg user=barman dbname=postgres application_name=myapp
```
!!!note
    `application_name` is optional.
