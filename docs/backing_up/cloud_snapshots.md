Barman can create backups of PostgreSQL servers deployed within certain cloud environments by taking snapshots of storage volumes. In this scenario, the physical backups of PostgreSQL files are volume snapshots stored in the cloud, and Barman acts as a storage server for WALs and the backup catalog. These backups can then be managed by Barman just like traditional backups taken with the rsync or postgres backup methods. even though the backup data itself is stored in the cloud.

You can create snapshot backups without a Barman server using the `barman-cloud-backup` command directly on a PostgreSQL server.

## Requirements

To use the snapshot backup method with Barman, deployments must meet the following requirements:

-   PostgreSQL must be deployed on a compute instance within a supported cloud provider.
-   PostgreSQL must be configured such that all critical data, such as `PGDATA` and any tablespace data, is stored on storage volumes which support snapshots.
-   The `findmnt` command must be available on the PostgreSQL host.

!!!IMPORTANT
    Any configuration files stored outside of `PGDATA` won't be included in the snapshots. The management of those files must be carried out using another mechanism such as a configuration management system.

### Google Cloud Platform snapshot prerequisites

The `google-cloud-compute` and `grpcio` libraries must be available to the Python distribution used by Barman. These libraries are an optional dependency and aren't installed by any of the Barman packages. You can install them using pip:
```bash
pip3 install grpcio google-cloud-compute
```
!!!NOTE
    The minimum version of Python required by the `google-cloud-compute` library is 3.7. GCP snapshots can't be used with earlier versions of Python.

The following additional requirements apply to snapshot backups on Google Cloud Platform:

-   All disks included in the snapshot backup must be zonal persistent disks. Regional persistent disks aren't supported.
-   A service account with the required set of permissions must be available to Barman. This can be achieved by attaching such an account to the compute instance running Barman (recommended) or by using the `GOOGLE_APPLICATION_CREDENTIALS` environment variable to point to a credentials file.

### Required permissions for GCP

The required permissions are:

-   compute.disks.createSnapshot
-   compute.disks.get
-   compute.globalOperations.get
-   compute.instances.get
-   compute.snapshots.create
-   compute.snapshots.delete
-   compute.snapshots.list

### Azure snapshot requirements

The `azure-mgmt-compute` and `azure-identity` libraries must be available to the Python distribution used by Barman.

These libraries are an optional dependency and aren't installed by any of the Barman packages. You can install them using pip:
```bash
pip3 install azure-mgmt-compute azure-identity
```
!!!NOTE
    The minimum version of Python required by the `azure-mgmt-compute` library is 3.7. Azure snapshots can't be used with earlier versions of Python.

The following additional requirements apply to snapshot backups on Azure:

-   All disks included in the snapshot backup must be managed disks which are attached to the VM instance as data disks.
-   Barman must be able to use a credential obtained either using managed identity or CLI login and this must grant access to Azure with the required set of permissions.

### Required permissions for Azure

The following permissions are required:

-   Microsoft.Compute/disks/read
-   Microsoft.Compute/virtualMachines/read
-   Microsoft.Compute/snapshots/read
-   Microsoft.Compute/snapshots/write
-   Microsoft.Compute/snapshots/delete

### AWS snapshot prerequisites

The `boto3` library must be available to the Python distribution used by Barman.

This library is an optional dependency and not installed by any of the Barman packages. You can install it using pip:
```bash
pip3 install boto3
```
The following additional requirements and limitations apply to snapshot backups on AWS:

-   All disks included in the snapshot backup must be non-root EBS volumes and must be attached to the same VM instance.
-   NVMe volumes aren't supported.

### Required permissions for AWS

The following permissions are required:

-   ec2:CreateSnapshot
-   ec2:CreateTags
-   ec2:DeleteSnapshot
-   ec2:DescribeSnapshots
-   ec2:DescribeInstances
-   ec2:DescribeVolumes

## Configuration for snapshot backups

To configure Barman for backup via cloud snapshots, set the `backup_method` parameter to snapshot and set `snapshot_provider` to a supported cloud provider:
```bash
backup_method = snapshot
snapshot_provider = gcp
```

The following parameters must be set regardless of cloud provider:
```bash
snapshot_instance = INSTANCE_NAME
snapshot_disks = DISK_NAME,DISK2_NAME,...
```
Where `snapshot_instance` is set to the name of the VM or compute instance where the storage volumes are attached and `snapshot_disks` is a comma-separated list of the disks which should be included in the backup.

!!!IMPORTANT
    Ensure that `snapshot_disks` includes every disk which stores data required by PostgreSQL. Any data which is not stored on a storage volume listed in `snapshot_disks` won't be included in the backup and won't be available at recovery time.

### Configuration for Google Cloud Platform snapshots

The following additional parameters must be set when using GCP:
```bash
gcp_project = GCP_PROJECT_ID
gcp_zone = ZONE
```
- `gcp_project`:  Set to the ID of the GCP project which owns the instance and storage volumes defined by `snapshot_instance` and `snapshot_disks`.

- `gcp_zone`:  Set to the availability zone in which the instance is located.

### Configuration for Azure snapshots

The following additional parameters must be set when using Azure:
```bash
azure_subscription_id = AZURE_SUBSCRIPTION_ID
azure_resource_group = AZURE_RESOURCE_GROUP
```
- `azure_subscription_id`: Set to the ID of the Azure subscription ID which owns the instance and storage volumes defined by `snapshot_instance` and `snapshot_disks`. 

- `azure_resource_group`: Set to the resource group to which the instance and disks belong.

### Configuration for AWS snapshots

When specifying `snapshot_instance` or `snapshot_disks`, Barman will accept either the instance/volume ID which was assigned to the resource by AWS, *or* a name. If a name is used, Barman will query AWS to find resources with a matching name tag. If none or multiple matching resources are found, Barman will exit with an error.

The following optional parameters can be set when using AWS:
```bash
aws_region = AWS_REGION
aws_profile = AWS_PROFILE_NAME
```
- `aws_profile`:  Set to the name of a section in the AWS credentials file. If `aws_profile` isn't used, the default profile will be used. If a credentials file doesn't exist, credentials will be sourced from the environment.

If `aws_region` is specified, it will override any region that may be defined in the AWS profile.

## Taking a snapshot backup

Once the configuration options are set and appropriate credentials are available to Barman, backups can be taken using the `barman backup` command.

Barman will validate the configuration parameters for snapshot backups during the `barman check` command, and when starting a backup.

### Unavailable arguments

The following arguments and config variables are unavailable when using `backup_method = snapshot`:

| **Command argument** | **Config variable** |
|----------------------|---------------------|
| N/A                  | backup_compression  |
| --bwlimit            | bandwidth_limit     |
| --jobs               | parallel_jobs       |
| N/A                  | network_compression |
| --reuse-backup       | reuse_backup        |



