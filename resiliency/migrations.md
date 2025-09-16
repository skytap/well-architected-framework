---
title: Principles of the reliability pillar
description: Describes the principles of the reliability pillar.
author: George Stamos - Director, Solutions Architect - Business Development
permalink: /resiliency/migrations/
---

# {{page.title}}

Building a reliable application in the cloud is different from traditional application development. While historically you may have purchased levels of redundant higher-end hardware to minimize the chance of an entire application platform failing, in the cloud, we acknowledge up front that failures will happen. Instead of trying to prevent failures altogether, the goal is to minimize the effects of a single failing component.

## Considerations for migration

The following section provides an overview of {{site.SoA}} architecture and is used as a lens to assess the reliability of an environment deployed in {{site.Brand}}.

Migration considerations include:

* {{site.SoA}} general architecture
* Supported LPARs
* [Cold Migrations (Backup and Restore)](./solutions/ColdMigrationsOverview.md)
* [Hot Migrations (Replication Sync)](./solutions/HotMigrationOverview.md)

## {{site.Brand}} discovery and migration – support and limits

See [Skytap service limits](https://help.skytap.com/overview-service-limits.html)

## High-Level considerations for migration to {{site.SoA}}

Migration to {{site.SoA}} can be migrated via a hot or cold/warm migration as depicted here.

![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/migrationmedia/media/image6.png)

## Definitions

**RTO** – Downtime of services, applications, and infrastructure for business continuity. For example, the amount of time it takes to get back up and running in the event of a disaster or outage.

**RPO** – Frequency of data backup. For example, the amount of time in which data may be lost.

The two numbers above, combined with contingencies like distance, amount of data rate change and available bandwidth will determine whether to employ a High Availability Tool or a Disaster Recovery Tool.

This is _very_ similar to a _warm_ versus _hot_ migration, and the same tools may be utilized.

![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/media/backuptypes.png)
