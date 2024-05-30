# Diagnose

|**Command** | **Category** |  **Description**| **Synopsis**|
|------------|--------------|-----------------|----------|
|`barman diagnose`|General|Creates a JSON report useful for diagnostic and support purposes|`barman diagnose`|


# Details

The `diagnose` command creates a JSON report useful for diagnostic and support purposes. This report contains the following diagnostic information for all configured servers:

 - Global configuration
 - SSH version
 - Python version
 - Rsync version
 - Current configuration of all servers
 - Status of all servers

!!!NOTE
    From Barman 3.10.0 onwards you can optionally specify the `--show-config-source` argument to the command. In that case, for each configuration option of Barman and of the Barman servers, the output will include not only the configuration value, but also the configuration file which provides the effective value.

!!!IMPORTANT
    Even if the diagnose is written in JSON and that format is thought to be machine readable, its structure is not to be considered part of the interface. Format can change between different Barman versions.

