This manual assumes that you are familiar with theoretical disaster recovery concepts, and that you have a grasp of PostgreSQL fundamentals in terms of physical backup and disaster recovery.

## Prerequisites

Before you start using Barman, you should become familiar with PostgreSQL and the following concepts:

- Base backups
- Physical backups
- Full and point-in-time recovery
- Replication
- WAL archiving

## Approach

After you have a solid understanding of those concepts, you can use the following approach for Barman:

1. Review your servers to ensure they meet the requirements and recommendations.
2. Choose an architecture for Barman.
3. Choose a backup method.
4. Install Barman on your servers.
5. Install the `barman-cli` package to use Barman commands.
6. (Optional) If your architecture includes a cloud provider, install `barman-cli-cloud` package to use Barman's cloud commands. 
7. Configure your backups.
8. Test your backup and recovery strategy.
9. Implement monitoring for Barman.
10. Customize your Barman configuration.
11. Document your Barman configuration, backup, and recovery procedures.
12. (Optional) Contribute to Barman.


