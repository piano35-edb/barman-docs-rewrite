
# Cloud template

|**Command** | **Category** |  **Description**| **Synopsis**|
|------------|--------------|-----------------|----------|
|`cloud-backup`|Cloud|Perform a backup of a local PostgreSQL instance and ship the resulting tarball(s) to the cloud.|`barman-cloud-backup [*OPTIONS*] *DESTINATION_URL* *SERVER_NAME*`|

<!--
# Name
barman-cloud-backup
# Description
This script can be used to perform a backup of a local PostgreSQL instance and ship the resulting tarball(s) to the cloud.
# Synopsis
```bash
barman-cloud-backup [*OPTIONS*] *DESTINATION_URL* *SERVER_NAME*
```
-->
# Requirements and limitations

The following requirements and limitations apply to the `barman-cloud-backup` command:

- It requires read access to *PGDATA* and tablespaces *(normally run as postgres user)*. 
- It can also be used as a hook script on a barman server, in which case it requires read access to the directory where barman backups are stored.
- If the arguments prefixed with `--snapshot-` are used, and snapshots are supported for the selected cloud provider, then the backup will be performed using snapshots of the disks specified using `--snapshot-disk arguments`. The backup label and backup metadata will be uploaded to the cloud object store.

!!!warning
    The cloud upload process may fail if any file with a size greater than the configured `--max-archive-size` is present either in the data directory or in any tablespaces. However, PostgreSQL creates files with a maximum size of 1GB, and that size is always allowed, regardless of the `max-archive-size` parameter.

# Usage

```bash
barman-cloud-backup [-V] [--help] [-v \| -q] [-t]

[--cloud-provider {aws-s3,azure-blob-storage,google-cloud-storage}]

[--endpoint-url ENDPOINT_URL] [-P AWS_PROFILE]

[--profile AWS_PROFILE]

[--read-timeout READ_TIMEOUT]

[--azure-credential {azure-cli,managed-identity}]

[-z \| -j \| --snappy] [-h HOST] [-p PORT] [-U USER]

[--immediate-checkpoint] [-J JOBS]

[-S MAX_ARCHIVE_SIZE]

[--min-chunk-size MIN_CHUNK_SIZE]

[--max-bandwidth MAX_BANDWIDTH] [-d DBNAME]

[-n BACKUP_NAME]

[--snapshot-instance SNAPSHOT_INSTANCE]

[--snapshot-disk NAME] [--snapshot-zone GCP_ZONE]

[--snapshot-gcp-project GCP_PROJECT]

[--gcp-project GCP_PROJECT]

[--kms-key-name KMS_KEY_NAME] [--gcp-zone GCP_ZONE]

[--tags [TAGS [TAGS ...]]] [-e {AES256,aws:kms}]

[--sse-kms-key-id SSE_KMS_KEY_ID]

[--aws-region AWS_REGION]

[--encryption-scope ENCRYPTION_SCOPE]

[--azure-subscription-id AZURE_SUBSCRIPTION_ID]

[--azure-resource-group AZURE_RESOURCE_GROUP]
```

# Positional arguments

The following positional arguments can be used with the `cloud-backup` command:

|**Argument**|**Description**|**Default**|**Example**|
|------------|---------------|-----------|-----------|
|`destination_url`|URL of the cloud destination, such as a bucket in AWS S3.| |\`s3://bucket/path/to/folder\`|
|`destination_url server_name`| | | |
|`server_name`:|The name of the server as configured in Barman| | |

# Optional arguments

The following optional arguments can be used with the `cloud-backup` command:

|**Argument**|**Description**|**Default**|**Example**|
|------------|---------------|-----------|-----------|
|`\--help`|Show this help message and exit| | |
|`\-v, --verbose`|Increase output verbosity (`-vv` is more than `-v`)| | |
|`\-V, --version`|Show program's version number and exit| | |
|`\-q, --quiet`|Decrease output verbosity (`-qq` is less than `-q`)| | |
|`\-t, --test`|Test cloud connectivity and exit| | |
|`\--cloud-provider {aws-s3,azure-blob-storage,google-cloud-storage}`|The cloud provider to use as a storage backend| | |
|`\-z, --gzip`|gzip-compress the backup while uploading to the cloud| | |
|`\-j, --bzip2`|bzip2-compress the backup while uploading to the cloud| | |
|`\--snappy`|snappy-compress the backup while uploading to the cloud| | |
|`\-h HOST, --host HOST`|host or Unix socket for PostgreSQL connection|libpq settings| |
|`-p PORT, --port PORT`|port for PostgreSQL connection|libpq settings| |
|`\-U USER, --user USER`|user name for PostgreSQL connection|libpq settings | |
|`\--immediate-checkpoint`|Forces the initial checkpoint to be done as quickly as possible| | |
|`\-J JOBS, --jobs JOBS `|Number of subprocesses to upload data to cloud storage|2| |
|`\-S MAX_ARCHIVE_SIZE, --max-archive-size MAX_ARCHIVE_SIZE`|Maximum size of an archive when uploading to cloud storage|100GB | |
|`\--min-chunk-size MIN_CHUNK_SIZE`|Minimum size of an individual chunk when uploading to cloud storage|5MB for aws-s3, 64KB for azure-blob-storage, not applicable for google-cloud-storage | |
|`\--max-bandwidth MAX_BANDWIDTH`|The maximum amount of data to be uploaded per second when backing up to either AWS S3 or Azure Blob Storage|no limit| |
|`\-d DBNAME, --dbname DBNAME`|Database name or conninfo string for Postgres connection|postgres| |
|`\-n BACKUP_NAME, --name BACKUP_NAME`|A name which can be used to reference this backup in commands such as `barman-cloud-restore` and `barman-cloud-backup-delete`| | |
|`\--snapshot-instance SNAPSHOT_INSTANCE`|Instance where the disks to be backed up as snapshots are attached| | |
|`\--snapshot-disk NAME`|Name of a disk from which snapshots should be taken| | |
|`\--snapshot-zone GCP_ZONE`| Zone of the disks from which snapshots should be taken *(deprecated: replaced by `--gcp-zone`)*| | |
|`\--tags [TAGS [TAGS ...]]`|Tags to be added to all uploaded files in cloud storage| | |

# Extra options 
The following extra options can be used with the `cloud-backup` command for the following cloud providers:

**aws-s3**

The following extra options can be used with the `cloud-backup` command for **aws-s3**:

|**Argument**|**Description**|**Default**|**Example**|
|------------|---------------|-----------|-----------|
|`\--endpoint-url ENDPOINT_URL`|Override default S3 endpoint URL with the given one|| |
|`\-P AWS_PROFILE, --aws-profile AWS_PROFILE`|profile name (e.g. `INI` section in the AWS credentials file)|| |
|`\--profile AWS_PROFILE`|profile name (deprecated: replaced by `--aws-profile`)|| |
|`\--read-timeout READ_TIMEOUT`|the time in seconds until a timeout is raised when waiting to read from a connection|60 seconds| |
|`\-e {AES256,aws:kms}, --encryption {AES256,aws:kms}`|The encryption algorithm used when storing the uploaded data in S3. Allowed values: 'AES256'\|'aws:kms'.| | |
|`\--sse-kms-key-id SSE_KMS_KEY_ID`|The AWS KMS key ID that should be used for encrypting the uploaded data in S3. Can be specified using the key ID on its own or using the full ARN for the key. Only allowed if `\`-e/--encryption\` is set to \`aws:kms\`.| | |
|`\--aws-region AWS_REGION`|The name of the AWS region containing the EC2 VM and storage volumes defined by the `--snapshot-instance` and `--snapshot-disk` arguments.| | |

**azure-blob-storage**

The following extra options can be used with the `cloud-backup` command for **azure-blob-storage**:

|**Argument**|**Description**|**Default**|**Example**|
|------------|---------------|-----------|-----------|
|`\--azure-credential {azure-cli,managed-identity}, --credential {azure-cli,managed-identity}`|Optionally specify the type of credential to use when authenticating with Azure. If omitted then Azure Blob Storage credentials will be obtained from the environment and the default Azure authentication flow will be used for authenticating with all other Azure services. If no credentials can be found in the environment then the default Azure authentication flow will also be used for Azure Blob Storage.| | |
|`\--encryption-scope ENCRYPTION_SCOPE`|The name of an encryption scope defined in the Azure Blob Storage service which is to be used to encrypt the data in Azure| | |
|`\--azure-subscription-id AZURE_SUBSCRIPTION_ID`|The ID of the Azure subscription which owns the instance and storage volumes defined by the `\--snapshot-instance` and `--snapshot-disk` arguments.| | |
|`\--azure-resource-group AZURE_RESOURCE_GROUP`|The name of the Azure resource group to which the compute instance and disks defined by the `--snapshot-instance` and `--snapshot-disk` arguments belong.| | |

**google-cloud-storage**

The following extra options can be used with the `cloud-backup` command for **google-cloud-storage**:

|**Argument**|**Description**|**Default**|**Example**|
|------------|---------------|-----------|-----------|
|`\--snapshot-gcp-project GCP_PROJECT`|GCP project under which disk snapshots should be stored *(deprecated: replaced by `--gcp-project`)*| | |
|`\--gcp-project GCP_PROJECT`|GCP project under which disk snapshots should be stored| | |
|`\--kms-key-name KMS_KEY_NAME`|The name of the GCP KMS key which should be used for encrypting the uploaded data in GCS| | |
|`\--gcp-zone GCP_ZONE`|Zone of the disks from which snapshots should be taken| | |

# References

For more information on configuring your environment, see the following references:

- [Boto](https://boto3.amazonaws.com/v1/documentation/api/latest/guide/configuration.html)
- [AWS CLI Setup](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-set-up.html)
- [AWS CLI Getting Started](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html)
- [Azure Blob Storage Environment Variables](https://docs.microsoft.com/en-us/azure/storage/blobs/authorize-data-operations-cli\#set-environment-variables-for-authorization-parameters)
- [Azure Blob Storage Using Python](https://docs.microsoft.com/en-us/python/api/azure-storage-blob/?view=azure-python)
- [**libpq** Environment Variables](https://www.postgresql.org/docs/current/libpq-envars.html)
- [Google Cloud Storage Credentials](https://cloud.google.com/docs/authentication/getting-started\#setting_the_environment_variable)

    !!!info
        Only authentication with `GOOGLE_APPLICATION_CREDENTIALS` env is supported when using Google cloud storage credentials.

# Dependencies

The following dependencies apply to the `cloud-backup` command:

|**Cloud provider**|**Dependencies**|
|------------------|----------------|
|aws-s3|boto3|
|azure-blob-storage|azure-storage-blob, azure-identity *(optional, if you wish to use `DefaultAzureCredential`)*|
|google-cloud-storage|\* google-cloud-storage|
|google-cloud-storage *(with snapshot backups)*|grpcio, google-cloud-compute|

# Exit status

|**Exit code**|**Description**|
|-------------|---------------|
|0|    Success   |
|1|The backup was not successful              |
|2|The connection to the cloud provider failed             |
|3|There was an error in the command input             |
|Other non-zero code|Failure      |



# Additional information

This script can be used in conjunction with **post_backup_script** or **post_backup_retry_script** to relay barman backups to cloud storage as follows:

```bash
post_backup_retry_script = 'barman-cloud-backup [\*OPTIONS\*] \*DESTINATION_URL\* \${BARMAN_SERVER}'
```
When running as a hook script, `barman-cloud-backup` will read the location of the backup directory and the backup ID from **BACKUP_DIR** and **BACKUP_ID** environment variables set by barman.


<!--------------------------------->
<!--------------------------------->
<!----------ORIGINAL BELOW--------->
<!--------------------------------->
<!--------------------------------->