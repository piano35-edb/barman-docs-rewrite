# Upgrade paths

Barman follows the trunk-based development paradigm.  There is only one stable version, the latest. After every commit, Barman goes through thousands of automated tests for each supported PostgreSQL version and on each supported Linux distribution.

Every version is back-compatible with previous versions. Therefore, upgrading Barman normally requires a simple update of packages using `yum update` or `apt update`.

!!!important
    The following exceptions in the development of Barman have required some small changes to the configuration.

## Upgrading to Barman 3.0.0

**Default backup approach for Rsync backups is now concurrent**

Barman will now use concurrent backups if neither `concurrent_backup` or `exclusive_backup` are specified in `backup_options`. This differs from previous Barman versions where the default was to use exclusive backup.

If you require exclusive backups you will now need to add `exclusive_backup` to `backup_options` in the Barman configuration.

Exclusive backups are not supported with PostgreSQL 15.

**Metadata changes**

A new field named **compression** will be added to the metadata stored in the `backup.info` file for all backups taken with version 3.0.0. This is used when recovering from backups taken using the built-in compression functionality of **pg_basebackup**.

The presence of this field means that earlier versions of Barman are not able to read backups taken with Barman 3.0.0. This means that if you downgrade from Barman 3.0.0 to an earlier version, you will have to either manually remove any backups taken with 3.0.0 or edit the `backup.info` file of each backup to remove the compression field.

The same metadata change affects [pg-backup-api](https://github.com/EnterpriseDB/pg-backup-api) so if you are using **pg-backup-api**, you will need to update it to version 0.2.0.

## Upgrading from Barman 2.10

If you are using `barman-cloud-wal-archive` or `barman-cloud-backup`, be aware that from version 2.11 all cloud utilities have been moved into the new `barman-cli-cloud` package. Therefore, you need to ensure that the `barman-cli-cloud` package is properly installed as part of the upgrade to the latest version. If you are not using the above tools, you can upgrade to the latest version as usual.

## Upgrading from Barman 2.X (prior to 2.8)

Before upgrading from a version of Barman 2.7 or older users of rsync backup method on a primary server should explicitly set `backup_options` to either `concurrent_backup` *(recommended for PostgreSQL 9.6 or higher)*, or `exclusive_backup` (current default), otherwise Barman emits a warning every time it runs.

## Upgrading from Barman 1.X

If your Barman installation is 1.X, you need to explicitly configure the archiving strategy. Before, the file based archiver, controlled by archiver, was enabled by default.

Before you upgrade your Barman installation to the latest version, make sure you add the following line either globally or for any server that requires it:
```bash
archiver = **on**
```
Additionally, for a few releases, Barman will transparently set `archiver = on` with any server that has not explicitly set an archiving strategy and emit a warning.
