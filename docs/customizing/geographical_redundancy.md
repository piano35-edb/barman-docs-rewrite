# Geographical redundancy

You can set up cascading backup architectures with Barman, where the source of a backup server is a Barman installation rather than a PostgreSQL server.

This feature allows users to transparently keep geographically distributed copies of PostgreSQL backups.

A backup server that's connected to a Barman installation rather than a PostgreSQL server is defined **passive node**. A passive node is configured through the `primary_ssh_command` option.  The option is available for the following levels:

- Global: for a full replica of a primary Barman installation
- Server: for mixed scenarios, with both a *direct* and *passive* server(s).