To troubleshoot a Barman installation, or for diagnostic and support purposes, use the following command:

|**Command** | **Category** | **Description** |
|------------|--------------|----------------|
|`barman diagnose`|  General | Returns configuration information and status for all nodes within a Barman installation.|

The `diagnose` command output provides a JSON-formatted report with a full view of the barman server, including:

- Global configuration
- SSH version
- Python version
- rsync version
- PostgreSQL clients version,
- Current configuration of all servers 
- Current status of all servers

!!!info
    For Barman 3.10.0 and higher versions, you can optionally specify the `--show-config-source` argument. When the argument is provided, for each configuration option of Barman and of the Barman servers, the output will include not only the configuration value, but also the configuration file which provides the effective value.

!!!important
    Even if the diagnostic report is provided in JSON and that format is thought to be machine readable, its structure is not to be considered part of the interface. The format can change between different Barman versions.

    