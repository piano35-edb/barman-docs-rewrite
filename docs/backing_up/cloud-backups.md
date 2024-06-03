# Barman-cloud and snapshot backups

Snapshot backups are backups which consist of one or more snapshots of cloud storage volumes.

The `barman-cloud` client utilities can also be used to create and manage backups using cloud snapshots as an alternative to uploading to a cloud object store.

When using `barman-cloud` this way, the backup data is stored by the cloud provider as volume snapshots and the WALs and backup metadata, including the `backup_label`, are stored in cloud object storage.

## Requirements

The prerequisites are the same as for snapshot backups using Barman.   An additional requirement is the credentials used by `barman-cloud` must be able to perform read/write/update operations against an object store.

You can take a snapshot backup for a PostgreSQL server using the following commands:

- `barman backup`:  with the required configuration operations for snapshots if a Barman server is being used to store WALs and backup metadata.
- `barman-cloud-backup`:  with the required command line arguments if there is no Barman server and a cloud object store is being used for WALs and backup metadata.

## Process

The high level process for taking a snapshot backup is as follows:

1. Barman carries out a series of pre-flight checks to validate the snapshot options, instance and disks.
2. Barman starts a backup using the PostgreSQL backup API.
3. The cloud provider API is used to trigger a snapshot for each specified disk. Barman will wait until the snapshot has reached the required state for guaranteeing application consistency before moving on to the next disk.
4. Additional provider-specific data, such as the device name for each disk, is saved to the backup metadata.
5. The mount point and mount options for each disk are saved in the backup metadata.
6. Barman stops the backup using the PostgreSQL backup API.
7. The cloud provider API calls are made on the node where the backup command runs.  This will be either the Barman server (when `barman backup` is used) or the PostgreSQL server (when `barman-cloud-backup` is used).

The following pre-flight checks are carried out before each backup and also when `barman check` runs against a server configured for snapshot backups:

1. The compute instance specified by `snapshot_instance` and any provider-specific arguments exists.
2. The disks specified by `snapshot_disks` exist.
3. The disks specified by `snapshot_disks` are attached to `snapshot_instance`.
4. The disks specified by `snapshot_disks` are mounted on `snapshot_instance`.


## Taking a snapshot backup

To take a snapshot backup with `barman-cloud`, use `barman-cloud-backup` with the following additional arguments:

`--snapshot-disk` (can be used multiple times for multiple disks)
`--snapshot-instance`

If the `--cloud-provider` is `google-cloud-storage` then the following arguments are also required:

`--gcp-project`
`--gcp-zone`

If the `--cloud-provider` is `azure-blob-storage` then the following arguments are also required:

`--azure-subscription-id`
`--azure-resource-group`

If the `--cloud-provider` is `aws-s3` then the following optional arguments can be used:

`--aws-profile`
`--aws-region`

The following options cannot be used with `barman-cloud-backup` when cloud snapshots are requested:

`--bzip2`, `--gzip` or `--snappy`
`--jobs`

Once a backup has been taken it can be managed using the standard `barman-cloud` commands such as `barman-cloud-backup-delete` and `barman-cloud-backup-keep`.