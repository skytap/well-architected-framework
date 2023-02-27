---
title: Principles of the reliability pillar
description: Describes the principles of the reliability pillar.
author: George Stamos - Director, Solutions Architect - Business Development
permalink: /resiliency/migrations/
---

# Principles of the reliability pillar

Building a reliable application in the cloud is different from traditional application development. While historically you may have purchased levels of redundant higher-end hardware to minimize the chance of an entire application platform failing, in the cloud, we acknowledge up front that failures will happen. Instead of trying to prevent failures altogether, the goal is to minimize the effects of a single failing component.

## Considerations for Migration

The following section provides an overview of Skytap on Azure Architecture and is used as a lens to assess the reliability of an environment deployed in Skytap. 

Migration Considerations include:

-   Skytap on Azure General Architecture

-   Supported LPARs

-   [Cold Migrations (Backup and Restore)](./solutions/ColdMigrationsOverview.md)

-   [Hot Migrations (Replication Sync)](./solutions/HotMigrationOverview.md)

**Skytap Discovery and Migration -- Support and Limits**

The following section outlines Skytap on Azure support and limits for
IBM i and AIX.

***Skytap for Azure Support and Limits (IBM i)***


**Compute**

LPAR Sizing and OS Support
for Power

* S922 System p Frames
* IBM i P10 License Class
* 512 GB RAM per LPAR
* CPW up to 60000 per LPAR
* Supports:
    * IBM i:  7.2 TR 9, 7.3 TR 5, 7.4

**Network**

* IPSec (SDN) / PNC Connectivity Between Skytap and On-Premises
  * Range: 750 Mbps Limit
* Layer 2 networking in environment
  * Range: Burst to 2.7 Gbps Limit
* 10 NICs Per Host
* 10 VPN Connections Per Account


**Storage**

* Limit of 32 Disks for POWER
* Each Disk Limited to 2TB 
* Storage performance scalability with VM/LPAR RAM capacity
* IOPS up to 30K per LPAR

***Skytap for Azure Support and Limits (AIX)***

**Compute**

LPAR Sizing and OS Support for Power

* S922 System p Frames
* IBM i P10 License Class
* Up to 16 vCPU / 512 GB RAM per LPAR
* Higher RAM capacity supported on as needed basis (~1.6TB)
* Supports:
    * AIX: 6.1 TL 9 SP 10
    * AIX: 7.1 TL 4 SP 4, 
    * AIX: 7.2 TL 1, SP 2

**Network**

* IPSec (SDN) / PNC Connectivity Between Skytap and On-Premises
  * Range: 750 Mbps Limit
* Layer 2 networking in environment
  * Range: Burst to 2.7 Gbps Limit
* 10 NICs Per Host
* 10 VPN Connections Per Account


**Storage**
* Limit of 32 Disks for POWER
* Each Disk Limited to 2TB 
* Storage performance scalability with VM/LPAR RAM capacity
* All SSD technology and encrypted
* IOPS up to 30K per LPAR

High-Level Considerations for Migration to Skytap on Azure

Migration to Skytap on Azure can be migrated via a hot or cold/warm
migration as depicted here. 

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/migrationmedia/media/image6.png" width="600">

## Definitions

**RTO**: Downtime of services, apps and infrastructure for business continuity – i.e., the amount of time it takes to get back up and running in the event of a disaster or outage.

**RPO**: Frequency of data backup – i.e., the amount of time in which data may be lost.

The two numbers above, combined with contingencies like distance, amount of data rate change and available bandwidth will determine whether to employ a High Availability Tool or a Disaster Recovery Tool.

This is VERY similar to a WARM versus HOT migration, and the same tools may be utilized.
<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/media/backuptypes.png" width="800">

### Next steps

**Main Overview**
> [Skytap Well-Architected Framework]({{ site.navigation.Home }})

**Operational Excellence**
> [Skytap Operational Excellence Pillar]({{ site.navigation.Operations }})

**Resiliency**
> [Skytap Resiliency Pillar]({{ site.navigation.Resiliency }})
> * [Protection]({{ site.navigation.Resiliency }}backups)
> * [Disaster Recovery]({{ site.navigation.Resiliency }}disaster-recovery)
> * [High Availability]({{ site.navigation.Resiliency }}ibmi-disaster-recovery)
>
> **Migration Solutions**
> * [Hot Migrations (Replication Sync)]({{ site.navigation.Resiliency }}solutions/hot-migrations)
> * [Cold (Warm) Migrations (Backup and Restore)]({{ site.navigation.Resiliency }}solutions/cold-migrations)
>
> **Design**
> * [Design Considerations for Azure]({{ site.navigation.Resiliency }}design-considerations-azure)
> * [Design Considerations for IBM Cloud]({{ site.navigation.Resiliency }}design-considerations-ibm)

**Security**
> [Skytap Security Pillar]({{ site.navigation.Security }})
