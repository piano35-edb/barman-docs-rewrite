# config-switch

|**Command** | **Category** |  **Description**| **Synopsis**|
|------------|--------------|-----------------|----------|
|`config-switch`|General|Apply a set of configuration overrides defined through a model to a Barman server|`barman config-switch`|

## Syntax
```bash
barman config-switch <server_name> <model_name>
```

## Details

The `config-switch` command is used to apply a set of configuration overrides defined through a model to a Barman server. 

The final configuration of the Barman server is composed of the configuration of the server plus the overrides applied by the selected model. Models are particularly useful for clustered environments, so you can create different configuration models which can be used in response to various scenarios such as failover events.

The command will only succeed if <model_name> exists and belongs to the same cluster as <server_name>.

There can only be one model active at a time. If you run the command twice with different models, only the overrides defined for the last one apply.

To unapply an existing active model for a server:
```bash
barman config-switch <server_name> --reset
```
It will take care of unapplying the overrides that were previously in place by the active model.

!!!tip
    This command can also be useful for recovering when you have a server with an active model which was previously configured but which no longer exists in your configuration.