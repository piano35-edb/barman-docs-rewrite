# Cloud backup delete

|**Command** | **Category** |  **Description**| **Synopsis**|
|------------|--------------|-----------------|----------|
|`cloud-backup-delete`|Cloud|Delete backups stored in the Cloud|`barman-cloud-backup-delete [*OPTIONS*] *SOURCE_URL* *SERVER_NAME*`|

## Supported cloud providers

`cloud-backup-delete` is supported for the following cloud providers:

* AWS Simple Storage Service (S3)
* Azure Blob Storage
* Google Cloud Storage

##  Details
This script can be used to delete backups previously made with `barman-cloud-backup`.

The target backups can be specified either using the backup ID *(as returned by barman-cloud-backup-list)* or by retention policy. Retention policies are the same as those for Barman server and work as described in the Barman manual: *all backups not required to meet the specified policy will be deleted*.

When a backup is successfully deleted any unused WALs associated with that backup are removed. WALs are only considered unused if:

* There are no older backups than the deleted backup *or* all older backups are archival backups.
* The WALs pre-date the `begin_wal` value of the oldest remaining backup.
* The WALs are not required by any archival backups present in cloud storage.

!!!info
    The deletion of each backup involves three separate delete requests to the cloud provider: 

    - Delete the backup files
    - Delete the backup.info file
    - Delete any associated WALs

    If you have a significant number of backups accumulated in cloud storage, deleting by retention policy could result in a large number of delete requests.

## Usage

```bash
barman-cloud-backup-delete [-V] [--help] [-v \| -q] [-t]
[--cloud-provider {aws-s3,azure-blob-storage,google-cloud-storage}]
[--endpoint-url ENDPOINT_URL]
[-P AWS_PROFILE] [--profile AWS_PROFILE]
[--read-timeout READ_TIMEOUT]
[--azure-credential {azure-cli,managed-identity}
[-b BACKUP_ID] [-m MINIMUM_REDUNDANCY]
[-r RETENTION_POLICY] [--dry-run]
[--batch-size DELETE_BATCH_SIZE]
[source_url server_name]
```

##  Positional arguments

The following positional arguments can be used with the `cloud-backup-delete` command:

|**Argument**|**Description**|**Default**|**Example**|
|------------|---------------|-----------|-----------|
|`source_url`|URL of the cloud source, such as a bucket in AWS S3.| |`s3://bucket/path/to/folder`|
|`server_name`|The name of the server as configured in Barman| | |

## Optional arguments

The following optional arguments can be used with the `cloud-backup-delete` command:

|**Argument**|**Description**|**Default**|**Example**|
|------------|---------------|-----------|-----------|
|`\--help`|Show the help message and exit| | |
|`\-v, --verbose`|Increase output verbosity (`-vv` is more than `-v`)| | |
|`\-V, --version`|Show the version number and exit| | |
|`\-q, --quiet`|Decrease output verbosity (`-qq` is less than `-q`)| | |
|`\-t, --test`|Test cloud connectivity and exit| | |
|`\--cloud-provider {aws-s3,azure-blob-storage,google-cloud-storage}`|The cloud provider to use as a storage backend| | |
|`\-b BACKUP_ID, --backup-id BACKUP_ID`|Backup ID of the backup to be deleted| | |
|`\-m MINIMUM_REDUNDANCY, --minimum-redundancy MINIMUM_REDUNDANCY`|The minimum number of backups that should always be available.
|`\-r RETENTION_POLICY, --retention-policy RETENTION_POLICY`|If specified, delete all backups eligible for deletion according to the supplied retention policy. Syntax: `REDUNDANCY value | RECOVERY WINDOW OF value {DAYS \|WEEKS \| MONTHS}`| | |
|`\--dry-run`|Find the objects which need to be deleted but do not delete them| | |
|`\--batch-size DELETE_BATCH_SIZE`|The maximum number of objects to be deleted in a single request to the cloud provider. If unset then the maximum allowed batch size for the specified cloud provider will be used (1000 for aws-s3, 256 for azure-blob-storage and 100 for google-cloud-storage).| | |

##  Extra options 
The following extra options can be used with the `cloud-backup-delete` command for the following cloud providers:

### aws-s3

The following extra options can be used with the `cloud-backup-delete` command for **aws-s3**:

|**Argument**|**Description**|**Default**|**Example**|
|------------|---------------|-----------|-----------|
|`\--endpoint-url ENDPOINT_URL`|Override default S3 endpoint URL with the given one|| |
|`\-P AWS_PROFILE, --aws-profile AWS_PROFILE`|Profile name (e.g. `INI` section in the AWS credentials file)|| |
|`\--profile AWS_PROFILE`|Profile name (deprecated: replaced by `--aws-profile`)|| |
|`\--read-timeout READ_TIMEOUT`|The time in seconds until a timeout is raised when waiting to read from a connection|60 seconds| |

### azure-blob-storage

The following extra options can be used with the `cloud-backup-delete` command for **azure-blob-storage**:

|**Argument**|**Description**|**Default**|**Example**|
|------------|---------------|-----------|-----------|
|`\--azure-credential {azure-cli,managed-identity}, --credential {azure-cli,managed-identity}`|Optionally specify the type of credential to use when authenticating with Azure. If omitted then Azure Blob Storage credentials will be obtained from the environment and the default Azure authentication flow will be used for authenticating with all other Azure services. If no credentials can be found in the environment then the default Azure authentication flow will also be used for Azure Blob Storage.| | |

### google-cloud-storage

No extra options can be used with the `cloud-backup-delete` command for **google-cloud-storage**.

## Dependencies

The following dependencies apply to the `cloud-backup-delete` command:

|**Cloud provider**|**Dependencies**|
|------------------|----------------|
|aws-s3|boto3|
|azure-blob-storage|azure-storage-blob, azure-identity *(optional, if you wish to use `DefaultAzureCredential`)*|
|google-cloud-storage|google-cloud-storage|

## Exit status

|**Exit code**|**Description**|
|-------------|---------------|
|0|Success|
|1|The delete was not successful|
|2|The connection to the cloud provider failed|
|3|There was an error in the command input|
|Other non-zero code|Failure|
