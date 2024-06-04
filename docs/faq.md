# F.A.Q.

??? "Does barman perform physical backup of PostgreSQL databases?"
    Yes. Barman is an application for physical backups of PostgreSQL servers that manages base backups and WAL archiving. It's a disaster recovery application. Barman doesn't support logical backups (aka dumps).

??? "What's the difference between pg_dump and Barman and why should I use Barman instead?"
    If you already use pg_dump, it is a good thing. However, if your business is based on your PostgreSQL database, logical backups (the ones performed by pg_dump) are not enough. These dumps are snapshots of your database at a particular time of your day. Usually we perform these activities at night. If a crash occurs during the day, all your transactions between the time of the dump and the crash are lost. Forever. In this context, you need to put in place a more robust solution for disaster recovery, based on physical backups. barman is one of these, but not the only one. Similar tools, also open-source, are pg-rman and OmniPITR which are quite different from barman.

??? "I manage several PostgreSQL instances. Can Barman manage more than one PostgreSQL server centrally?"
!!!Answer
    Yes. barman has been designed to allow remote backups of PostgreSQL servers. It allows DBAs to manage the backups of multiple servers from a centralised host. barman allows definition of a catalogue of PostgreSQL servers for base backups and continuous archiving of WAL segments.

??? "Does Barman manage replication and high availability as well? How does it compare with repmgr, OmniPITR, walmgr and similar tools?"
    !!!Answer
    No. Barman aims to be a pure disaster recovery solution. It is responsible for the sole backup of a cluster of PostgreSQL servers. If high availability is what you are looking for, we encourage you to use repmgr. barman specifically targets the DR case only, because it requires a simpler and less invasive design than the one required to cover HA. This is why barman does not duplicate existing HA tools like the ones mentioned above. You do not need to install anything on the server. The only requirement is to configure access for the barman client, which is less invasive than what is required for HA.

5.  Can you define retention policies. Does barman manage them?

!!!Answer
    According to us, the PostgreSQL eco-system was lacking of a product that facilitates the setup and the management of disaster recovery solutions for DBAs. barman 1.2.0 introduced support for retention policies both for base backups and WAL segments, for every single server. You can specify the retention policy in the configuration file based on time (e.g. ‘recovery window of 30 days’) or number of base backups (e.g. ‘redundancy 3’).

6. Does Barman guarantee data protection and security?

!!!Answer
    Barman communicates with your remote/local PostgreSQL server using SSH connections (for process and file management) and the standard PostgreSQL connection (for querying the database). It is the system administrator’s duty to make sure that SSH communications occur in a safe way. At the same time it is the database administrator’s duty to make sure communications with the PostgreSQL server occur in a secure way.

7. Is the interface complex and hard to understand?

!!!Answer
    Currently barman has a console interface through which you can specify several commands. You can list the servers managed by barman, or list the available backups (and related information such as time, size, number of WAL segments, etc.), or show the information for a particular backup. Launching a new base backup for a server is trivial, as well as restoring it, either locally or remotely.

8.  I don't want to manage backup space and want my disaster recovery solution to be scalable and elastic in terms of disk resources. Do you support storage on Amazon EC2 facilities?

!!!Answer
    Currently, no. However barman has been designed to one day integrate WAL shipping and backup archiving with Amazon S3 storage. We are looking for sponsors willing to fund the development of this feature and add it to barman‘s open-source version.

9.  Do you have packages for RedHat and Debian based distributions?

!!!Answer
    Yes. However, maintaining packages requires development and testing. We would appreciate it if you would consider sponsoring the project so that our Debian and RPM packages are regularly updated and released.

10.  Does Barman support streaming replication protocol available from PostgreSQL 9?

!!!Answer
    Currently, no. However, we would love to extend barman so that it can perform both the client and the server roles for streaming replication. It is certainly feasible and this would allow to implement cascading backup, as well as synchronous backup (for “0 recovery point objective”).

11.  Does Barman allow to cap to bandwidth usage for backup and recovery?

!!!Answer
    Version 1.2.1 introduces support for bandwidth limitation at global, server and tablespace level.