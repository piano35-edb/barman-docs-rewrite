Configuration models define a set of overrides for configuration options. These overrides can be applied to Barman servers which are part of the same cluster as the config models. They can be useful when handling clustered environments, so you can change the configuration of a Barman server in response to failover events, for example.

For example, in a PostgreSQL cluster with the following nodes:

- **pg-node-1**: primary
- **pg-node-2**: standby
- **pg-node-3**: standby

Assume you're backing up from the primary node.  Your configuration includes the following options:

```bash
**[my-barman-server]**
cluster = my-cluster
conninfo = host=pg-node-1 user=barman database=postgres
streaming_conninfo = host=pg-node-1 user=streaming_barman
*; other options...*
```

You could have a configuration model for that cluster as follows:
```bash
**[my-barman-server:backup-from-pg-node-2]**
cluster = my-cluster
model = **true**
conninfo = host=pg-node-2 user=barman database=postgres
streaming_conninfo = host=pg-node-2 user=streaming_barman
```
That model could be applied upon a failover from **pg-node-1** to **pg-node-2** so you start backing up from the new primary node.  The following command could be used:
```bash
barman config-switch my-barman-server my-barman-server:backup-from-pg-node-2
```
The command will override the cluster configuration options with the values defined in the selected model.

!!!info
    Not all options are configurable through models.  Confirm your selected option is compatible with the model scope.

!!!tip
    The [pg-backup-api](https://www.enterprisedb.com/docs/supported-open-source/barman/pg-backup-api/) can start a REST API and listen for remote requests for executing Barman commands, including `barman config-switch`.
