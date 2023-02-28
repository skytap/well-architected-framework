---
title: Principles of the reliability pillar
description: Describes the principles of the reliability pillar.
author: George Stamos - Director, Solutions Architect - Business Development
permalink: /resiliency/
---

# Principles of the reliability pillar

Building a reliable application in the cloud is different from traditional application development. While historically you may have purchased levels of redundant higher-end hardware to minimize the chance of an entire application platform failing, in the cloud, we acknowledge up front that failures will happen. Instead of trying to prevent failures altogether, the goal is to minimize the effects of a single failing component.

**Skytap on Azure General Architecture**

Here is a high-level look at the Skytap on Azure general architecture.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/migrationmedia/media/image1.png" width="800">

- **Define and test availability and recovery targets -** Availability targets, such as Service Level Agreements (SLAs) and Service Level Objectives (SLOs), and Recovery targets, such as Recovery Time Objectives (RTOs) and Recovery Point Objectives (RPOs), should be defined and tested to ensure reliability aligns with business requirements.

- **Design environments to be resistant to failures -** Resilient environment architectures should be designed to recover gracefully from failures in alignment with defined reliability targets.

- **Ensure required capacity and services are available in targeted regions -** Azure services and capacity can vary by region, so it's important to understand if targeted regions offer required capabilities.

- **Plan for disaster recovery -** Disaster recovery is the process of restoring application functionality in the wake of a catastrophic failure. It might be acceptable for some applications to be unavailable or partially available with reduced functionality for a period of time, while other applications may not be able to tolerate reduced functionality.

- **Ensure networking and connectivity meets reliability requirements -** Identifying and mitigating potential network bottlenecks or points-of-failure supports a reliable and scalable foundation over which resilient application components can communicate.

- **Allow for reliability in scalability and performance -** Resilient applications should be able to automatically scale in response to changing load to maintain application availability and meet performance requirements.

- **Address security-related risks -** Identifying and addressing security-related risks helps to minimize application downtime and data loss caused by unexpected security exposures.

**Skytap Service Layers**

Skytap is comprised of three service layer tiers as depicted below:

-   Data Platform Tier -- includes Bare Metal infrastructure, hosting
    service, storage service and network service.

-   Platform Tier -- includes business logic, accounting, quote manager,
    internal API and workflow engine and platform services (containers,
    shared drive, metadata, etc.)

-   Web Tier -- includes HTML UI, RESTful API, Import Export and
    SmartClient Service

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/migrationmedia/media/image2.png" width="600">

**Skytap on Azure: Azure Region, Skytap Region and connection to Azure
Native Services**

Within a given Azure region, Skytap Standard Power VMs and x86 VMs and
Azure Dedicated S922 and x86 Bare Metal and Storage are connected via
ExpressRoute to Azure Native Services as depicted below.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/migrationmedia/media/image3.png" width="600">

**Skytap Discovery and Migration -- Support and Limits**

The following section outlines Skytap on Azure support and limits for
IBM i and AIX.

***Skytap for Azure Support and Limits (IBM i)***


**Compute**

LPAR Sizing and OS Support
for Power

* S922 Power Frames
* IBM i P10 License Class
* 512 GB RAM per LPAR
* CPW up to 60000 per LPAR
* Supports:
    * IBM i: 7.1 *
    * IBM i: 7.2 TR 9
    * IBM i: 7.3 TR 5
    * IBM i: 7.4
    
 *Some specialized attention is required
  
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

* S922 Power Frames
* IBM i P10 License Class
* Up to 16 vCPU / 512 GB RAM per LPAR
* Higher RAM capacity supported on as needed basis (~1.6TB)
* Supports:

  * AIX: 5.x (via versioned WPAR)
  * AIX: 6.1 TL 9 SP 10
  * AIX: 7.1 TL 4 SP 4 
  * AIX: 7.2 TL 1 SP 2

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

The sections below in this guide can help the strategy required for the organizational change management required to consistently ensure strong reliability.

These are the disciplines we group in the Reliability Pillar:

| Operational Excellence Disciplines | Description |
|-------------------|-------------|
| [Migration]({{ site.navigation.Resiliency }}migrations) | The following section provides an overview of Skytap on Azure Architecture and is used as a lens to assess the reliability of an environment deployed in Skytap.   |
| [Protection]({{ site.navigation.Resiliency }}backups) | In the cloud, we acknowledge up front that failures will happen. Instead of trying to prevent failures altogether, the goal is to minimize the effects of a single failing component by keeping your data safe; in this situation, RPO is more critical than RTO.  |
| [Disaster Recovery]({{ site.navigation.Resiliency }}disaster-recovery) | Disaster recovery is the process of restoring application functionality in the wake of a catastrophic loss. A comprehensive disaster recovery solution that can restore data quickly and completely is required to meet low RPO and RTO thresholds. |
| [High Availability]({{ site.navigation.Resiliency }}high-availability) | Avoiding down time and keeping your critical applications and data online -- a high availability solution is required for high RPO and RTO.

**Main Overview**
> [Skytap Well-Architected Framework]({{ site.navigation.Home }})

**Operational Excellence**
> [Skytap Operational Excellence Pillar]({{ site.navigation.Operations }})

**Resiliency**
> * [Migration]({{ site.navigation.Resiliency }}migrations)
> * [Protection]({{ site.navigation.Resiliency }}backups)
> * [Disaster Recovery]({{ site.navigation.Resiliency }}disaster-recovery)
> * [High Availability]({{ site.navigation.Resiliency }}high-availability)
>
> **Design**
> * [Design Considerations for Azure]({{ site.navigation.Resiliency }}design-considerations-azure)
> * [Design Considerations for IBM Cloud]({{ site.navigation.Resiliency }}design-considerations-ibm)


**Security**
> [Skytap Security Pillar]({{ site.navigation.Security }})

