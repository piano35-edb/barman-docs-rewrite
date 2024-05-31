# config-update

|**Command** | **Category** |  **Description**| **Synopsis**|
|------------|--------------|-----------------|----------|
|`config-update`|Server|Create or update configuration of servers and models|`barman config-update`|

## Syntax
```bash
barman config-update <json_changes>
```

## Details

The `config-update` command is used to create or update configuration of servers and models in Barman.

`json_changes` should be a JSON string containing an array of documents. 

Each document must contain the following key:

`scope`: either server or model, depending on if you want to create or update a Barman server or a Barman model;

They must also contain either of the following keys, depending on value of scope:

- `server_name`: if scope is server, you should fill this key with the Barman server name.
- `model_name`: if scope is model, you should fill this key with the Barman model name.

Additionally, fill in each document with one or more Barman configuration options along with the desired values for them.

For example, to update the Barman server `my_server` with `archiver=on` and `streaming_archiver=off`:

```bash
barman config-update \
  ‘[{“scope”: “server”, “server_name”: “my_server”, “archiver”: “on”, “streaming_archiver”: “off”}]’
  ```

!!!info
    `barman config-update` writes the configuration options to a file named `.barman.auto.conf`, which is created under the `barman_home`. 
    
    That configuration file takes higher precedence and overrides values coming from the Barman global configuration file (typically `/etc/barman.conf`), and from included files as per `configuration_files_directory` (typically files in `/etc/barman.d`). 
    
!!!warning
    If you decide to manually change configuration options in those files, be aware of the potential impact to the Barman files.