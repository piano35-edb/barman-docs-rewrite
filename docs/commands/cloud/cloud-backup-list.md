# Cloud backup list

|**Command** | **Category** |  **Description**| **Synopsis**|
|------------|--------------|-----------------|----------|
|`cloud-backup-list`|Cloud|List backups stored in the Cloud|`barman-cloud-backup-list [*OPTIONS*] *SOURCE_URL* *SERVER_NAME*`|

## Supported cloud providers

`cloud-backup-list` is supported for the following cloud providers:

* AWS Simple Storage Service (S3)
* Azure Blob Storage
* Google Cloud Storage

## Details
This script can be used to list backups previously made with `barman-cloud-backup`.

## Usage

```bash
barman-cloud-backup-list [-V] [--help] [-v | -q] [-t]
[--cloud-provider {aws-s3,azure-blob-storage,google-cloud-storage}]
[--endpoint-url ENDPOINT_URL]
[-P AWS_PROFILE] [--profile AWS_PROFILE]
[--read-timeout READ_TIMEOUT]
[--azure-credential {azure-cli,managed-identity}]
[--format FORMAT]
source_url server_name
```

## Positional arguments

The following positional arguments can be used with the `cloud-backup-list` command:

|**Argument**|**Description**|**Default**|**Example**|
|------------|---------------|-----------|-----------|
|`source_url`|URL of the cloud source, such as a bucket in AWS S3.| |`s3://bucket/path/to/folder`|
|`server_name`|The name of the server as configured in Barman.| | |

## Optional arguments

The following optional arguments can be used with the `cloud-backup-list` command:

|**Argument**|**Description**|**Default**|**Example**|
|------------|---------------|-----------|-----------|
|`\--help`|Show the help message and exit| | |
|`\-v, --verbose`|Increase output verbosity (`-vv` is more than `-v`)| | |
|`\-V, --version`|Show the version number and exit| | |
|`\-q, --quiet`|Decrease output verbosity (`-qq` is less than `-q`)| | |
|`\-t, --test`|Test cloud connectivity and exit| | |
|`--format FORMAT`|Output format (console or json)|console| |
|`\--cloud-provider {aws-s3,azure-blob-storage,google-cloud-storage}`|The cloud provider to use as a storage backend| | |

## Extra options 
The following extra options can be used with the `cloud-backup-list` command for the following cloud providers:

### aws-s3

The following extra options can be used with the `cloud-backup-list` command for **aws-s3**:

|**Argument**|**Description**|**Default**|**Example**|
|------------|---------------|-----------|-----------|
|`\--endpoint-url ENDPOINT_URL`|Override default S3 endpoint URL with the given one|| |
|`\-P AWS_PROFILE, --aws-profile AWS_PROFILE`|Profile name (e.g. `INI` section in the AWS credentials file)|| |
|`\--profile AWS_PROFILE`|Profile name (deprecated: replaced by `--aws-profile`)|| |
|`\--read-timeout READ_TIMEOUT`|The time in seconds until a timeout is raised when waiting to read from a connection|60 seconds| |

### azure-blob-storage

The following extra options can be used with the `cloud-backup-list` command for **azure-blob-storage**:

|**Argument**|**Description**|**Default**|**Example**|
|------------|---------------|-----------|-----------|
|`\--azure-credential {azure-cli,managed-identity}, --credential {azure-cli,managed-identity}`|Optionally specify the type of credential to use when authenticating with Azure. If omitted then Azure Blob Storage credentials will be obtained from the environment and the default Azure authentication flow will be used for authenticating with all other Azure services. If no credentials can be found in the environment then the default Azure authentication flow will also be used for Azure Blob Storage.| | |

### google-cloud-storage

No extra options can be used with the `cloud-backup-list` command for **google-cloud-storage**.

## Dependencies

The following dependencies apply to the `cloud-backup-list` command:

|**Cloud provider**|**Dependencies**|
|------------------|----------------|
|aws-s3|boto3|
|azure-blob-storage|azure-storage-blob, azure-identity *(optional, if you wish to use `DefaultAzureCredential`)*|
|google-cloud-storage|google-cloud-storage|

## Exit status

|**Exit code**|**Description**|
|-------------|---------------|
|0|Success|
|1|The list was not successful|
|2|The connection to the cloud provider failed|
|3|There was an error in the command input|
|Other non-zero code|Failure|


