Configuration models define a set of overrides for configuration options. These overrides can be applied to Barman servers which are part of the same cluster as the config models. They can be useful when handling clustered environments, so you can change the configuration of a Barman server in response to failover events, for example.

For example, using a PostgreSQL cluster with the following nodes:

- pg-node-1: primary
- pg-node-2: standby
- pg-node-3: standby

Assume you are backing up from the primary node, and have a configuration which includes the following options:

```bash
**[my-barman-server]**
cluster = my-cluster
conninfo = host=pg-node-1 user=barman database=postgres
streaming_conninfo = host=pg-node-1 user=streaming_barman
*; other options...*
```

You could, for example, have a configuration model for that cluster as follows:
```bash
**[my-barman-server:backup-from-pg-node-2]**
cluster = my-cluster
model = **true**
conninfo = host=pg-node-2 user=barman database=postgres
streaming_conninfo = host=pg-node-2 user=streaming_barman
```
Which could be applied upon a failover from **pg-node-1** to **pg-node-2** with the following command, so you start backing up from the new primary node:
```bash
barman config-switch my-barman-server my-barman-server:backup-from-pg-node-2
```
That will override the cluster configuration options with the values defined in the selected model.

!!!NOTE
    Not all options are configurable through models. Please refer to [section 5 of the 'man' page](https://docs.pgbarman.org/barman.5.html) to check settings which scope applies to models.

!!!tip
    You might be interested in checking [pg-backup-api](https://www.enterprisedb.com/docs/supported-open-source/barman/pg-backup-api/), which can start a REST API and listen for remote requests for executing barman commands, including `barman config-switch`.
