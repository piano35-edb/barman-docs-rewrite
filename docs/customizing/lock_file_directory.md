# Lock file directory

Barman allows you to specify a directory for lock files through the `barman_lock_directory` global option.

Lock files are used to coordinate concurrent work at global and server level, such as:

- cron operations
- backup operations
- access to the WAL archive

`barman_lock_directory` is defaults to `barman_home`.

!!!TIP
    You can use a directory in a volatile partition, such as the one dedicated to run-time variable data (e.g. `/var/run/barman`).