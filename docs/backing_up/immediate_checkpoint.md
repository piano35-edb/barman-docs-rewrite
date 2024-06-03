Before starting a backup, Barman requests a checkpoint, which generates additional workload. Normally that checkpoint is throttled according to the settings for workload control on the PostgreSQL server, which means that the backup could be delayed.

This default behaviour can be changed through the `immediate_checkpoint` configuration global/server option (set to false by default).

If `immediate_checkpoint` is set to true, PostgreSQL will not try to limit the workload, and the checkpoint will happen at maximum speed, starting the backup as soon as possible.

You can override the configuration option behavior, by issuing `barman backup` with any of these two options:

-   `\--immediate-checkpoint`:  Forces an immediate checkpoint.
-   `\--no-immediate-checkpoint`:  Forces to wait for the checkpoint to happen.