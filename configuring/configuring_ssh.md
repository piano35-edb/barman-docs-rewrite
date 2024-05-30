# SSH connections

SSH is a protocol and a set of tools that allows you to open a remote shell to a remote server and copy files between the server and the local system. For more information, see ["SSH Essentials"](https://www.digitalocean.com/community/tutorials/ssh-essentials-working-with-ssh-servers-clients-and-keys) by Digital Ocean.

SSH key exchange is a very common practice that is used to implement secure passwordless connections between users on different machines, and it's needed to use rsync for WAL archiving and for backups.

!!!note
    This procedure is not needed if you plan to use the streaming connection only to archive transaction logs and backup your PostgreSQL server.

**SSH configuration of postgres user**

Create an SSH key for the PostgreSQL user. Log in as **postgres**, in the pg host run the following command:

```bash
postgres@pg\$ ssh-keygen -t rsa
```
As this key must be used to connect from hosts without providing a password, no passphrase should be entered during the key pair creation.

**SSH configuration of barman user**

Create an SSH key for the Barman user. Log in as **barman** in the backup host and type:
```bash
barman@backup\$ ssh-keygen -t rsa
```
For the same reason, no passphrase should be entered.

**From PostgreSQL to Barman**

The SSH connection from the PostgreSQL server to the backup server is needed to correctly archive WAL files using the `archive_command` setting.

To connect from the PostgreSQL server to the backup server, the PostgreSQL public key must be configured into the authorized keys of the backup server for the **barman** user.

The public key to be authorized is stored inside the **postgres** user home directory in `.ssh/id_rsa.pub`, and its contents should be included in a file named `.ssh/authorized_keys` inside the home directory of the **barman** user in the backup server. If the `authorized_keys` file doesn't exist, create it using `600` as permissions.

The following command should succeed without any output if the SSH key pair exchange has been completed successfully:
```bash
postgres@pg\$ ssh barman@backup -C true
```
The value of the `archive_command` configuration parameter will be discussed in the *"WAL archiving via `archive_command` section"*.

**From Barman to PostgreSQL**

The SSH connection between the backup server and the PostgreSQL server is used for the traditional backup over rsync. Just as with the connection from the PostgreSQL server to the backup server, we should authorize the public key of the backup server in the PostgreSQL server for the **postgres** user.

The content of the file `.ssh/id_rsa.pub` in the barman server should be put in the file named `.ssh/authorized_keys` in the PostgreSQL server. The permissions of that file should be `600`.

The following command should succeed without any output if the key pair exchange has been completed successfully.
```bash
barman@backup\$ ssh postgres@pg -C true
```