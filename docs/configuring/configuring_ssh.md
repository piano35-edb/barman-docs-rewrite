# SSH connections

SSH is a protocol and a set of tools that allows you to open a remote shell to a remote server and copy files between the server and the local system. For more information, see ["SSH Essentials"](https://www.digitalocean.com/community/tutorials/ssh-essentials-working-with-ssh-servers-clients-and-keys) by Digital Ocean.

SSH key exchange is a common practice used to implement secure passwordless connections between users on different machines. It's required for using rsync with WAL archiving and backups.

!!!note
    This procedure isn't needed if you plan to use the streaming connection only to archive transaction logs and backup your PostgreSQL server.

# Configuring

## SSH configuration of the **postgres** user

To create an SSH key for the PostgreSQL user, log into the **pg** host as **postgres** and run the following command:

```bash
postgres@pg\$ ssh-keygen -t rsa
```
This key must be used to connect from hosts without providing a password, so don't enter a passphrase when creating the key pair.

## SSH configuration of the **barman** user

To create an SSH key for the Barman user, log into the backup host as **barman** and run the following command:
```bash
barman@backup\$ ssh-keygen -t rsa
```
This key must be used to connect from hosts without providing a password, so don't enter a passphrase when creating the key pair.

## Connectivity from PostgreSQL to Barman

The SSH connection from the PostgreSQL server to the backup server is required to correctly archive WAL files using the `archive_command` setting.

To connect from the PostgreSQL server to the backup server, the PostgreSQL public key must be added to the authorized keys of the backup server for the **barman** user.

The public key to be authorized is stored inside the **postgres** user home directory in `.ssh/id_rsa.pub`, and its contents should be included in a file named `.ssh/authorized_keys` inside the home directory of the **barman** user in the backup server. 

If the `authorized_keys` file doesn't exist, create it.  The permissions of that file should be `600`.

The following command should succeed without any output if the SSH key pair exchange has been completed successfully:
```bash
postgres@pg\$ ssh barman@backup -C true
```

## Connectivity from Barman to PostgreSQL

The SSH connection between the backup server and the PostgreSQL server is used for traditional backup over rsync. Just as with the connection from the PostgreSQL server to the backup server, authorize the public key of the backup server in the PostgreSQL server for the **postgres** user.

The content of the file `.ssh/id_rsa.pub` in the Barman server should be added to the file named `.ssh/authorized_keys` in the PostgreSQL server. The permissions of that file should be `600`.

The following command should succeed without any output if the key pair exchange has been completed successfully.
```bash
barman@backup\$ ssh postgres@pg -C true
```