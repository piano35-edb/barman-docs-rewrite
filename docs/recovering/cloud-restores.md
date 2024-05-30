# Barman-cloud-restore for snapshots

The process for recovering from a snapshot backup with `barman-cloud` is very similar to the process for barman backups except that `barman-cloud-restore` should be run instead of `barman recover` once a recovery instance has been provisioned. This carries out the same pre-recovery checks as `barman recover` and copies the backup label into place on the recovery instance.

The snapshot metadata required to provision the recovery instance can be queried using `barman-cloud-backup-show`.

!!!Note
    when using `barman-cloud-restore` with an object stored backup, the command will not prepare PostgreSQL for the recovery. Any PITR options, custom `restore_command` values or WAL files required before PostgreSQL starts must be handled manually or by external tooling.

The following additional argument must be used with `barman-cloud-restore` when restoring a backup made with cloud snapshots:

`--snapshot-recovery-instance`
The following additional arguments are required:

- For the gcp provider:    `--gcp-zone`
- For the azure provider:  `--azure-resource-group`
- For the aws-s3 provider: `--aws-region`

The` --tablespace` option cannot be used with `barman-cloud-restore` when restoring a cloud snapshot backup.