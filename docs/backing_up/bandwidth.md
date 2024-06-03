# Limiting bandwidth usage

It is possible to limit the usage of I/O bandwidth through the `bandwidth_limit` option (global/per server), by specifying the maximum number of kilobytes per second. The default is 0, or no limit.

!!!IMPORTANT
    The `bandwidth_limit` option is supported with the postgres backup method, but the `tablespace_bandwidth_limit` option is available only if you use rsync.

If you have several tablespaces and prefer to limit the I/O workload of your backup procedures on one or more tablespaces, you can use the `tablespace_bandwidth_limit` option (global/per server):
```bash
tablespace_bandwidth_limit = tbname:bwlimit[, tbname:bwlimit, ...]
```
The option accepts a comma-separated list of pairs made up of the tablespace name and the bandwidth limit (in kilobytes per second).

When backing up a server, Barman will try to locate any existing tablespace listed in the `tablespace_bandwidth_limit` option. If a tablespace is located, the specified bandwidth limit will be enforced. If not, the default bandwidth limit for that server will be applied.
