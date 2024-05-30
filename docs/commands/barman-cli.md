# Barman client utilities (`barman-cli`)

Formerly a separate open-source project, `barman-cli` has been merged into Barman's core since version 2.8, and is distributed as an RPM/Debian package. 

`barman-cli`contains a set of recommended client utilities to be installed alongside the PostgreSQL server:

- `barman-wal-archive`: archiving script to be used as `archive_command`.
- `barman-wal-restore`: WAL restore script to be used as part of the restore_command recovery option on standby and recovery servers.

For more information on commands, see the specific Barman documentation pages or the use the `--help` option.

## Installation

Barman client utilities are normally installed where PostgreSQL is installed. It's recommended to install the `barman-cli`package on every PostgreSQL server, both primary or standbys.

To install the `barman-cli` package:

RedHat/CentOS: (as root)

As root, type:
```bash
yum install barman-cli
```
Debian/Ubuntu:  (as root)
```bash
apt-get install barman-cli
```
For more information on installing repositories, see the "Installing" section in the Barman documentation.