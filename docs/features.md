# Features

The main features and goals of Barman are:


## Management and Configuration

- Management of multiple PostgreSQL servers
- Server status and information
- A simple INI configuration file
- Relocation of PGDATA and tablespaces at recovery time
- General and disk usage information of backups
- Server diagnostics for backup
- Local storage of metadata

## Backup and Recovery

- Full hot physical backup of a PostgreSQL server
- Point-In-Time-Recovery (PITR)
- Incremental backup and recovery
- Parallel backup and recovery
- Remote backup via rsync/SSH or pg_basebackup (including a 9.2+ standby)
- Support for both WAL archiving and streaming
- Support for synchronous WAL streaming (“zero data loss”, RPO=0)
- Support for both local and remote (via SSH) recovery
- Hub of WAL files for enhanced integration with standby servers
- Management of retention policies for backups and WAL files
- Compression of WAL files (bzip2, gzip or custom)
- Management of base backups and WAL files through a catalog
- Pre/Post backup hook scripts

And best of all, Barman is developed purely in Python.

