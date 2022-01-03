---
title: Hot Migration Overview
description: Skytap Hot Migration Partner Solutions
author: George Stamos, Director, Solutions Architect - Business Development
---

## Skytap on Azure + Precisely for High Availability Scenarios

For hot migrations, Skytap partners with Precisely, the market leader for high availability scenarios. Precisely supports IBM i and AIX. How Precisely supports the Skytap on Azure hot migration for IBM i is depicted below.

<b>Quick Recap: What is Precisely?</b>

- MIMIX is the most prevalent tool used in RPO scenarios of near-zero
- Easy to Package – Support for both AIX and IBM I
- Automated Role Swaps Allow for Testing, Operations Upgrades in Prod
- AIX Solution Provides Live Snapshot Capability for Any Subsidiary Environment Seeding

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/media/preciselyhotmigrationtopo.png" width="800">

This is built on IBM's remote journaling which replicates changed data in real-time and minimizes bandwidth usage. The MIMX change data capture replicates non-journaled data. This solution is flexible and platform independent because it supports mixed hardware, storage and IBM I OS version. It is also optimized for performance because it:

-   delivers near-zero RPO (zero with synchronous Remote Journaling)

-   provides near-zero RTO by maintaining a hot, switch-ready back up

-   includes performance tuning features to optimize performance for
    your environment (i.e. parallel applies and database caching)

-   has active-active replication with built-in conflict resolution that
    delivers RTO in seconds (as quickly as moving users to another
    active server)

The following outlines popular Precisely migration scenarios available for you to consider:

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/migrationmedia/media/image10.png" width="800">

**Built on IBM’s remote journaling**

- Replicates changed data in real time
- Minimizes bandwidth usage
- MIMIX change data capture replicates non-journaled data

**Flexible and platform independent**

- Supports mixed hardware, storage and IBM i OS version

**Optimized for performance**

- Delivers near-zero RPO (zero with synchronous Remote Journaling)
- Provides near-zero RTO by maintaining a hot, switch-ready backup
- Performance tuning features optimize performance for your environment (e.g. parallel applies and database caching)
- Active-active replication with built-in conflict resolution delivers RTO in seconds (as quickly as moving users to another active server)

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/media/preciselyhotmigrationscenerios.png" width="800" alt="preciselytopo">

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/migrationmedia/media/image9.png">

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/media/mimixibmirunbookoutline.png" width="800">

**What's different about AIX?**

- The AIX Mimix Product focuses on Volume Group replication and sync
- Any applications encapsulated in the Volume Group are included as part of the replication
- The Root Volume Group is NOT Replicated (Needs to be Handled Separately) [There are some known scripts for handling initial sync of Root Volume Group]
- Point in Time Snapshot of Volume Groups
- Inline Encryption

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/migrationmedia/media/image11.png">

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/media/mimixaixrunbookoutline.png" width="800">

## Additional Solutions

- **[Skytap Live Clone Feature](liveclone.md)**

### Next steps

**Main Overview**
> [Skytap Well-Architected Framework](../../README.md)

**Operational Excellence**
>[Skytap Operational Excellence Pillar](../../operations/README.md)

**Resiliency**
> [Skytap Resiliency Pillar](../../resiliency/README.md)

>**Migration Solutions**
>* [Cold (Warm) Migrations (Backup and Restore)](ColdMigrationsOverview.md)

>**Design**
>* [Design Considerations for Azure](../designconsiderationsazure.md)

<!--- >* [Design Considerations for IBM Cloud](../designconsiderationsibm.md) --->

**Security**
> * [Skytap Security Pillar](../../security/README.md)
