# sync-info

|**Command** | **Category** |  **Description**| **Synopsis**|
|------------|--------------|-----------------|----------|
|`sync-info`|Server|Collect information regarding the current status of a Barman server|`barman sync-info`|

# Syntax
```bash
barman sync-info [--primary] \<server_name\> [\<last_wal\> [\<last_position\>]]
```
# Details

Collect information regarding the current status of a Barman server.  This can be useful for synchronization purposes.

The command returns a JSON object containing:

-   A map with all the backups having status DONE for that server
-   A list with all the archived WAL files
-   The configuration for the server
-   The last read position (in the *xlog database file*)
-   The name of the last read WAL file

The JSON response contains all the required information for the synchronization between the master and a passive node.

If `--primary` is specified, the command is executed on the defined primary node, rather than locally.

