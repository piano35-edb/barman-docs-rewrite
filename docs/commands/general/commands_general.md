# General commands

Barman has many commands and, for the sake of exposition, we can organize them by scope.

The scope of the **general commands** is the entire Barman server, that can backup many PostgreSQL servers. **Server commands**, instead, act only on a specified server. **Backup commands** work on a backup, which is taken from a certain server.

**Usage**

All commands should be prefaced with `barman`.  For example `barman cron`

# General commands index

The following list includes the general commands.


|**Command** | **Category** |  **Description**|
|------------|--------------|-----------------|
|`cron`| General | Barman doesn't include a long-running daemon or service file (there's nothing to systemctl start, service start, etc.). Instead, the barman cron subcommand is provided to perform barman's background "steady-state" backup operations.| 
|`diagnose` | General | See barman diagnose|
|`list-servers`| General | Display the list of active servers configured for your backup system.|

