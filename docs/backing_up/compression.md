Barman has a variety of compression configuration options.

## Network Compression

It is possible to reduce the size of transferred data using compression. It can be enabled using the network_compression option (global/per server):

!!!IMPORTANT
    The `network_compression` option isn't available with the postgres backup method.
```bash
network_compression = **true**\|**false**
```
The default is `false`.  If set to `true`, data compression is enabled during network transfers (for both backup and recovery).

## Backup Compression

Barman can use the compression features of `pg_basebackup` to compress backup data during the backup process.  You can enable it with the `backup_compression` configuration option (global/per server).

!!!IMPORTANT
    The `backup_compression` and other options in this section aren't available with the rsync or local-rsync backup methods. They're only available with postgres backup method.

## Compression algorithms

This option will cause `pg_basebackup` to compress the backup using the specified compression algorithm. The supported algorithms in Barman are: `gzip, lz4, zstd`, and `none`. The `none` compression algorithm will create an uncompressed archive.
```bash
backup_compression = gzip\|lz4\|zstd\|none
```
Barman requires the CLI utility for the selected compression algorithm to be available on both the Barman server *and* the PostgreSQL server. The CLI utility is used to extract the backup label from the compressed backup and to decompress the backup on the PostgreSQL server during recovery. You can install them with the system packages `gzip, lz4` and `zstd` on Debian, Ubuntu, RedHat, CentOS, and SLES systems.

!!!info
    On Ubuntu 18.04 (Bionic), the `lz4` utility is available in the `liblz4-tool` pacakge.

### Requirements

- `zstd` version must be 1.4.4 or higher. The system packages for `zstd` on Debian 10 (Buster), Ubuntu 18.04 (Bionic) and SLES 12 install an earlier version.  `backup_compression = zstd` won't work with those packages.
- `z4` and `zstd` are only available with PostgreSQL version 15 or higher.
- When using `backup_compression`, you must also set `recovery_staging_path` so that `barman recover` is able to recover the compressed backups.

## Compression workers

This optional parameter allows compression using multiple threads to increase compression speed The default is 0.  

```bash
backup_compression_workers = 2
```
### Requirements

- The `zstd` version must be either:
    - 1.5.0 or higher
    - 1.4.4 or higher compiled with multithreading option

### Limitations
- The `backup_compression_workers` option is only available with `zstd` compression.

## Compression level

The compression level can be specified using the `backup_compression_level` option. This should be set to an integer value supported by the compression algorithm specified in `backup_compression`. If undefined, the compression algorithm default value will be used.

The `none` compression only supports `backup_compression_level=0`.

!!!Note
    `backup_compression_level` available and default values depends on the compression algorithm used. 

!!!Note
    On PostgreSQL version prior to 15, gzip support `backup_compression_level=0` uses the default compression level.

## Compression location

When using Barman with PostgreSQL version 15 or higher, you can use the `backup_compression_location` option to specify if compression happens on the server (i.e. PostgreSQL will compress the backup) or on the client (i.e. `pg_basebackup` will compress the backup).

```bash
backup_compression_location = server\|client
```
 Using `server` should reduce the network bandwidth required by the backup at the cost of moving the compression work onto the PostgreSQL server.

When `backup_compression_location` is set to `server`, an additional option, `backup_compression_format`, can be set to `plain` to have `pg_basebackup `uncompress the data before writing it to disk.

## Compression format
```bash
backup_compression_format = plain\|tar
```
If `backup_compression_format` is unset or has the value `tar`, the backup will be written to disk as compressed tarballs. A description of both the plain and tar formats can be found in the [pg_basebackup documentation](https://www.postgresql.org/docs/current/app-pgbasebackup.html).

!!!IMPORTANT
    Barman uses external tools to manage compressed backups. Depending on the `backup_compression` and `backup_compression_format`, you may need to install one or more tools on the Postgres server and the Barman server. The following table will help you choose according to your configuration.

| **backup_compression** | **backup_compression_format** | **Postgres server** | **Barman server** |
|------------------------|-------------------------------|---------------------|-------------------|
| gzip                   | plain                         | **tar**             | None              |
| gzip                   | tar                           | **tar**             | **tar**           |
| lz4                    | plain                         | **tar, lz4**        | None              |
| lz4                    | tar                           | **tar, lz4**        | **tar, lz4**      |
| zstd                   | plain                         | **tar, zstd**       | None              |
| zstd                   | tar                           | **tar, zstd**       | **tar, zstd**     |
| none                   | tar                           | **tar**             | **tar**           |