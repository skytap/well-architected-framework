---
title: Hot Migration Overview
description: Skytap Hot Migration Partner Solutions
author: George Stamos, Director, Solutions Architect - Business Development
permalink: /resiliency/solutions/hot-migrations/
---

## Skytap on Azure + Precisely for High Availability Scenarios

For hot migrations, Skytap partners with Precisely, the market leader for high availability scenarios. Precisely supports IBM i and AIX. How Precisely supports the Skytap on Azure hot migration for IBM i is depicted below.

### What is Precisely for IBM iSeries?

- Precisely's MIMIX is the most prevalent tool used in RPO scenarios of near-zero
- Easy to Package – Support for both AIX and IBM i
- Automated Role Swaps Allow for Testing, Operations Upgrades in Prod
- AIX Solution Provides Live Snapshot Capability for Any Subsidiary Environment Seeding

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/media/preciselyhotmigrationtopo.png" width="800">

This is built on IBM's remote journaling which replicates changed data in real-time and minimizes bandwidth usage. The MIMIX change data capture replicates non-journaled data. This solution is flexible and platform independent because it supports mixed hardware, storage and IBM i OS version. It is also optimized for performance because it:

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
- Performance tuning features optimize performance for your environment (i.e. parallel applies and database caching)
- Active-active replication with built-in conflict resolution delivers RTO in seconds (as quickly as moving users to another active server)

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/media/preciselyhotmigrationscenerios.png" width="800" alt="preciselytopo">

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/migrationmedia/media/image9.png">

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/media/mimixibmirunbookoutline.png" width="800">

#### Additional Resources

- **<a href="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/migrationmedia/SKYTAP-Systemi-MIMIX-Runbook.pdf" target="_blank">Skytap + Precisely MIMIX Runbook for IBM iSeries</a>**

### What's different about AIX?

- The AIX Mimix Product focuses on Volume Group replication and sync
- Any applications encapsulated in the Volume Group are included as part of the replication
- The Root Volume Group is NOT Replicated (Needs to be Handled Separately) [There are some known scripts for handling initial sync of Root Volume Group]
- Point in Time Snapshot of Volume Groups
- Inline Encryption

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/migrationmedia/media/image11.png">

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/media/mimixaixrunbookoutline.png" width="800">

#### Additional Resources

- **<a href="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/migrationmedia/Mimix-AIX-Runbook-V522.pdf">Skytap + Precisely MIMIX Runbook for AIX</a>**

#### Additional Solutions

- **[Skytap Live Clone Feature]({{ site.navigation.Resiliency }}solutions/live-clone)**

### Next steps

**Main Overview**
> [Skytap Well-Architected Framework]({{ site.navigation.Home }})

**Operational Excellence**
> [Skytap Operational Excellence Pillar]({{ site.navigation.Operations }})

**Resiliency**
> [Skytap Resiliency Pillar]({{ site.navigation.Resiliency }})
> * [Migration]({{ site.navigation.Resiliency }}migrations)
> * [Protection]({{ site.navigation.Resiliency }}backups)
> * [Disaster Recovery]({{ site.navigation.Resiliency }}disaster-recovery)
> * [High Availability]({{ site.navigation.Resiliency }}high-availability)
>
> **Migration Solutions**
> * [Cold (Warm) Migrations (Backup and Restore)]({{ site.navigation.Resiliency }}solutions/cold-migrations)
>
> **Design**
> * [Design Considerations for Azure]({{ site.navigation.Resiliency }}design-considerations-azure)
> * [Design Considerations for IBM Cloud]({{ site.navigation.Resiliency }}design-considerations-ibm)

**Security**
> [Skytap Security Pillar]({{ site.navigation.Security }})
