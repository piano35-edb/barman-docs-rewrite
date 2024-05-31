# Configuration examples

The following examples show various types of configuration files:

## Main configuration file 
```bash
**[barman]**

barman_user = barman
configuration_files_directory = /etc/barman.d
barman_home = /var/lib/barman
log_file = /var/log/barman/barman.log
log_level = INFO
compression = gzip
```

## Server configuration file for streaming backup
```bash
**[streaming-pg]**
description = "Example of PostgreSQL Database (Streaming-Only)"
conninfo = host=pg user=barman dbname=postgres
streaming_conninfo = host=pg user=streaming_barman
backup_method = postgres
streaming_archiver = **on**
slot_name = barman
```

## Configuration model with a set of overrides

 In this example, the overrides are applied to the server with a cluster named **streaming-pg**:

```bash
**[streaming-pg:switchover]**
cluster=streaming-pg
model=**true**
conninfo = host=pg-2 user=barman dbname=postgres
streaming_conninfo = host=pg-2 user=streaming_barman
```

## Traditional backup using rsync/SSH
```bash
**[ssh-pg]**
description = "Example of PostgreSQL Database (via Ssh)"
ssh_command = ssh postgres@pg
conninfo = host=pg user=barman dbname=postgres
backup_method = rsync
```parallel_jobs = 1``` 
reuse_backup = link
archiver = **on**
```
!!!tip
    For more information, refer to the the following files:
    - `distributed barman.conf`
    - `ssh-server.conf-template`
    - `streaming-server.conf-template`

## Full configuration file

```bash
[barman]
; Main directory
barman_home = /var/lib/barman
; System user
barman_user = barman
; Log location
log_file = /var/log/barman/barman.log
; Default compression level
;compression = gzip
; Incremental backup
reuse_backup = link
; 'main' PostgreSQL Server configuration
[main]
; Human readable description
description =  "Main PostgreSQL Database"
; SSH options
ssh_command = ssh postgres@pg
; PostgreSQL connection string
conninfo = host=pg user=postgres
; PostgreSQL streaming connection string
streaming_conninfo = host=pg user=postgres
; Minimum number of required backups (redundancy)
minimum_redundancy = 1
; Retention policy (based on redundancy)
retention_policy = REDUNDANCY 2
```