
# Using Cloud commands

Barman client utilities (`barman-cli-cloud`) have been extended to support object storage integration and enhance disaster recovery capabilities of your PostgreSQL databases by relaying WAL files and backups to a supported cloud provider.

These utilities are distributed in the `barman-cli-cloud` RPM/Debian package, and can be installed alongside the PostgreSQL server.

## Cloud providers
Cloud commands are supported for the following cloud providers:

* AWS Simple Storage Service (S3) (or any S3 compatible object store)
* Azure Blob Storage
* Google Cloud Storage (Rest API)

!!!info
    Some cloud commands may not be supported for all cloud providers.  For more information, see the **Supported cloud providers** section in each cloud command page.

## Dependencies

Cloud commands have a dependency on the appropriate library for the cloud provider you wish to use.  Exact dependencies are listed on the page for each cloud command.  In general, the following dependencies apply:

|**Cloud provider**|**Dependencies**|
|------------------|----------------|
|aws-s3|boto3|
|azure-blob-storage|azure-storage-blob, azure-identity *(optional, if you wish to use `DefaultAzureCredential`)*|
|google-cloud-storage|google-cloud-storage|

Use the `--cloud-provider` option to choose the cloud provider for your backups and WALs.

## Credentials
For information on how to setup credentials for the `aws-s3` cloud provider see the *"Credentials"* section in Boto 3 documentation.

For credentials for the `azure-blob-storage` cloud provider see the *"Environment variables for authorization parameters"* section in the Azure documentation. 

The following Azure environment variables are supported: 

- `AZURE_STORAGE_CONNECTION_STRING`
- `AZURE_STORAGE_KEY`
- `AZURE_STORAGE_SAS_TOKEN`

You can also use the`--credential` option to specify either `azure-cli`or managed-identity credentials in order to authenticate via Azure Active Directory.

## Installation

`barman-cli-cloud` needs to be installed on those PostgreSQL servers that you want to directly backup to a cloud provider, bypassing Barman.

If you want to use `barman-cloud-backup` and/or `barman-cloud-wal-archive` as hook scripts, you can install the `barman-cli-cloud` package on the Barman server also.

See the Barman "Installation" section to install the repositories.

RedHat/CentOS:  (as root)
```bash
yum install barman-cli-cloud
```
Debian/Ubuntu:  (as root)
```bash
apt-get install barman-cli-cloud
```

## Usage

All commands should be prefaced with `barman-cloud-`.  For example `barman-cloud-backup-keep`

## Cloud command index
The following list includes the cloud commands.


|**Command** | **Category** |  **Description**|
|------------|--------------|-----------------|
|`cloud-backup`|Cloud|Perform a backup of a local PostgreSQL instance and ship the resulting tarball(s) to the cloud.|      
|`cloud-backup-delete`|Cloud|Delete backups previously made with the `barman-cloud-backup` command.|      
|`cloud-backup-keep`|Cloud|Flag backups previously made with `barman-cloud-backup` as archival backups.|       
|`cloud-backup-list`|Cloud|List backups previously made with `barman-cloud-backup` command.|       
|`cloud-backup-show`|Cloud|Display metadata for backups previously made with the `barman-cloud-backup` command.|       
|`cloud-check-wal-archive`|Cloud|Check that the WAL archive destination for *SERVER_NAME* is safe to use for a new PostgreSQL cluster.|       
|`cloud-wal-archive`|Cloud|Used with the `archive` command of a PostgreSQL server to ship WAL files to the Cloud.|       
|`cloud-wal-restore`|Cloud|Used as a `restore` command to download WAL files previously archived with `barman-cloud-wal-archive` command.|       
|`cloud-restore`| Cloud|Download a backup previously made with `barman-cloud-backup command`.|       

## References
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