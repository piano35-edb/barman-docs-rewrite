# Recovering from a snapshot backup

Barman won't perform a fully-automated recovery from snapshot backups. This is because recovery from snapshots requires the provision and management of new infrastructure which is something better handled by dedicated infrastructure-as-code solutions such as Terraform.

However, the `barman recover` command can still be used to validate the snapshot recovery instance, carry out post-recovery tasks such as checking the PostgreSQL configuration for unsafe options and set any required PITR options. It will also copy the `backup_label` file into place (since the backup label is not stored in any of the volume snapshots) and copy across any required WALs (unless the `--get-wal` recovery option is used, in which case it will configure the PostgreSQL `restore_command` to fetch the WALs).

If restoring a backup made with `barman-cloud-backup` then the more limited `barman-cloud-restore` command should be used instead of `barman recover`.

## Process

Recovery from a snapshot backup consists of the following steps:

1. Provision a new disk for each snapshot taken during the backup.
2. Provision a compute instance where each disk provisioned in step 1 is attached and mounted according to the backup metadata.
3. Use the `barman recover` or `barman-cloud-restore` command to validate and finalize the recovery.

Steps 1 and 2 are best handled by an existing infrastructure-as-code system, however, it is also possible to carry these steps out manually or using a custom script.

!!!tip
    For example scripts that can be helpful when carrying out these steps, see [Cloud snapshot recovery scripts](../scripts/recovery-scripts.md/#cloud-snapshot-recovery-scripts).

!!!note
    The above resources make assumptions about the backup/recovery environment and should not be considered suitable for production use without further customization.

Once the recovery instance is provisioned and disks cloned from the backup snapshots are attached and mounted, run `barman recover` with the following additional arguments:

- `--remote-ssh-command`: The ssh command required to log in to the recovery instance.
- `--snapshot-recovery-instance`: The name of the recovery instance as required by the cloud provider.

Any additional arguments specific to the snapshot provider.  For example:

```sql
barman recover SERVER_NAME BACKUP_ID REMOTE_RECOVERY_DIRECTORY \
--remote-ssh-command 'ssh USER@HOST' \
--snapshot-recovery-instance INSTANCE_NAME
```
Barman will automatically detect that the backup is a snapshot backup and check that the attached disks were cloned from the snapshots for that backup. Barman will then prepare PostgreSQL for recovery by copying the backup label and WALs into place and setting any required recovery options in the PostgreSQL configuration.

## Additional arguments

The following additional `barman recover arguments are available for cloud providers:

For `gcp`:
 - `--gcp-zone`: The name of the availability zone in which the recovery instance is located. If not provided then Barman will use the value of gcp_zone set in the server config.

For `azure`:
 - `--azure-resource-group`: The resource group to which the recovery instance belongs. If not provided then Barman will use the value of `azure_resource_group` set in the server config.

For `aws`:
- `--aws-region`: The AWS region in which the recovery instance is located. If not provided then Barman will use the value of aws_region set in the server config.

## Unavailable arguments

The following `barman recover` arguments and config variables are unavailable when recovering snapshot backups:

|**Command argument**|**Config variable**|
|--------------------|-------------------|
|`--bwlimit`|bandwidth_limit|
|`--jobs`|parallel_jobs|
|`--recovery-staging-path`|recovery_staging_path|
|`--tablespace`|N/A|

## Backup metadata for snapshot backups

Whether the recovery disks and instance are provisioned via infrastructure-as-code, ad-hoc automation or manually, it will be necessary to query Barman to find the snapshots required for a given backup. This can be achieved using `barman show-backup` which will provide details for each snapshot in the backup. 

For example:
```sql
$ barman show-backup primary 20230123T131430
Backup 20230123T131430:
  Server Name            : primary
  System Id              : 7190784995399903779
  Status                 : DONE
  PostgreSQL Version     : 140006
  PGDATA directory       : /opt/postgres/data

  Snapshot information:
    provider             : gcp
    project              : project_id

    device_name          : pgdata
    snapshot_name        : barman-av-ubuntu20-primary-pgdata-20230123t131430
    snapshot_project     : project_id
    Mount point          : /opt/postgres
    Mount options        : rw,noatime

    device_name          : tbs1
    snapshot_name        : barman-av-ubuntu20-primary-tbs1-20230123t131430
    snapshot_project     : project_id
    Mount point          : /opt/postgres/tablespaces/tbs1
    Mount options        : rw,noatime
```
The the `--format=json` option can be used when integrating with external tooling, e.g.:

```sql
$ barman --format=json show-backup primary 20230123T131430
...
"snapshots_info": {
  "provider": "gcp",
  "provider_info": {
    "project": "project_id"
  },
  "snapshots": [
    {
      "mount": {
        "mount_options": "rw,noatime",
        "mount_point": "/opt/postgres"
      },
      "provider": {
        "device_name": "pgdata",
        "snapshot_name": "barman-av-ubuntu20-primary-pgdata-20230123t131430",
        "snapshot_project": "project_id"
      }
    },
    {
      "mount": {
        "mount_options": "rw,noatime",
        "mount_point": "/opt/postgres/tablespaces/tbs1"
      },
      "provider": {
        "device_name": "tbs1",
        "snapshot_name": "barman-av-ubuntu20-primary-tbs1-20230123t131430",
        "snapshot_project": "project_id",
      }
    }
  ]
}
```

For backups taken with `barman-cloud-backup` there is an analogous `barman-cloud-backup-show` command which can be used along with `barman-cloud-backup-list` to query the backup metadata in the cloud object store.

## Cloud provider-specific metadata

Metadata available in `snapshots_info/provider_info` and `snapshots_info/snapshots/*/provider` varies by cloud provider as follows:

### For GCP

The following fields are available in `snapshots_info/provider_info`:

`project`: The GCP project ID of the project which owns the resources involved in backup and recovery.

The following fields are available in `snapshots_info/snapshots/*/provider`:

`device_name`: The short device name with which the source disk for the snapshot was attached to the backup VM at the time of the backup.
`snapshot_nam`e`: The name of the snapshot.
`snapshot_project`: The GCP project ID which owns the snapshot.

### For Azure

The following fields are available in `snapshots_info/provider_info`:

`subscription_id`: The Azure subscription ID which owns the resources involved in backup and recovery.
`resource_group`: The Azure resource group to which the resources involved in the backup belong.

The following fields are available in `snapshots_info/snapshots/*/provider`:

`location`: The Azure location of the disk from which the snapshot was taken.
`lun`: The LUN identifying the disk from which the snapshot was taken at the time of the backup.
`snapshot_name`: The name of the snapshot.

### For AWS

The following fields are available in `snapshots_info/provider_info`:

`account_id`: The ID of the AWS account which owns the resources used to make the backup.
`region`: The AWS region in which the resources involved in backup are located.

The following fields are available in `snapshots_info/snapshots/*/provider`:

`device_name`: The device to which the source disk was mapped on the backup VM at the time of the backup.
`snapshot_id`: The ID of the snapshot as assigned by AWS.
`snapshot_name`: The name of the snapshot.