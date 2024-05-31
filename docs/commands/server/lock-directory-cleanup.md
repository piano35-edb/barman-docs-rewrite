# lock-directory-cleanup

|**Command** | **Category** |  **Description**| **Synopsis**|
|------------|--------------|-----------------|----------|
|`lock-directory-cleanup`|Server|Automatically cleans up the `barman_lock_directory` from unused lock files|`barman lock-directory-cleanup`|

# Details

`lock-directory-cleanup'`will automatically clean up the `barman_lock_directory` from unused lock files.

Barman allows you to specify a directory for lock files through the `barman_lock_directory` global option.

Lock files are used to coordinate concurrent work at global and server level (for example, cron operations, backup operations, access to the WAL archive, and so on.).

By default (for backward compatibility reasons), `barman_lock_directory` is set to `barman_home`.

!!!tip
    Use a directory in a volatile partition, such as the one dedicated to run-time variable data (e.g. `/var/run/barman`).