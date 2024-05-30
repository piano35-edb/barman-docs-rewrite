
# Cloud restore

|**Command** | **Category** |  **Description**| **Synopsis**|
|------------|--------------|-----------------|----------|
|`cloud-restore`|Cloud|Restore a PostgreSQL backup from the Cloud.|`barman-cloud-restore [*OPTIONS*] *SOURCE_URL* *SERVER_NAME* *BACKUP_ID* *RECOVERY_DIR*`|

# Supported cloud providers

`cloud-restore` is supported for the following cloud providers:

* AWS Simple Storage Service (S3)
* Azure Blob Storage
* Google Cloud Storage

# Details

This script can be used to download a backup previously made with `barman-cloud-backup`.  It can also be used to prepare for recovery from a snapshot backup by checking the attached disks were cloned from the correct snapshots and downloading the backup label from object storage. 

# Usage

```bash
barman-cloud-restore [-V] [--help] [-v | -q] [-t]
[--cloud-provider {aws-s3,azure-blob-storage,google-cloud-storage}]
[--endpoint-url ENDPOINT_URL] [-P AWS_PROFILE]
[--profile AWS_PROFILE]
[--read-timeout READ_TIMEOUT]
[--azure-credential {azure-cli,managed-identity}]
[--tablespace NAME:LOCATION]
[--snapshot-recovery-instance SNAPSHOT_RECOVERY_INSTANCE]
[--snapshot-recovery-zone GCP_ZONE]
[--aws-region AWS_REGION] [--gcp-zone GCP_ZONE]
[--azure-resource-group AZURE_RESOURCE_GROUP]
source_url server_name backup_id recovery_dir
```

# Positional arguments

The following positional arguments can be used with the `cloud-restore` command:

|**Argument**|**Description**|**Default**|**Example**|
|------------|---------------|-----------|-----------|
|`source_url`|URL of the cloud source, such as a bucket in AWS S3.| |`s3://bucket/path/to/folder`|
|`server_name`|The name of the server as configured in Barman| | |
|`backup_id`|The backup id| | |
|`recovery_dir`|The path to a directory for recovery| | |

# Optional arguments

The following optional arguments can be used with the `cloud-restore` command:

|**Argument**|**Description**|**Default**|**Example**|
|------------|---------------|-----------|-----------|
|`\--help`|Show the help message and exit| | |
|`\-v, --verbose`|Increase output verbosity (`-vv` is more than `-v`)| | |
|`\-V, --version`|Show the version number and exit| | |
|`\-q, --quiet`|Decrease output verbosity (`-qq` is less than `-q`)| | |
|`\-t, --test`|Test cloud connectivity and exit| | |
|`\--cloud-provider {aws-s3,azure-blob-storage,google-cloud-storage}`|The cloud provider to use as a storage backend| | |
|`\--tablespace NAME:LOCATION`|Tablespace relocation rule| | |
|`\--snapshot-recovery-instance SNAPSHOT_RECOVERY_INSTANCE`|Instance where the disks recovered from the snapshots are attached| | |
`\--snapshot-recovery-zone GCP_ZONE`|Zone containing the instance and disks for the snapshot recovery (deprecated: replaced by `--gcp-zone`)| | |

# Extra options 
The following extra options can be used with the `cloud-restore` command for the following cloud providers:

**aws-s3**

The following extra options can be used with the `cloud-restore` command for **aws-s3**:

|**Argument**|**Description**|**Default**|**Example**|
|------------|---------------|-----------|-----------|
|`\--endpoint-url ENDPOINT_URL`|Override default S3 endpoint URL with the given one|| |
|`\-P AWS_PROFILE, --aws-profile AWS_PROFILE`|profile name (e.g. `INI` section in the AWS credentials file)|| |
|`\--profile AWS_PROFILE`|profile name (deprecated: replaced by `--aws-profile`)|| |
|`\--read-timeout READ_TIMEOUT`|the time in seconds until a timeout is raised when waiting to read from a connection|60 seconds| |
|`\--aws-region AWS_REGION`|The name of the AWS region containing the EC2 VM and storage volumes defined by the `--snapshot-instance` and `--snapshot-disk` arguments.| | |

**azure-blob-storage**

The following extra options can be used with the `cloud-restore` command for **azure-blob-storage**:

|**Argument**|**Description**|**Default**|**Example**|
|------------|---------------|-----------|-----------|
|`\--azure-credential {azure-cli,managed-identity}, --credential {azure-cli,managed-identity}`|Optionally specify the type of credential to use when authenticating with Azure. If omitted then Azure Blob Storage credentials will be obtained from the environment and the default Azure authentication flow will be used for authenticating with all other Azure services. If no credentials can be found in the environment then the default Azure authentication flow will also be used for Azure Blob Storage.| | |
|`\--azure-resource-group AZURE_RESOURCE_GROUP`|The name of the Azure resource group to which the compute instance and disks defined by the `--snapshot-instance` and `--snapshot-disk` arguments belong.| | |

**google-cloud-storage**

The following extra options can be used with the `cloud-restore` command for **google-cloud-storage**:

|**Argument**|**Description**|**Default**|**Example**|
|------------|---------------|-----------|-----------|
|`\--gcp-zone GCP_ZONE`|Zone containing the instance and disks for the snapshot recovery| | |


# Dependencies

The following dependencies apply to the `cloud-restore` command:

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
|1|The restore was not successful              |
|2|The connection to the cloud provider failed             |
|3|There was an error in the command input             |
|Other non-zero code|Failure      |


