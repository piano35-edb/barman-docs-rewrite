Barman has a variety of compression configuration options.

## Network Compression

It is possible to reduce the size of transferred data using compression. It can be enabled using the network_compression option (global/per server):

!!!IMPORTANT
    The `network_compression` option is not available with the postgres backup method.
```bash
network_compression = **true**\|**false**
```
Setting this option to true will enable data compression during network transfers (for both backup and recovery). By default it is set to false.

## Backup Compression

Barman can use the compression features of `pg_basebackup` in order to compress the backup data during the backup process. This can be enabled using the `backup_compression` config option (global/per server):

!!!IMPORTANT
    The `backup_compression` and other options discussed in this section are not available with the rsyncor local-rsync backup methods. They're only with postgres backup method.

## Compression algorithms

Setting this option will cause pg_basebackup to compress the backup using the specified compression algorithm. Currently, supported algorithm in Barman are: `gzip, lz4, zstd`, and `none`. `none` compression algorithm will create an uncompressed archive.
```bash
backup_compression = gzip\|lz4\|zstd\|none
```
Barman requires the CLI utility for the selected compression algorithm to be available on both the Barman server *and* the PostgreSQL server. The CLI utility is used to extract the backup label from the compressed backup and to decompress the backup on the PostgreSQL server during recovery. These can be installed through system packages named `gzip, lz4` and `zstd` on Debian, Ubuntu, RedHat, CentOS and SLES systems.

!!!Note
    On Ubuntu 18.04 (bionic) the lz4 utility is available in the liblz4-tool pacakge.

!!!Note
    zstd version must be 1.4.4 or higher. The system packages for zstd on Debian 10 (buster), Ubuntu 18.04 (bionic) and SLES 12 install an earlier version - backup_compression = zstd will not work with these packages.
    
    z4 and zstd are only available with PostgreSQL version 15 or higher.

!!!IMPORTANT
    If you are using backup_compression you must also set recovery_staging_path so that barman recoveris able to recover the compressed backups. See the [Recovering compressed backups](https://docs.pgbarman.org/release/3.10.0/#recovering-compressed-backups) section for more information.

## Compression workers

This optional parameter allows compression using multiple threads to increase compression speed (default being 0).
```bash
backup_compression_workers = 2
```
!!!Note
    This option is only available with zstd compression.

!!!Note
    zstd version must be 1.5.0 or higher. Or 1.4.4 or higher compiled with multithreading option.

## Compression level

The compression level can be specified using the backup_compression_level option. This should be set to an integer value supported by the compression algorithm specified in backup_compression. If not defined, compression algorithm default value will be used.

none compression only supports backup_compression_level=0.

!!!Note
    `backup_compression_level` available and default values depends on the compression algorithm used. 

!!!Note
    On PostgreSQL version prior to 15, gzip support `backup_compression_level=0`. It results using the default compression level.

## Compression location

When using Barman with PostgreSQL version 15 or higher it is possible to specify for compression to happen on the server (i.e. PostgreSQL will compress the backup) or on the client (i.e. pg_basebackup will compress the backup). This can be achieved using the `backup_compression_location option`:

!!!IMPORTANT
    The `backup_compression_location` option is only available when running against PostgreSQL 15 or later.
```bash
backup_compression_location = server\|client
```
Using `backup_compression_location = server` should reduce the network bandwidth required by the backup at the cost of moving the compression work onto the PostgreSQL server.

When `backup_compression_location` is set to server then an additional option, `backup_compression_format`, can be set to plain in order to have `pg_basebackup `uncompress the data before writing it to disk:

## Compression format
```bash
backup_compression_format = plain\|tar
```
If `backup_compression_format` is unset or has the value tar then the backup will be written to disk as compressed tarballs. A description of both the plain and tar formats can be found in the [pg_basebackup documentation](https://www.postgresql.org/docs/current/app-pgbasebackup.html).

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