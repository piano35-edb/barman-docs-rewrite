## PostgreSQL streaming connection

If you plan to use WAL streaming or streaming backup, you need to setup a streaming connection. We recommend creating a specific user in PostgreSQL, named **streaming_barman**, as follows:
```bash
postgres@pg\$ createuser -P --replication streaming_barman
```
!!!security
    The `createuser` command will prompt for a password, which you are then advised to add to the `\~barman/.pgpass` file on the backup server. For more information, see ["The Password File"](https://www.postgresql.org/docs/current/static/libpq-pgpass.html) in the PostgreSQL documentation..

You can manually verify that the streaming connection works through the following command:
```bash
barman@backup\$ psql -U streaming_barman -h pg \\
\-c "IDENTIFY_SYSTEM" \\
replication=1
```
If the connection is working you should see a response containing the system identifier, current timeline ID, and current WAL flush location.  For example:
```bash
systemid \| timeline \| xlogpos \| dbname
\---------------------+----------+------------+--------
7139870358166741016 \| 1 \| 1/330000D8 \|
(1 row)
```
Ensure you're able to connect via streaming replication before proceeding.

You also need to configure the `max_wal_senders` parameter in the PostgreSQL configuration file. The number of WAL senders depends on the PostgreSQL architecture you've implemented. This option represents the maximum number of concurrent streaming connections that the server will be allowed to manage.  For example:

`max_wal_senders = 2`

Another important parameter is `max_replication_slots`, which represents the maximum number of replication slots [5](https://docs.pgbarman.org/release/3.10.0/#fn5) that the server will be allowed to manage. This parameter is needed if you're planning to use the streaming connection to receive WAL files over the streaming connection.  For example:

`max_replication_slots = 2`

The values proposed for `max_replication_slots` and `max_wal_senders` here are only examples.  The values you'll use in your actual setup must be chosen after a careful evaluation of your architecture. For guidelines and clarification, see the PostgreSQL documentation.
