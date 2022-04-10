---
title: Principles of the reliability pillar
description: Describes the principles of the reliability pillar.
author: George Stamos - Director, Solutions Architect - Business Development
permalink: /resiliency/
---

# Principles of the reliability pillar

Building a reliable application in the cloud is different from traditional application development. While historically you may have purchased levels of redundant higher-end hardware to minimize the chance of an entire application platform failing, in the cloud, we acknowledge up front that failures will happen. Instead of trying to prevent failures altogether, the goal is to minimize the effects of a single failing component.

## Considerations for Migration

The following section provides an overview of Skytap on Azure Architecture and is used as a lens to assess the reliability of an environment deployed in Skytap. 

Migration Considerations include:

-   Skytap on Azure General Architecture

-   Supported LPARs

-   [Cold Migrations (Backup and Restore)](solutions/ColdMigrationsOverview.md)

-   [Hot Migrations (Replication Sync)](solutions/HotMigrationOverview.md)

**Skytap Discovery and Migration -- Support and Limits**

The following section outlines Skytap on Azure support and limits for
IBM i and AIX.

**Skytap on Azure Support and Limits for IBM i**

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/migrationmedia/media/image4.png" width="800">

**Skytap on Azure Support and Limits for AIX**

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/migrationmedia/media/image5.png" width="800">

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
> [Skytap Well-Architected Framework](../README.md)

**Operational Excellence**
>[Skytap Operational Excellence Pillar](../operations/README.md)

**Resiliency**
>[Skytap Resiliency Pillar](README.md)
>* [Protection](backups.md)
>* [Disaster Recovery](disasterrecovery.md)
>* [High Availability](ibmihadr.md)
>
>**Migration Solutions**
>* [Hot Migrations (Replication Sync)](solutions/HotMigrationOverview.md)
>* [Cold (Warm) Migrations (Backup and Restore)](solutions/ColdMigrationsOverview.md)
>
>**Design**
>* [Design Considerations for Azure](designconsiderationsazure.md)
>* [Design Considerations for IBM Cloud](designconsiderationsibm.md)


**Security**
> * [Skytap Security Pillar](../security/README.md)

