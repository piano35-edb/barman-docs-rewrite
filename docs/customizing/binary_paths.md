Barman allows users to specify one or more directories where Barman looks for executable files, using the global/server option `path_prefix`.

## Details

If a `path_prefix` is provided, it must contain a list of one or more directories separated by colon. Barman will search inside these directories first, then in those specified by the `PATH` environment variable.

By default the `path_prefix` option is empty.

## Supported versions

`path_prefix` is supported for Barman version 1.6.0 and higher.