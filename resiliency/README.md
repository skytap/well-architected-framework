---
title: Principles of the reliability pillar
description: Describes the principles of the reliability pillar.
author: George Stamos - Director, Solutions Architect - Business Development
permalink: /resiliency/
---

# Principles of the reliability pillar

Building a reliable application in the cloud is different from traditional application development. While historically you may have purchased levels of redundant higher-end hardware to minimize the chance of an entire application platform failing, in the cloud, we acknowledge up front that failures will happen. Instead of trying to prevent failures altogether, the goal is to minimize the effects of a single failing component.

## {{site.SoA}} general architecture

Here is a high-level look at the {{site.SoA}} general architecture.

![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/migrationmedia/media/image1.png)

* **Define and test availability and recovery targets** – Availability targets, such as Service Level Agreements (SLAs) and Service Level Objectives (SLOs), and recovery targets, such as Recovery Time Objectives (RTOs) and Recovery Point Objectives (RPOs), should be defined and tested to ensure reliability aligns with business requirements.
* **Design environments to be resistant to failures** – Resilient environment architectures should be designed to recover gracefully from failures in alignment with defined reliability targets.
* **Ensure required capacity and services are available in targeted regions** – {{site.Azure}} services and capacity can vary by region, so it's important to understand if targeted regions offer required capabilities.
* **Plan for disaster recovery** – Disaster recovery is the process of restoring application functionality in the wake of a catastrophic failure. It might be acceptable for some applications to be unavailable or partially available with reduced functionality for a period of time, while other applications may not be able to tolerate reduced functionality.
* **Ensure networking and connectivity meets reliability requirements** – Identifying and mitigating potential network bottlenecks or points-of-failure supports a reliable and scalable foundation over which resilient application components can communicate.
* **Allow for reliability in scalability and performance** – Resilient applications should be able to automatically scale in response to changing load to maintain application availability and meet performance requirements.
* **Address security-related risks** – Identifying and addressing security-related risks helps to minimize application downtime and data loss caused by unexpected security exposures.

## {{site.Brand}} service layers

{{site.Brand}} is composed of three service layer tiers as depicted below:

* **Data Platform Tier** – includes Bare Metal infrastructure, hosting service, storage service and network service.
* **Platform Tier** – includes business logic, accounting, quote manager, internal API and workflow engine and platform services (containers, shared drive, metadata, etc.)
* **Web Tier** – includes HTML UI, RESTful API, Import Export and SmartClient Service

![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/migrationmedia/media/image2.png)

## {{site.SoA}}: {{site.Azure}} region, {{site.Brand}} region and connection to {{site.Azure}} native services

Within a given {{site.Azure}} region, {{site.Brand}} standard Power VMs and x86 VMs and {{site.Azure}}-dedicated S922 and x86 bare metal and storage are connected via ExpressRoute to {{site.Azure}} native services.

![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/migrationmedia/media/image3.png)

## {{site.Brand}} discovery and migration – support and limits

The following section outlines {{site.SoA}} support and limits for IBM i and AIX.

### {{site.Brand}} service limits

See [Skytap service limits](https://help.skytap.com/overview-service-limits.html)

## High-Level considerations for migration to {{site.SoA}}

Migration to {{site.SoA}} can be migrated via a hot or cold/warm migration.

![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/migrationmedia/media/image6.png)

### Definitions

**RTO** – Downtime of services, applications, and infrastructure for business continuity. For example, the amount of time it takes to get back up and running in the event of a disaster or outage.

**RPO** – Frequency of data backup. For example, the amount of time in which data may be lost.

The two numbers above, combined with contingencies such as distance, amount of data rate change, and available bandwidth will determine whether to employ a High Availability tool or a Disaster Recovery tool.

This is _very_ similar to a _warm_ versus _hot_ migration, and the same tools may be utilized.

![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/media/backuptypes.png)

### Next steps

The sections below in this guide can help the strategy required for the organizational change management required to consistently ensure strong reliability.

These are the disciplines we group in the Reliability Pillar:

| Operational excellence disciplines                                    | Description                                                                                                                                                                                                                                                        |
|:----------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Migration]({{site.navigation.Resiliency}} migrations)                | The following section provides an overview of {{site.SoA}} architecture and is used as a lens to assess the reliability of an environment deployed in {{site.Brand}}.                                                                                              |
| [Protection]({{site.navigation.Resiliency}} backups)                  | In the cloud, we acknowledge up front that failures will happen. Instead of trying to prevent failures altogether, the goal is to minimize the effects of a single failing component by keeping your data safe; in this situation, RPO is more critical than RTO.  |
| [Disaster Recovery]({{site.navigation.Resiliency}} disaster-recovery) | Disaster recovery is the process of restoring application functionality in the wake of a catastrophic loss. A comprehensive disaster recovery solution that can restore data quickly and completely is required to meet low RPO and RTO thresholds.                |
| [High Availability]({{site.navigation.Resiliency}} high-availability) | Avoiding down time and keeping your critical applications and data online—a high availability solution is required for high RPO and RTO.                                                                                                                           |
