# Cloud wal-archive

|**Command** | **Category** |  **Description**| **Synopsis**|
|------------|--------------|-----------------|----------|
|`cloud-wal-archive`|Cloud|Archive PostgreSQL WAL files in the Cloud using `archive_command`|`barman-cloud-wal-archive [*OPTIONS*] *DESTINATION_URL* *SERVER_NAME* *WAL_PATH*`|

## Supported cloud providers

`cloud-wal-archive` is supported for the following cloud providers:

* AWS Simple Storage Service (S3)
* Azure Blob Storage
* Google Cloud Storage

## Details
This script can be used in the `archive_command` of a PostgreSQL server to ship WAL files to the Cloud.

!!!Note
    If you're running python 2 or older unsupported versions of python 3, then avoid the compression options `--gzip` or `--bzip2`.  `barman-cloud-wal-restore` isn't able to restore gzip-compressed WALs on python versions under 3.2 or bzip2-compressed WALs on python versions under 3.3.

## Usage

```bash
barman-cloud-wal-archive [-V] [--help] [-v | -q] [-t]
[--cloud-provider {aws-s3,azure-blob-storage,google-cloud-storage}]
[--endpoint-url ENDPOINT_URL] [-P AWS_PROFILE]
[--profile AWS_PROFILE]
[--read-timeout READ_TIMEOUT]
[--azure-credential {azure-cli,managed-identity}]
[-z | -j | --snappy]
[--tags [TAGS [TAGS ...]]]
[--history-tags [HISTORY_TAGS [HISTORY_TAGS ...]]]
[--kms-key-name KMS_KEY_NAME] [-e ENCRYPTION]
[--sse-kms-key-id SSE_KMS_KEY_ID]
[--encryption-scope ENCRYPTION_SCOPE]
[--max-block-size MAX_BLOCK_SIZE]
[--max-concurrency MAX_CONCURRENCY]
[--max-single-put-size MAX_SINGLE_PUT_SIZE]
destination_url server_name [wal_path]
```

## Positional arguments

The following positional arguments can be used with the `cloud-wal-archive` command:

|**Argument**|**Description**|**Default**|**Example**|
|------------|---------------|-----------|-----------|
|`destination_url`|URL of the cloud destination, such as a bucket in AWS S3.| |`s3://bucket/path/to/folder`|
|`server_name`|The name of the server as configured in Barman.| | |
|`wal_path`|The value of the `%p` keyword (according to `archive_command`).| | |

## Optional arguments

The following optional arguments can be used with the `cloud-wal-archive` command:

|**Argument**|**Description**|**Default**|**Example**|
|------------|---------------|-----------|-----------|
|`\--help`|Show the help message and exit| | |
|`\-v, --verbose`|Increase output verbosity (`-vv` is more than `-v`)| | |
|`\-V, --version`|Show the version number and exit| | |
|`\-q, --quiet`|Decrease output verbosity (`-qq` is less than `-q`)| | |
|`\-t, --test`|Test cloud connectivity and exit| | |
|`\--cloud-provider {aws-s3,azure-blob-storage,google-cloud-storage}`|The cloud provider to use as a storage backend| | |
|`-z, --gzip`|gzip-compress the WAL while uploading to the cloud (should not be used with python < 3.2)| | |
|`-j, --bzip2`|bzip2-compress the WAL while uploading to the cloud (should not be used with python < 3.3)| | |
|`--snappy`|snappy-compress the WAL while uploading to the cloud (requires optional python-snappy library)| | |
|`--tags [TAGS [TAGS ...]]`|Tags to be added to archived WAL files in cloud storage| | |
|`--history-tags [HISTORY_TAGS [HISTORY_TAGS ...]]`|Tags to be added to archived history files in cloud storage| | |

## Extra options 
The following extra options can be used with the `cloud-wal-archive` command for the following cloud providers:

### aws-s3

The following extra options can be used with the `cloud-wal-archive` command for **aws-s3**:

|**Argument**|**Description**|**Default**|**Example**|
|------------|---------------|-----------|-----------|
|`\--endpoint-url ENDPOINT_URL`|Override default S3 endpoint URL with the given one|| |
|`\-P AWS_PROFILE, --aws-profile AWS_PROFILE`|Profile name (e.g. `INI` section in the AWS credentials file)|| |
|`\--profile AWS_PROFILE`|Profile name (deprecated: replaced by `--aws-profile`)|| |
|`\--read-timeout READ_TIMEOUT`|The time in seconds until a timeout is raised when waiting to read from a connection|60 seconds| |
|`\-e {AES256,aws:kms}, --encryption {AES256,aws:kms}`|The encryption algorithm used when storing the uploaded data in S3. Allowed values: 'AES256'\|'aws:kms'.| | |
|`\--sse-kms-key-id SSE_KMS_KEY_ID`|The AWS KMS key ID that should be used for encrypting the uploaded data in S3. Can be specified using the key ID on its own or using the full ARN for the key. Only allowed if `\`-e/--encryption\` is set to \`aws:kms\`.| | |

### azure-blob-storage

The following extra options can be used with the `cloud-wal-archive` command for **azure-blob-storage**:

|**Argument**|**Description**|**Default**|**Example**|
|------------|---------------|-----------|-----------|
|`\--azure-credential {azure-cli,managed-identity}, --credential {azure-cli,managed-identity}`|Optionally specify the type of credential to use when authenticating with Azure. If omitted then Azure Blob Storage credentials will be obtained from the environment and the default Azure authentication flow will be used for authenticating with all other Azure services. If no credentials can be found in the environment then the default Azure authentication flow will also be used for Azure Blob Storage.| | |
|`\--encryption-scope ENCRYPTION_SCOPE`|The name of an encryption scope defined in the Azure Blob Storage service which is to be used to encrypt the data in Azure| | |
|`--max-block-size MAX_BLOCK_SIZE`|The chunk size to be used when uploading an object via the concurrent chunk method|4MB| |
|`--max-concurrency MAX_CONCURRENCY`|The maximum number of chunks to be uploaded concurrently|1| |
|`--max-single-put-size MAX_SINGLE_PUT_SIZE`|Maximum size for which the Azure client will upload an object in a single request. If this is set lower than the PostgreSQL WAL segment size after any applied compression then the concurrent chunk upload method for WAL archiving will be used.|64MB| |

### google-cloud-storage

The following extra options can be used with the `cloud-wal-archive` command for **google-cloud-storage**:

|**Argument**|**Description**|**Default**|**Example**|
|------------|---------------|-----------|-----------|
|`\--kms-key-name KMS_KEY_NAME`|The name of the GCP KMS key which should be used for encrypting the uploaded data in GCS| | |

## Dependencies

The following dependencies apply to the `cloud-wal-archive` command:

|**Cloud provider**|**Dependencies**|
|------------------|----------------|
|aws-s3|boto3|
|azure-blob-storage|azure-storage-blob, azure-identity *(optional, if you wish to use `DefaultAzureCredential`)*|
|google-cloud-storage|google-cloud-storage|

## Exit status

|**Exit code**|**Description**|
|-------------|---------------|
|0|Success|
|1|The wal-archive was not successful|
|2|The connection to the cloud provider failed|
|3|There was an error in the command input|
|Other non-zero code|Failure|


## Additional information

This script can be used in conjunction with `pre_archive_retry_script` to relay WAL files to S3, as follows:

```bash
pre_archive_retry_script = 'barman-cloud-wal-archive [*OPTIONS*] *DESTINATION_URL* ${BARMAN_SERVER}'
```
