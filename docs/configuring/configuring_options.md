# Configuration options

The following options are available:

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`active`|True|Server/Model|
|**Description**|
|When set to true, the server is in full operational state. When set to false, the server can be used for diagnostics, but any operational command such as backup execution or WAL archiving is temporarily disabled. When adding a new server to Barman, we suggest setting `active=false` at first, making sure that barman check shows no problems, and only then activating the server. This will avoid spamming the Barman logs with errors during the initial setup.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`archiver`|False|Global/Server/Model|
|**Description**|
|This option allows you to activate log file shipping through PostgreSQL's `archive_command` for a server. If set to true, Barman expects that continuous archiving for a server is in place and will activate checks as well as management (including compression) of WAL files that Postgres deposits in the incoming directory. Setting it to false (default), will disable standard continuous archiving for a server. Note: If neither `archiver` nor `streaming_archiver` are set, Barman will automatically set this option to true. This is in order to maintain parity with deprecated behaviour where archiver would be enabled by default. This behaviour will be removed from the next major Barman version.|


|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`archiver_batch_size`||Global/Server/Model|
|**Description**|
|This option allows you to activate batch processing of WAL files for the archiver process, by setting it to a value > 0. Otherwise, the traditional unlimited processing of the WAL queue is enabled. When batch processing is activated, the archive-wal process would limit itself to maximum `archiver_batch_size` WAL segments per single run. Integer.|


|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`autogenerate_manifest`||Global/Server/Model|
|**Description**|
|This option enables the auto-generation of backup manifest files for rsync based backups and strategies. The manifest file is a JSON file containing the list of files contained in the backup. It is generated at the end of the backup process and stored in the backup directory. The manifest file generated follows the format described in the postgesql documentation, and is compatible with the `pg_verifybackup` tool. The option is ignored if the backup method is not rsync.|


|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`aws_profile`||Global/Server/Model|
|**Description**|
|The name of the AWS profile to use when authenticating with AWS (e.g. INI section in AWS credentials file).|


|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`aws_region`||Global/Server/Model|
|**Description**|
|The name of the AWS region containing the EC2 VM and storage volumes defined by `snapshot_instance` and `snapshot_disks`.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`azure_credential`||Global/Server/Model|
|**Description**|
The credential type (either `azure-cli` or `managed-identity) to use when authenticating with Azure. If this is omitted then the default Azure authentication flow will be used.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`azure_resource_group`||Global/Server/Model|
|**Description**|
The name of the Azure resource group to which the compute instance and disks defined by `snapshot_instance` and `snapshot_disks` belong. Required when the snapshot value is specified for backup_method and snapshot_provider is set to azure.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`azure_subscription_id`||Global/Server/Model|
|**Description**|
The ID of the Azure subscription which owns the instance and storage volumes defined by `snapshot_instance` and `snapshot_disks`. Required when the snapshot value is specified for `backup_method` and `snapshot_provider` is set to azure.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`backup_compression`||Global/Server/Model|
|**Description**|
The compression to be used during the backup process. Only supported when `backup_method = postgres`. Can either be unset or gzip,lz4, zstd or none. If unset then no compression will be used during the backup. Use of this option requires that the CLI application for the specified compression algorithm is available on the Barman server (at backup time) and the PostgreSQL server (at recovery time). Note that the lz4 and zstd algorithms require PostgreSQL 15 (beta) or later. Note that none compression will create an archive not compressed.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`backup_compression_format`||Global/Server/Model|
|**Description**|
The format `pg_basebackup` should use when writing compressed backups to disk. Can be set to either `plain` or `tar`. If unset then a default of `tar` is assumed. The value `plain` can only be used if the server is running PostgreSQL 15 or later and if `backup_compression_location` is server. Only supported when `backup_method = postgres`.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`backup_compression_level`||Global/Server/Model|
|**Description**|
An integer value representing the compression level to use when compressing backups. Allowed values depend on the compression algorithm specified by `backup_compression`. Only supported when `backup_method = postgres.`|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`backup_compression_location`||Global/Server/Model|
|**Description**|
The location (either client or server) where compression should be performed during the backup. The value server is only allowed if the server is running PostgreSQL 15 or later.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`backup_compression_workers`|0|Global/Server/Model|
|**Description**|
|The number of compression threads to be used during the backup process. Only supported when `backup_compression = zstd` is set.  In this case default compression behavior will be used.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`backup_directory`||Server|
|**Description**|
|Directory where backup data for a server will be placed.|


|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`backup_method`||Global/Server/Model|
|**Description**|
|Configure the method barman used for backup execution. If set to rsync (default), barman will execute backup using the rsync command over SSH (requires ssh_command). If set to postgres barman will use the pg_basebackup command to execute the backup. If set to local-rsync, barman will assume to be running on the same server as the PostgreSQL instance and with the same user, then execute rsync for the file system copy. If set to snapshot, barman will use the API for the cloud provider defined in the snapshot_provider option to create snapshots of disks specified in the snapshot_disks option and save only the backup label and metadata to its own storage.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`backup_options`|||
|**Description**|
|This option allows you to control the way Barman interacts with PostgreSQL for backups. It is a comma-separated list of values that accepts the following options:|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`concurrent_backup`||Global/Server/Model|
|**Description**|
|`barman backup` executes backup operations using concurrent backup which is the recommended backup approach for PostgreSQL versions >= 9.6 and uses the PostgreSQL API.
`concurrent_backup` can also be used to perform a backup from a standby server.
`exclusive_backup` (PostgreSQL versions older than 15 only): barman backup executes backup operations using the deprecated exclusive backup approach (technically through `pg_start_backup` and `pg_stop_backup`)
`external_configuration`: if present, any warning regarding external configuration files is suppressed during the execution of a backup.
Note that `exclusive_backup` and `concurrent_backup` are mutually exclusive.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`bandwidth_limit`|0|Global/Server/Model|
|**Description**|
|This option allows you to specify a maximum transfer rate in kilobytes per second. A value of zero specifies no limit.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`barman_home`||Global|
|**Description**|
|Main data directory for Barman.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`barman_lock_directory`|`%(barman_home)s`|Global|
|**Description**|
|Directory for locks.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`basebackup_retry_sleep`|30|Global/Server/Model|
|**Description**|
|Number of seconds of wait after a failed copy, before retrying. Used during both backup and recovery operations. Positive integer.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`basebackup_retry_times`|0|Global/Server/Model|
|**Description**|
|Number of retries of base backup copy, after an error. Used during both backup and recovery operations. Positive integer.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`basebackups_directory`||Server|
|**Description**|
|Directory where base backups will be placed.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`check_timeout`|30|Global/Server/Model|
|**Description**|
|Maximum execution time, in seconds per server, for a `barman check` command. Set to 0 to disable the timeout. Positive integer.|


|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`cluster`||Server/Model|
|**Description**|
|Name of the Barman cluster associated with a Barman server or model. Used by Barman to group servers and configuration models that can be applied to them. Can be omitted for servers, in which case it defaults to the server name. Must be set for configuration models, so Barman knows the set of servers which can apply a given model.|


|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`compression`|||
|**Description**|
|Standard compression algorithm applied to WAL files. Possible values are: gzip (requires gzip to be installed on the system), bzip2 (requires bzip2), pigz (requires pigz), pygzip (Python's internal gzip compressor) and pybzip2 (Python's internal bzip2 compressor).|

Scope: Global/Server/Model.

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`config_changes_queue`||Global|
|**Description**|
|Barman uses a queue to apply configuration changes requested through barman config-update command. This allows it to serialize multiple requests of configuration changes, and also retry an operation which has been abruptly terminated. This configuration option allows you to specify where in the filesystem the queue should be written. By default Barman writes to a file named `cfg_changes.queue` under `barman_home`.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`conninfo`||Server/Model|
|**Description**|
|Connection string used by Barman to connect to the Postgres server. This is a libpq connection string, consult the PostgreSQL manual for more information. Commonly used keys are: `host, hostaddr, port, dbname, user, password`.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`create_slot`|manual|Global/Server/Model|
|**Description**|
|When set to auto and slot_name is defined, Barman automatically attempts to create the replication slot if not present. When set to manual, the replication slot needs to be manually created.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`custom_compression_filter`||Global/Server/Model|
|**Description**|
|Customised compression algorithm applied to WAL files.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`custom_compression_magic`||Global/Server/Model|
|**Description**|
|Customised compression magic which is checked in the beginning of a WAL file to select the custom algorithm. If you are using a custom compression filter then setting this will prevent barman from applying the custom compression to WALs which have been pre-compressed with that compression. If you do not configure this then custom compression will still be applied but any pre-compressed WAL files will be compressed again during WAL archive.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`custom_decompression_filter`||Global/Server/Model|
|**Description**|
|Customized decompression algorithm applied to compressed WAL files. This must match the compression algorithm.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`description`||Server/Model|
|**Description**|
|A human-readable description of a server.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`errors_directory`||Server|
|**Description**|
|Directory that contains WAL files that contain an error; usually this is related to a conflict with an existing WAL file (e.g. a WAL file that has been archived after a streamed one).|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`forward_config_path`|false|Global/Server/Model|
|**Description**|
|Parameter which determines whether a passive node should forward its configuration file path to its primary node during cron or sync-info commands. Set to `true` if you are invoking barman with the`-c/--config` option and your configuration is in the same place on both the passive and primary barman servers.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`gcp_project`||Global/Server/Model|
|**Description**|
|The ID of the GCP project which owns the instance and storage volumes defined by `snapshot_instance` and `snapshot_disks`. Required when the snapshot value is specified for `backup_method` and `snapshot_provider` is set to `gcp`.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`gcp_zone`||Server/Model|
|**Description**|
|The name of the availability zone where the compute instance and disks to be backed up in a snapshot backup are located. Required when the snapshot value is specified for `backup_method` and `snapshot_provider` is set to `gcp`.|


|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`immediate_checkpoint`|false|Global/Server/Model|
|**Description**|
|This option allows you to control the way PostgreSQL handles checkpoint at the start of the backup. If set to `false`, the I/O workload for the checkpoint will be limited, according to the `checkpoint_completion_target` setting on the PostgreSQL server. If set to `true`, an immediate checkpoint will be requested, meaning that PostgreSQL will complete the checkpoint as soon as possible.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`incoming_wals_directory`|||Server
|**Description**|
|Directory where incoming WAL files are archived into. Requires archiver to be enabled.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`last_backup_maximum_age`|empty|Global/Server/Model|
|**Description**|
|This option identifies a time frame that must contain the latest backup. If the latest backup is older than the time frame,  the `barman check` command will report an error to the user. If empty, the latest backup is always considered valid. The syntax for this option is: "i (DAYS | WEEKS | MONTHS)" where `i` is an integer greater than zero, representing the number of days | weeks | months of the time frame.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`last_backup_minimum_size`|empty|Global/Server/Model|
|**Description**|
|This option identifies lower limit to the acceptable size of the latest successful backup. If the latest backup is smaller than the specified size, the `barman check` command will report an error to the user. If empty, the latest backup is always considered valid. The syntax for this option is: "i (k|Ki|M|Mi|G|Gi|T|Ti)" where `i` is an integer greater than zero, with an optional SI or IEC suffix. k=kilo=1000, Ki=Kibi=1024 and so forth. The suffix is case-sensitive.|


|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`last_wal_maximum_age`|empty|Global/Server/Model|
|**Description**|
|This option identifies a time frame that must contain the latest WAL file archived. If the latest WAL file is older than the time frame, the `barman check` command will report an error to the user. If empty, the age of the WAL files is not checked. The syntax is the same as the `last_backup_maximum_age` option.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`lock_directory_cleanup`||Global|
|**Description**|
|Enables automatic cleaning up of the `barman_lock_directory` from unused lock files.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`log_file`||Global|
|**Description**|
|Location of Barman's log file.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`log_level`||Global|
|**Description**|
|Level of logging (DEBUG, INFO, WARNING, ERROR, CRITICAL).|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`max_incoming_wals_queue`||Global/Server/Model|
|**Description**|
|Maximum number of WAL files in the incoming queue (in both streaming and archiving pools) that are allowed before barman check returns an error (that does not block backups). Default: None (disabled).|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`minimum_redundancy`|0|Global/Server/Model|
|**Description**|
|Minimum number of backups to be retained.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`model`||Model|
|**Description**|
|By default any section configured in the Barman configuration files define the configuration for a Barman server. If you set `model = true` in a section, that turns that section into a configuration model for a given cluster. Cannot be set as false.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`network_compression`|false|Global/Server/Model|
|**Description**|
|This option allows you to enable data compression for network transfers. If set to false, no compression is used. If set to true, compression is enabled, reducing network usage.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`parallel_jobs`|1|Global/Server/Model|
|**Description**|
|This option controls how many parallel workers will copy files during a backup or recovery command. For backup purposes, it works only when `backup_method` is rsync.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`parallel_jobs_start_batch_period`|1 second|Global/Server/Model|
|**Description**|
|The time period in seconds over which a single batch of jobs will be started.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`parallel_jobs_start_batch_size`|10 jobs|Global/Server/Model|
|**Description**|
|Maximum number of parallel jobs to start in a single batch.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`path_prefix`||Global/Server/Model|
|**Description**|
|One or more absolute paths, separated by colon, where Barman looks for executable files. The paths specified in `path_prefix` are tried before the ones specified in `PATH` environment variable.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`post_archive_retry_script`||Global/Server|
|**Description**|
|Hook script launched after a WAL file is archived by maintenance. Being this a retry hook script, Barman will retry the execution of the script until this either returns a SUCCESS (0), an ABORT_CONTINUE (62) or an ABORT_STOP (63) code. In a post archive scenario, ABORT_STOP has currently the same effects as ABORT_CONTINUE.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`post_archive_script`||Global/Server|
|**Description**|
|Hook script launched after a WAL file is archived by maintenance, after 'post_archive_retry_script'.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`post_backup_retry_script`||Global/Server|
|**Description**|
|Hook script launched after a base backup. Being this a retry hook script, Barman will retry the execution of the script until this either returns a SUCCESS (0), an ABORT_CONTINUE (62) or an ABORT_STOP (63) code. In a post backup scenario, ABORT_STOP has currently the same effects as ABORT_CONTINUE.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`post_backup_script`||Global/Server|
|**Description**|
|Hook script launched after a base backup, after 'post_backup_retry_script'.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`post_delete_retry_script`||Global/Server|
|**Description**|
|Hook script launched after the deletion of a backup. Being this a retry hook script, Barman will retry the execution of the script until this either returns a SUCCESS (0), an ABORT_CONTINUE (62) or an ABORT_STOP (63) code. In a post delete scenario, ABORT_STOP has currently the same effects as ABORT_CONTINUE.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`post_delete_script`||Global/Server|
|**Description**|
|Hook script launched after the deletion of a backup, after 'post_delete_retry_script'.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`post_recovery_retry_script`||Global/Server|
|**Description**|
|Hook script launched after a recovery. Being this a retry hook script, Barman will retry the execution of the script until this either returns a SUCCESS (0), an ABORT_CONTINUE (62) or an ABORT_STOP (63) code. In a post recovery scenario, ABORT_STOP has currently the same effects as ABORT_CONTINUE.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`post_recovery_script`||Global/Server|
|**Description**|
|Hook script launched after a recovery, after 'post_recovery_retry_script'.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`post_wal_delete_retry_script`||Global/Server|
|**Description**|
|Hook script launched after the deletion of a WAL file. Being this a retry hook script, Barman will retry the execution of the script until this either returns a SUCCESS (0), an ABORT_CONTINUE (62) or an ABORT_STOP (63) code. In a post delete scenario, ABORT_STOP has currently the same effects as ABORT_CONTINUE.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`post_wal_delete_script`||Global/Server|
|**Description**|
|Hook script launched after the deletion of a WAL file, after 'post_wal_delete_retry_script'.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`pre_archive_retry_script`||Global/Server|
|**Description**|
|Hook script launched before a WAL file is archived by maintenance, after 'pre_archive_script'. Being this a retry hook script, Barman will retry the execution of the script until this either returns a SUCCESS (0), an ABORT_CONTINUE (62) or an ABORT_STOP (63) code. Returning ABORT_STOP will propagate the failure at a higher level and interrupt the WAL archiving operation.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`pre_archive_script`||Global/Server|
|**Description**|
|Hook script launched before a WAL file is archived by maintenance.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`pre_backup_retry_script`||Global/Server|
|**Description**|
|Hook script launched before a base backup, after 'pre_backup_script'. Being this a retry hook script, Barman will retry the execution of the script until this either returns a SUCCESS (0), an ABORT_CONTINUE (62) or an ABORT_STOP (63) code. Returning ABORT_STOP will propagate the failure at a higher level and interrupt the backup operation.

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`pre_backup_script`||Global/Server|
|**Description**|
|Hook script launched before a base backup.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`pre_delete_retry_script`||Global/Server|
|**Description**|
|Hook script launched before the deletion of a backup, after 'pre_delete_script'. Being this a retry hook script, Barman will retry the execution of the script until this either returns a SUCCESS (0), an ABORT_CONTINUE (62) or an ABORT_STOP (63) code. Returning ABORT_STOP will propagate the failure at a higher level and interrupt the backup deletion.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`pre_delete_script`||Global/Server|
|**Description**|
|Hook script launched before the deletion of a backup.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`pre_recovery_retry_script`||Global/Server|
|**Description**|
|Hook script launched before a recovery, after 'pre_recovery_script'. Being this a retry hook script, Barman will retry the execution of the script until this either returns a SUCCESS (0), an ABORT_CONTINUE (62) or an ABORT_STOP (63) code. Returning ABORT_STOP will propagate the failure at a higher level and interrupt the recover operation.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`pre_recovery_script`||Global/Server|
|**Description**|
|Hook script launched before a recovery.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`pre_wal_delete_retry_script`||Global/Server|
|**Description**|
|Hook script launched before the deletion of a WAL file, after 'pre_wal_delete_script'. Being this a retry hook script, Barman will retry the execution of the script until this either returns a SUCCESS (0), an ABORT_CONTINUE (62) or an ABORT_STOP (63) code. Returning ABORT_STOP will propagate the failure at a higher level and interrupt the WAL file deletion.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`pre_wal_delete_script`||Global/Server|
|**Description**|
|Hook script launched before the deletion of a WAL file.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`primary_checkpoint_timeout`||Server/Model|
|**Description**|
|This defines the amount of seconds that Barman will wait at the end of a backup if no new WAL files are produced, before forcing a checkpoint on the primary server.  If not set or set to 0, Barman will not force a checkpoint on the primary, and wait indefinitely for new WAL files to be produced.  The value of this option should be greater of the value of the `archive_timeout` set on the primary server.  This option works only if `primary_conninfo` option is set, and it is ignored otherwise.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`primary_conninfo`||Server/Model|
|**Description**|
|The connection string used by Barman to connect to the primary Postgres server during backup of a standby Postgres server. Barman will use this connection to carry out any required WAL switches on the primary during the backup of the standby. This allows backups to complete even when `archive_mode = always` is set on the standby and write traffic to the primary is not sufficient to trigger a natural WAL switch.  If `primary_conninfo` is set then it must be pointing to a primary Postgres instance and conninfo must be pointing to a standby Postgres instance. Furthermore both instances must share the same systemid. If these conditions are not met then barman check will fail.  The `primary_conninfo` value must be a libpq connection string.  Consult the PostgreSQL manual for more information. Commonly used keys are: `host, hostaddr, port, dbname, user, password`.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`primary_ssh_command`||Global/Server/Model|
|**Description**|
|Parameter that identifies a Barman server as passive. In a passive node, the source of a backup server is a Barman installation rather than a PostgreSQL server. If `primary_ssh_command` is specified, Barman uses it to establish a connection with the primary server. Empty by default, it can also be set globally.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`recovery_options`|empty|Global/Server/Model|
|**Description**|
|Options for recovery operations. Currently only supports get-wal. get-wal activates generation of a basic restore_command in the resulting recovery configuration that uses the barman get-wal command to fetch WAL files directly from Barman's archive of WALs. Comma separated list of values.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`recovery_staging_path`||Global/Server/Model|
|**Description**|
|A path to a location on the recovery host (either the barman server or a remote host if `--remote-ssh-command` is also used) where files for a compressed backup will be staged before being uncompressed to the destination directory. Backups will be staged in their own directory within the staging path according to the following naming convention: "barman-staging-SERVER_NAME-BACKUP_ID". The staging directory within the staging path will be removed at the end of the recovery process. This option is required when recovering from compressed backups and has no effect otherwise.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`retention_policy`|empty|Global/Server/Model|
|**Description**|
|Policy for retention of periodic backups and archive logs. If left empty, retention policies are not enforced. For redundancy based retention policy use "REDUNDANCY i" (where i is an integer > 0 and defines the number of backups to retain). For recovery window retention policy use "RECOVERY WINDOW OF i DAYS" or "RECOVERY WINDOW OF i WEEKS" or "RECOVERY WINDOW OF i MONTHS" where i is a positive integer representing, specifically, the number of days, weeks or months to retain your backups.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`retention_policy_mode`||Global/Server/Model|
|**Description**|
|Currently only "auto" is implemented.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`reuse_backup`|off|Global/Server/Model|
|**Description**|
|This option controls incremental backup support. Possible values are:
`off`: disabled;
`copy`: reuse the last available backup for a server and create a copy of the unchanged files (reduce backup time);
`link`: reuse the last available backup for a server and create a hard link of the unchanged files (reduce backup time and space). Requires operating system and file system support for hard links.|


|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`slot_name`|None|Global/Server/Model|
|**Description**|
|Physical replication slot to be used by the `receive-wal` command when streaming_archiver is set to on.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`snapshot_disks`||Server/Model|
|**Description**|
|A comma-separated list of disks which should be included in a backup taken using cloud snapshots. Required when the snapshot value is specified for `backup_method`.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`snapshot_instance`||Server/Model|
|**Description**|
|The name of the VM or compute instance where the storage volumes are attached. Required when the snapshot value is specified for `backup_method`.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`snapshot_provider`||Global/Server/Model|
|**Description**|
|The name of the cloud provider which should be used to create snapshots. Required when the snapshot value is specified for `backup_method`. Supported values: `gcp`.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`ssh_command`||Server/Model|
|**Description**|
|Command used by Barman to login to the Postgres server via ssh.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`streaming_archiver`|off|Global/Server/Model|
|**Description**|
|This option allows you to use the PostgreSQL's streaming protocol to receive transaction logs from a server. If set to on, Barman expects to find `pg_receivewal` (known as `pg_receivexlog` prior to PostgreSQL 10) in the `PATH` (see `path_prefix option`) and that streaming connection for the server is working. This activates connection checks as well as management (including compression) of WAL files. If set to off, Barman will rely only on continuous archiving for a server WAL archive operations, eventually terminating any running `pg_receivexlog` for the server.  If neither `streaming_archiver` nor archiver are set, Barman will automatically set archiver to true. This is in order to maintain parity with deprecated behaviour where archiver would be enabled by default. This behaviour will be removed from the next major Barman version.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`streaming_archiver_batch_size`||Global/Server/Model|
|**Description**|
|This option allows you to activate batch processing of WAL files for the `streaming_archiver` process, by setting it to a value > 0. Otherwise, the traditional unlimited processing of the WAL queue is enabled. When batch processing is activated, the `archive-wal` process would limit itself to maximum `streaming_archiver_batch_size` WAL segments per single run. Integer.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`streaming_archiver_name`||Global/Server/Model|
|**Description**|
|Identifier to be used as application_name by the `receive-wal` command. Only available with `pg_receivewal` (or `pg_receivexlog` >= 9.3). By default it is set to `barman_receive_wal`.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`streaming_backup_name`|`barman_streaming_backup`|Global/Server/Model|
|**Description**|
|Identifier to be used as application_name by the pg_basebackup command.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`streaming_conninfo`|`conninfo`|Server/Model|
|**Description**|
|Connection string used by Barman to connect to the Postgres server via streaming replication protocol.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`streaming_wals_directory`||Server|
|**Description**|
|Directory where WAL files are streamed from the PostgreSQL server to Barman. Requires `streaming_archiver` to be enabled.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`tablespace_bandwidth_limit`|0|Global/Server/Model|
|**Description**|
|This option allows you to specify a maximum transfer rate in kilobytes per second, by specifying a comma separated list of tablespaces (pairs TBNAME:BWLIMIT). A value of zero specifies no limit.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`wal_conninfo`||Server/Model|
|**Description**|
|A connection string which, if set, will be used by Barman to connect to the Postgres server when checking the status of the replication slot used for receiving WALs. If left unset then Barman will use the connection string defined by `wal_streaming_conninfo`. If `wal_conninfo` is set but `wal_streaming_conninfo` is unset then `wal_conninfo` will be ignored.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`wal_retention_policy`||Global/Server/Model|
|**Description**|
|Policy for retention of archive logs (WAL files). Currently only "MAIN" is available.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`wal_streaming_conninfo`||Server/Model|
|**Description**|
|A connection string which, if set, will be used by Barman to connect to the Postgres server when receiving WAL segments via the streaming replication protocol. If left unset then Barman will use the connection string defined by `streaming_conninfo` for receiving WAL segments.|

|**Option**|**Default**|**Scope**|
|----------|-----------|---------|
|`wals_directory`||Server|
|**Description**|
|Directory which contains WAL files.|

