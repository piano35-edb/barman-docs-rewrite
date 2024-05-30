# Configuration examples

The following is a basic example of main configuration file:
```bash
**[barman]**

barman_user = barman

configuration_files_directory = /etc/barman.d

barman_home = /var/lib/barman

log_file = /var/log/barman/barman.log

log_level = INFO

compression = gzip
```


The example below, on the other hand, is a server configuration file that uses streaming backup:
```bash
**[streaming-pg]**

description = "Example of PostgreSQL Database (Streaming-Only)"

conninfo = host=pg user=barman dbname=postgres

streaming_conninfo = host=pg user=streaming_barman

backup_method = postgres

streaming_archiver = **on**

slot_name = barman
```

The following example defines a configuration model with a set of overrides that can be applied to the server whose cluster is **streaming-pg**:
```bash
**[streaming-pg:switchover]**

cluster=streaming-pg

model=**true**

conninfo = host=pg-2 user=barman dbname=postgres

streaming_conninfo = host=pg-2 user=streaming_barman
```

The following code shows a basic example of traditional backup using rsync/SSH:
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

For more information, refer to the the following files:

- `distributed barman.conf`
- `ssh-server.conf-template`
- `streaming-server.conf-template`

# Example configuration file

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