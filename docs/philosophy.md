# Philosophy
In a perfect world, there would be no need for a backup. In the real world, especially in business environments, one must be prepared for the *"unexpected"*. Unexpected database events might present in the following forms:

-   Data corruption
-   Software failure
-   Hardware failure
-   Human error
-   Natural disaster

## Business continuity

A DBA should be able to recover from any of these unexpected events, and restore the database in the shortest time possible. We normally refer to this discipline as *disaster recovery*, and more broadly *business continuity*.

Within business continuity, it is important to familiarise yourself with two fundamental metrics, as defined by Wikipedia:

-   [**Recovery Point Objective (RPO)**](https://en.wikipedia.org/wiki/Recovery_point_objective): The maximum targeted period in which data might be lost from an IT service due to a major incident.

-   [**Recovery Time Objective (RTO)**](https://en.wikipedia.org/wiki/Recovery_time_objective): The targeted duration of time and a service level within which a business process must be restored after a disaster (or disruption) in order to avoid unacceptable consequences associated with a break in business continuity.

In more simplistic terms, RPO represents the maximum amount of data you can afford to lose, while RTO represents the maximum downtime that you can afford for your service.

Ideally, zero data loss and zero down-time (**RTO=0** / **RPO=0**) are preferable - even for non-critical systems. Achieving those metrics requires a careful cost analysis and conversation to determine your business continuity requirements.

Fortunately, with an open source stack composed of **Barman** and **PostgreSQL**, you can achieve **RPO=0**, thanks to synchronous streaming replication. RTO is more the focus of a *High Availability* solution, like [**repmgr**](https://www.repmgr.org/). Therefore, by integrating **Barman** and **repmgr**, you can dramatically reduce RTO to nearly zero.

Based on our experience at [EnterpriseDB](https://www.enterprisedb.com/), we can confirm that PostgreSQL open source clusters with **Barman** and **repmgr** can easily achieve more than 99.99% uptime over a year, if properly configured and monitored.

## Mission
Our mission with Barman is to promote a culture of disaster recovery that:

-   Focuses on backup and recovery procedures.
-   Relies on education and training on strong theoretical and practical concepts of PostgreSQL's crash recovery, backup, Point-In-Time-Recovery, and replication.
-   Promotes testing your backups (only a backup that is tested can be considered to be valid), either manually or automatically (be creative with Barman's hook scripts!)
-   Fosters regular practice of recovery procedures.
-   Solicits regularly scheduled drills and disaster recovery simulations.
-   Relies on continuous monitoring of PostgreSQL and Barman to promptly identify any anomalies.

## Be prepared
It's important to prepare yourself and your team for when unexpected events happen *(yes, *when*, not *if*)*, because, in the experiences of many:

-   It's going to be on a Friday evening, most likely right when you're about to leave the office.
-   It's going to be when you're on vacation and somebody else has to deal with it.
-   You'll regret not being certain that the last available backup is valid.
-   Every second will seem like forever, unless you know how long it takes to recover.
-   It's certainly going to be stressful.

**Don't be scared.  Be prepared.**

