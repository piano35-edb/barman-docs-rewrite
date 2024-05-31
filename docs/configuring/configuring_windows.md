## Setting up a Microsoft Windows-based server

You can back up a PostgreSQL server running on Windows using the streaming connection for both WAL archiving and for backups.

!!!warning
    This feature is still experimental because it is not yet part of our continuous integration system.

## Requirements 

Ensure that the system user chosen to run PostgreSQL has the permission needed to access the restored data. It must have full control over the PostgreSQL data directory.

## Limitations

-  Remote recovery isn't supported for Windows servers.  You must recover your cluster locally in the Barman server and then copy all the files on a Windows server or use a folder shared between the PostgreSQL server and the Barman server.

-  `pg_basebackup` interoperability from Windows to Linux is still experimental. If you're experiencing issues taking a backup from a Windows server and your PostgreSQL locale is not English, a possible workaround for the issue is instructing your PostgreSQL to emit messages in English. You can do this by adding the following parameter in your `postgresql.conf` file: `lc_messages = 'English'`

## Setting up

Follow the steps to set up a streaming connection.  You can then back up your server as usual.