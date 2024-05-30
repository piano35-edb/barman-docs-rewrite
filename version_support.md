
# Version Support

Barman supports the following versions:

| Barman version                   | PostgreSQL version | Operating system | Python version | Python modules                                                   | Rsync version    | Additional requirements                                                                                                                                         |
|----------------------------------|--------------------|------------------|----------------|------------------------------------------------------------------|------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 3.10.0 (Latest / stable version) | 10.0 and higher    | Linux / UNIX     | 3.6 and higher | Argcomplete Psycopg2 2.4.2 and higher Python-dateutil setuptools | 3.1.0 and higher | For RedHat Enterprise Linux, CentOS, and Scientific Linux, install the [Extra Packages Enterprise Linux (EPEL) repository](https://fedoraproject.org/wiki/EPEL) |

# Version Limitations

-   Python 2.6 and 3.5 aren’t supported.
-   Python 2.7 is only supported with Barman 3.4.X and will receive only bugfixes.
-   Python 3.6 won’t be supported in future releases of Barman.
-   PostgreSQL versions lower than 10 aren’t supported since Barman 3.0.0.
-   PostgreSQL 10 won’t be supported after Barman 3.5.0.

# Release Notes
For more information on release notes, bug fixes, and version history, see the [Barman Github](https://github.com/EnterpriseDB/barman/releases).