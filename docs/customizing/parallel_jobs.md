# Parallel jobs

By default, Barman uses only one worker for file copy during both backup and recover operations. Starting from version 2.2, it is possible to customize the number of workers that will perform file copy. In this case, the files to be copied will be equally distributed among all parallel workers.

It can be configured in global and server scopes, adding these in the corresponding configuration file:
```bash
parallel_jobs = n
```
where `n` is the desired number of parallel workers to be used in file copy operations. The default value is 1.

You can override this value at run-time when executing backup or recover commands. For example, you can use 4 parallel workers as follows:
```bash
barman backup --jobs 4 server1
```
Or, alternatively:
```bash
barman backup --j 4 server1
```
!!!note 
    The parallel jobs feature is only available for servers configured through rsync/SSH. For servers configured through streaming protocol, Barman will rely on `pg_basebackup`, which is currently limited to only one worker.

## Parallel jobs and sshd MaxStartups

Barman limits the rate at which parallel Rsync jobs are started in order to avoid exceeding the maximum number of concurrent unauthenticated connections allowed by the SSH server. This maximum is defined by the sshd parameter `MaxStartups`.  If more than `MaxStartups` connections have been created but not yet authenticated then the SSH server may drop some or all of the connections resulting in a failed backup or recovery.

The default value of sshd `MaxStartups` on most platforms is 10. Barman starts parallel jobs in batches of 10 and does not start more than one batch of jobs within a one second time period. This yields an effective rate limit of 10 jobs per second.

This limit can be changed using the following two configuration options:

- `parallel_jobs_start_batch_size`: The maximum number of parallel jobs to start in a single batch.
- `parallel_jobs_start_batch_period`: The time period in seconds over which a single batch of jobs will be started.

For example, to ensure no more than five new Rsync jobs will be created within any two second time period:
```bash
parallel_jobs_start_batch_size = 5
parallel_jobs_start_batch_period = 2
```
The configuration options can be overridden using the following arguments with both `barman backup` and `barman recover` commands:

- `\--jobs-start-batch-size`
- `\--jobs-start-batch-period`