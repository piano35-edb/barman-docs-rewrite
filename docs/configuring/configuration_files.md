# Configuration Files

Barman has the following three types of configuration files:

|**File type**|**Location**|**Notes**|
|------------|------------|---------|
|Global / General / Main|`/etc/barman.conf` or `/etc/barman/barman.conf`|Contains general options such as main directory, system user, log file, and so on.|
|Server|`/etc/barman.d`|Must have a *.conf* suffix.  There's a configuration file for each server that gets backed up by Barman.|
|Model|`/etc/barman.d`|Must have a *.conf* suffix.|

- The configuration file location can be overridden on a per-user level by `$HOME/.barman.conf`.
- Configuration files follow the **INI** format.  
- Rows starting with `;` are comments.

!!!note
      Models define a set of configuration overrides which can be applied on top of the configuration of Barman servers that are part of the same cluster as the model, through the `barman config-switch` command.
!!!important
    For historical reasons, you can still have one single configuration file containing both global as well as server and model options. However, for maintenance reasons, this approach is deprecated.


## Configuration file directory

Barman supports the inclusion of multiple configuration files, through the `configuration_files_directory` option. Included files must contain only server specifications, not global configurations. If the value of `configuration_files_directory` is a directory, Barman reads all files with `.conf` extension that exist in that folder. For example, if you set it to `/etc/barman.d`, you can specify your PostgreSQL servers placing each section in a separate `.conf` file inside the `/etc/barman.d` folder.

## Configuration file parameters

Configuration files accept distinct types of parameters:

-   string
-   enum
-   integer
-   boolean *(Accepts `on/true/1` and `off/false/0`)*.

Parameters don't need to be quoted.

!!!note
    Some *enum* allows *off* but not *false*.

## Configuration file options scope

Every configuration option has a *scope*:

-   global
-   server
-   model
-   global/server: server options that can be generally set at global level

### Global

Global options are allowed in the *general section*, which is identified in the INI file by the *[barman]* label:

```bash
**[barman]**
*; ... global and global/server options go here*
```

### Server

Server options can only be specified in a *server section*, which is identified by a line in the configuration file, in square brackets (`[` and `]`). The server section represents the ID of that server in Barman. 

For example, the following specifies a section for the server named **pg**, which belongs to the **my-cluster** cluster:
```bash
**[pg]**
cluster=my-cluster
*; Configuration options for the server named 'pg' go here*
```

### Model

Model options can only be specified in a *model section*, which is identified the same way as a *server section*. There can't be any conflicts among the identifier of *server sections* and *model sections*. 

For example, the following specifies a section for the model named **pg:switchover**, which belongs to the **my-cluster cluster**:
```bash
**[pg:switchover]**
cluster=my-cluster
model=**true**
*; Configuration options for the model named 'pg:switchover', 
*; which belongs to the server which is configured with the option 'cluster=pg', go here*
```

## Reserved words

The following two reserved words can't be used as server names or as model names in Barman:

-   **barman**: identifier of the global section
-   **all**: a shortcut that allows you to execute some commands in sequence on every server managed by Barman

## Overrides

Barman implements the **convention over configuration** design paradigm, which attempts to reduce the number of options that you're required to configure, without losing flexibility. Some server options can be defined at global level and overridden at server level, allowing you to specify a generic behavior and refine it for one or more servers. These options have a global/server scope.
