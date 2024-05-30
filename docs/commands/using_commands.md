# Using commands

## Categories

Barman has many commands and, for the sake of exposition, we can organize them into categories by scope.

**General commands**:
General commands apply to the entirity of a Barman server, that can back up many PostgreSQL servers.

**Server commands**:
Server commands, unlike general commands, act only on a specified server. 

**Backup commands**:
Backup commands work on a backup, which is taken from a certain server.  They work directly on backups already existing in Barman's backup catalog.

**Cloud commands**:
Cloud commands are specific to each cloud provider.

## Requirements

To run the general, server, and backup commands:

- Install the `barman-cli` package on your server(s).  For more information, see [Barman CLI](barman-cli.md).

To run the cloud commands:

- Install the `barman-cli-cloud` package on your server(s).  For more information, see [Barman CLI Cloud](../commands/cloud/cloud_commands_using.md/#installation).

## Command options

The following options can be used with all commands:

|**Argument**|**Description**|**Default**|**Example**|
|------------|---------------|-----------|-----------|
|`-h, --help`|Show the help message and exit| | |
|`\-v, --version`|Show the version number and exit| | |
|`-c CONFIG, --config` CONFIG|Use the specified configuration file.||
|`--color {never,always,auto}, --colour` {never,always,auto}|Whether to use colors in the output|auto||
|`-q, --quiet`|Do not output anything. Useful for cron scripts.|
|`-d, --debug`|Debug output|False||
|`--log-level {NOTSET,DEBUG,INFO,WARNING,ERROR,CRITICAL}`|Override the default log level||
|`-f {json,console}, --format {json,console}`|Output format|console||

