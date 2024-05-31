# switch-wal

|**Command** | **Category** |  **Description**| **Synopsis**|
|------------|--------------|-----------------|----------|
|`switch-wal`|Server|Make the PostgreSQL server switch to another transaction log file (WAL)|`barman switch-wal`|

## Syntax
```bash
barman switch-wal <server_name>
```

## Details

This command makes the PostgreSQL server switch to another transaction log file (WAL), allowing the current log file to be closed, received and then archived.  It execute `pg_switch_wal()` on the target server (from PostgreSQL 10), or `pg_switch_xlog` (for PostgreSQL 8.3 to 9.6).


If there hasn't been any transaction activity since the last transaction log file switch, the switch needs to be forced using the `--force` option.  It forces the switch by executing CHECKPOINT before `pg_switch_xlog()`. 
!!!warning
    Executing a CHECKPOINT might increase I/O load on a PostgreSQL server. Use this option with care.

The `--archive` option requests Barman to trigger WAL archiving after the xlog switch. By default, a 30 seconds timeout is enforced.  The timepout can be changed with `--archive-timeout`. If no WAL file is received, an error is returned.  The `--archive` and `--archive-timeout` options are also available on standby servers.

!!!NOTE
    In Barman 2.1 and 2.2 this command was called `switch-xlog`. It has been renamed for naming consistency with PostgreSQL version 10 and higher.