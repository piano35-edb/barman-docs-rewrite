# Incremental backup

Barman implements file-level incremental backup. Incremental backup is a type of full periodic backup which only saves data changes from the latest full backup available in the catalog for a specific PostgreSQL server. It must not be confused with differential backup, which is implemented by WAL continuous archiving.

!!!NOTE
   Block level incremental backup is planned for future versions of Barman.
!!!IMPORTANT
    The `reuse_backup` option can't be used with the postgres backup method.

The main goals of incremental backups in Barman are:

-   Reduce the time taken for the full backup process
-   Reduce the disk space occupied by several periodic backups (**data deduplication**)

This feature heavily relies on rsync and [hard links](https://en.wikipedia.org/wiki/Hard_link), which must therefore be supported by both the underlying operating system and the file system where the backup data resides.

The main concept is that a subsequent base backup will share those files that have not changed since the previous backup, leading to relevant savings in disk usage. This is particularly true of VLDB contexts and of those databases containing a high percentage of *read-only historical tables*.

Barman implements incremental backup through a global/server option called `reuse_backup`, that transparently manages the `barman backup` command. It accepts three values:

-   `off`: standard full backup (default)
-   `link`: incremental backup, by reusing the last backup for a server and creating a hard link of the unchanged files (for backup space and time reduction)
-   `copy`: incremental backup, by reusing the last backup for a server and creating a copy of the unchanged files (just for backup time reduction)

The most common scenario is to set `reuse_backup` to `link`, as follows:
```bash
reuse_backup = link
```
Setting this at global level will automatically enable incremental backup for all your servers.

You can override the setting of the `reuse_backup` option through the `--reuse-backup `runtime option for the `barman backup` command. Similarly, the runtime option accepts three values: `off, link`, and `copy`. For example, you can run a one-off incremental backup as follows:
```bash
barman backup --reuse-backup=link \<server_name\>
```