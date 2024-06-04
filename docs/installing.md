
# Installing
Barman can be installed on the following:

- Linux / Unix
- Windows *(experimental)*
- Docker

## Downloads

Official packages for Barman are distributed by EnterpriseDB through repositories listed on the [Downloads page](download.md).

[Download Barman](download.md){ .md-button }

!!!note
    These packages use the default **python3** version provided by the target operating system. If an alternative **python3** version is required, you must install Barman from source.

## Linux / Unix

!!!recommendation
    The recommended way to install Barman is by using the available packages for your GNU/Linux distribution.

### Red Hat Enterprise Linux (RHEL) and RHEL-based systems using RPM packages

Barman can be installed using RPM packages on:

- RHEL8
- RHEL7 
- Identical versions of the following RHEL derivatives: AlmaLinux, Oracle Linux, and Rocky Linux. 

You must install the Extra Packages Enterprise Linux (EPEL) repository and the [PostgreSQL Global Development Group RPM repository](https://yum.postgresql.org/) before installing Barman.

Official RPM packages and download instructions for Barman are distributed by EnterpriseDB via Yum through the [public RPM repository](https://rpm.2ndquadrant.com/).

To install Barman on a RHEL-based system, switch to the **root** user and run the following command:

```bash
 yum install barman
```

### Fedora RPMs

In addition to the Barman packages available in the EDB and PGDG repositories, Barman RPMs published by the Fedora project can be found in EPEL. 
!!!important
    Fedora RPMs are not maintained by the Barman developers!

Fedora RPMs use a different configuration layout than the packages available in the PGDG and EDB repositories:

-   EDB and PGDG packages use `/etc/barman.conf` as the main configuration file and `/etc/barman.d`for additional configuration files.
-   The Fedora packages use `/etc/barman/barman.conf` as the main configuration file and `/etc/barman/conf.d` for additional configuration files.

!!!bug
    The difference in configuration file layout means that upgrades between the EPEL and non-EPEL Barman packages can break existing Barman installations until configuration files are manually updated. It's recommended to use a single source repository for Barman packages. 
    
    To use a single source repository, add the following line to the definition of the repositories from which you don't want to obtain Barman packages:

    ```bash
    exclude=barman\* python\*-barman
    ```

Specifically, to use only Barman packages from the following repositories, add the exclude directive from above to repository definitions in:

**EDB**

- `/etc/yum.repos.d/epel.repo`
- `/etc/yum.repos.d/pgdg-\*.repo`

**PGDG**

- `/etc/yum.repos.d/epel.repo`
- `/etc/yum.repos.d/enterprisedb\*.repo`

**EPEL**

- `/etc/yum.repos.d/pgdg-\*.repo`
- `/etc/yum.repos.d/enterprisedb\*.repo`

### Debian/Ubuntu using packages

Barman can be installed on Debian and Ubuntu Linux systems using packages.

It is directly available in the official repository for Debian and Ubuntu, however, these repositories might not contain the latest available version. To obtain the latest version of Barman, the recommended method is to install both these repositories:

-   [Public APT repository](https://apt.2ndquadrant.com/), directly maintained by Barman developers
-   the [PostgreSQL Community APT repository](https://apt.postgresql.org/), by following instructions in the [APT section of the PostgreSQL Wiki](https://wiki.postgresql.org/wiki/Apt)

!!!note
    Thanks to the direct involvement of Barman developers in the PostgreSQL Community APT repository project, you will always have access to the most updated versions of Barman.

To install Barman on a Debian/Ubuntu-based system, switch to the **root** user and run the following command:

```bash
apt-get install barman
```

## SLES using packages

Barman can be installed on SLES systems using packages available in the [PGDG SLES repositories](https://zypp.postgresql.org/). Install the necessary repository by following the instructions available on the [PGDG site](https://zypp.postgresql.org/howtozypp/).

Supported SLES version: SLES 15 SP3.

To install Barman on a SLES-based system, switch to the **root** user and run the following command:

```bash
zypper install barman
```

## Installation from sources

!!!warning
    Manual installation of Barman from sources should only be performed by expert GNU/Linux users. Installing Barman this way requires system administration activities such as dependencies management, barmanuser creation, configuration of the barman.conf file, cron setup for the barman cron command, log management, and so on.

Create a system user called barman on the backup server. As barman user, download the sources and uncompress them.

For a system-wide installation, type:

```bash
barman@backup\$ ./setup.py build
```
*\# run this command with root privileges or through sudo*

```bash
barman@backup\# ./setup.py install
```

For a local installation, type:

```bash
barman@backup\$ ./setup.py install --user
```

The barman application will be installed in your user directory ([make sure that your PATH environment variable is set properly](https://docs.python.org/3/install/index.html#alternate-installation-the-user-scheme)).

[Barman is also available on the Python Package Index (PyPI)](https://pypi.python.org/pypi/barman/) and can be installed through pip.

## PostgreSQL client/server binaries

The following Barman features depend on PostgreSQL binaries:

|**Feature**|**Configuration**|**Requirement**|
|------------|------------|------------|
|Streaming backup|with `backup_method = postgres`|**pg_basebackup**|
|Streaming WAL archiving|with `streaming_archiver = on`|**pg_receivewal** or **pg_receivexlog**|
|Verifying backups|with barman `verify-backup`| **pg_verifybackup**|


Depending on the target OS, these binaries are installed with either the PostgreSQL client or server packages:

-   On RedHat/CentOS and SLES:
    -   The **pg_basebackup** and **pg_receivewal/pg_receivexlog** binaries are installed with the PostgreSQL client packages.
    -   The **pg_verifybackup** binary is installed with the PostgreSQL server packages.
    -   All binaries are installed in `/usr/pgsql-\${PG_MAJOR_VERSION}/bin`.

-   On Debian/Ubuntu:
    -   All binaries are installed with the PostgreSQL client packages.
    -   The binaries are installed in `/usr/lib/postgresql/\${PG_MAJOR_VERSION}/bin`.

You must ensure that either:

1.  The Barman user has the `bin` directory for the appropriate **PG_MAJOR_VERSION** on its path, or:
2.  The `path_prefix`option is set in the Barman configuration for each server and points to the `bin` directory for the appropriate **PG_MAJOR_VERSION**.

The `psql` program is recommended in addition to the above binaries. While Barman doesn't use it directly, the documentation provides examples of how it can be used to verify PostgreSQL connections are working as intended. The **psql** binary can be found in the PostgreSQL client packages.

## Third party PostgreSQL variants

When using Barman for backup and recovery of third-party PostgreSQL variants, check whether the PGDG client/server binaries described earlier are compatible with your variant. If they're incompatible, then choose and install compatible alternatives from their appropriate packages.
