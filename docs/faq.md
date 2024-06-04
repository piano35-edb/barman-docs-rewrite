# Frequently asked questions (F.A.Q.)

## General

??? "Does Barman perform physical backup of PostgreSQL databases?"
    Yes. Barman is an application for physical backups of PostgreSQL servers that manages base backups and WAL archiving. It's a disaster recovery application. Barman doesn't support logical backups (aka dumps).

??? "What's the difference between pg_dump and Barman and why should I use Barman instead?"
    If you already use pg_dump, it is a good thing. However, if your business is based on your PostgreSQL database, logical backups (the ones performed by pg_dump) are not enough. These dumps are snapshots of your database at a particular time of your day. Usually we perform these activities at night. If a crash occurs during the day, all your transactions between the time of the dump and the crash are lost. Forever. In this context, you need to put in place a more robust solution for disaster recovery, based on physical backups. Barman is one of these, but not the only one. Similar tools, also open-source, are pg-rman and OmniPITR which are quite different from Barman.

??? "I manage several PostgreSQL instances. Can Barman manage more than one PostgreSQL server centrally?"
    Yes. Barman has been designed to allow remote backups of PostgreSQL servers. It allows DBAs to manage the backups of multiple servers from a centralised host. Barman allows definition of a catalogue of PostgreSQL servers for base backups and continuous archiving of WAL segments.

??? "Does Barman manage replication and high availability as well? How does it compare with repmgr, OmniPITR, walmgr and similar tools?"
    No. Barman aims to be a pure disaster recovery solution. It is responsible for the sole backup of a cluster of PostgreSQL servers. If high availability is what you are looking for, we encourage you to use repmgr. Barman specifically targets the DR case only, because it requires a simpler and less invasive design than the one required to cover HA. This is why Barman does not duplicate existing HA tools like the ones mentioned above. You do not need to install anything on the server. The only requirement is to configure access for the Barman client, which is less invasive than what is required for HA.

??? "Can you define retention policies. Does Barman manage them?"
    According to us, the PostgreSQL eco-system was lacking of a product that facilitates the setup and the management of disaster recovery solutions for DBAs. Barman 1.2.0 introduced support for retention policies both for base backups and WAL segments, for every single server. You can specify the retention policy in the configuration file based on time (e.g. ‘recovery window of 30 days’) or number of base backups (e.g. ‘redundancy 3’).

??? "Does Barman guarantee data protection and security?"
    Barman communicates with your remote/local PostgreSQL server using SSH connections (for process and file management) and the standard PostgreSQL connection (for querying the database). It is the system administrator’s duty to make sure that SSH communications occur in a safe way. At the same time it is the database administrator’s duty to make sure communications with the PostgreSQL server occur in a secure way.

??? "Is the interface complex and hard to understand?"
    Currently Barman has a console interface through which you can specify several commands. You can list the servers managed by Barman, or list the available backups (and related information such as time, size, number of WAL segments, etc.), or show the information for a particular backup. Launching a new base backup for a server is trivial, as well as restoring it, either locally or remotely.

??? "I don't want to manage backup space and want my disaster recovery solution to be scalable and elastic in terms of disk resources. Do you support storage on Amazon EC2 facilities?"
    Currently, no. However Barman has been designed to one day integrate WAL shipping and backup archiving with Amazon S3 storage. We are looking for sponsors willing to fund the development of this feature and add it to Barman‘s open-source version.

??? "Do you have packages for RedHat and Debian based distributions?"
    Yes. However, maintaining packages requires development and testing. We would appreciate it if you would consider sponsoring the project so that our Debian and RPM packages are regularly updated and released.

??? "Does Barman support streaming replication protocol available from PostgreSQL 9?"
    Currently, no. However, we would love to extend Barman so that it can perform both the client and the server roles for streaming replication. It is certainly feasible and this would allow to implement cascading backup, as well as synchronous backup (for “0 recovery point objective”).

??? "Does Barman allow to cap to bandwidth usage for backup and recovery?"
    Version 1.2.1 introduces support for bandwidth limitation at global, server and tablespace level.

## Installation and Configuration

??? "Does Barman have to be installed on the same server than PostgreSQL?"
    No. Barman does not necessarily require to be on the same host where PostgreSQL is running. It is your choice to install it locally or on another server (usually dedicated for backup purposes).
??? "Setting up and managing physical backups in PostgreSQL is not trivial. Is Barman easy to setup and configure?"
    Barman is written in Python and the installer is able to download and install all the required dependencies. We have adopted a configuration by convention approach, that allows administrators to specify configuration options at global and server level but makes Barman work the same in case we want to stick with the default options. All you need to do is specify a root directory for Barman then, for each server, a Postgres connection and an SSH connection. The most complicated task you need to do is to configure WAL archiving in the PostgreSQL server in order to ship WAL files to Barman.
??? "Can you have more Barman configurations in a backup server?"
    Yes. Barman needs a configuration file. You can have a system wide configuration (/etc/Barman.conf) or a user configuration (~/.Barman.conf). For instance, you could setup several users in your system that use Barman, each of those working on a subset of your managed PostgreSQL servers. This way you can protect your backups on a user basis.
## Backup

??? "How many backups can Barman handle for every PostgreSQL server? Can it handle just one backup per server or more?"
Barman allows the storage of multiple base backups per PostgreSQL server. You can actually list every backup for that particular server, along with their dimensions on disks and the number of directly associated WAL files. The only limit on the number of backups is disk space.
??? "I have continuous archiving in place, but managing WAL files and understanding which ones “belong” to a particular base backup is not obvious. Can Barman simplify this?"
Fortunately yes. The way Barman works is to keep separate base backups from WAL segments for a specific server. Even though they are much related, Barman sees a WAL archive as a continuous stream of data from the first base backup to the last available. A neat feature of Barman is to link every WAL file to a base backup so that it is possible to determine the size of a backup in terms of two components: base backup and WAL segments.
??? "Can Barman compress base backups?"
Currently, Barman can compress backups using the postgres backup method, thanks to pg_basebackup compression features. This can be enabled using the backup_compression config option. For rsync based backups, at the moment there is no compression method, but it is feasible and the current design allows it. We are seeking sponsors interested in developing the compression feature of rsync base backups.
??? "Can Barman compress WAL segments?"
Yes. You can specify your compression filter for WAL segments, and dramatically reduce the size of your WAL files by 5/10 times.
??? "Does Barman backup tablespaces?"
Yes. Tablespaces are handled transparently and automatically by Barman.
Barman backup is stuck on “Asking PostgreSQL server to finalize the backup”. What am I supposed to do?
This probably means that the WAL shipping process is not working properly. Make sure that the archive_command on the PostgreSQL server is correctly shipping them in the expected WAL incoming directory.
??? "Can I backup from a PostgreSQL standby?"
Yes, Barman natively supports backup from standby servers for both postgres and rsync backup methods.

## Recovery

??? "Can I specify different retention policies for base backups and WAL segments?"
Yes, definitely. It might happen for instance that you want to keep base backups of the last 12 months and keep WALs for Point-In-Time-Recovery for the last month only.
??? "Does Barman manage recovery?"
Yes. With Barman you can recover a PostgreSQL instance on your backup server or a remote one.
??? "Does Barman manage recovery to a specific transaction or to a specific time?"
Yes. With Barman you can recover a PostgreSQL instance and specify a transaction ID or a time for targets. It is just a matter of adding an option to your recover command.
??? "Does Barman support timelines?"
Yes, Barman handles PostgreSQL timelines for recovery.
??? "Does Barman manage remote recovery?"
Yes.
??? "Does Barman manage tablespaces for recovery operations?"
Yes. You can also relocate a specific tablespace to another directory.
??? "Does Barman support flashbacks?"
Currently no. However, we will get there. Adding a time machine feature to PostgreSQL is feasible with Barman and requires a bit of coding. Once again, if you need this feature and are in the position to sponsor it, please contact us.
??? "During recovery, does Barman allow me to relocate the PGDATA directory? What about tablespaces?"
Yes. When recovering a server, you can specify different locations for your PGDATA directory and all your tablespaces (if any). This allows you to setup temporary sandbox servers. This is particularly useful in case you want to recover a table that you have unintentionally dropped from the master by dumping the table from the sandbox server and then recreating it in your master server.