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

Migration Considerations include

-   Skytap on Azure General Architecture

-   Supported LPARs

-   Warm Migration

-   Hot Migration

**Skytap on Azure General Architecture**

Here is a high-level look at the Skytap on Azure general architecture.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/migrationmedia/media/image1.png" width="800">

- **Define and test availability and recovery targets -** Availability targets, such as Service Level Agreements (SLA) and Service Level Objectives (SLO), and Recovery targets, such as Recovery Time Objectives (RTO) and Recovery Point Objectives (RPO), should be defined and tested to ensure reliability aligns with business requirements.

- **Design environments to be resistant to failures -** Resilient environment architectures should be designed to recover gracefully from failures in alignment with defined reliability targets.

- **Ensure required capacity and services are available in targeted regions -** Azure services and capacity can vary by region, so it's important to understand if targeted regions offer required capabilities.

- **Plan for disaster recovery -** Disaster recovery is the process of restoring application functionality in the wake of a catastrophic failure. It might be acceptable for some applications to be unavailable or partially available with reduced functionality for a period of time, while other applications may not be able to tolerate reduced functionality.

- **Ensure networking and connectivity meets reliability requirements -** Identifying and mitigating potential network bottle-necks or points-of-failure supports a reliable and scalable foundation over which resilient application components can communicate.

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

**Skytap on Azure Support and Limits for IBM i**

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/migrationmedia/media/image4.png" width="800">

**Skytap on Azure Support and Limits for AIX**

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/migrationmedia/media/image5.png" width="800">

High-Level Considerations for Migration to Skytap on Azure

Migration to Skytap on Azure can be migrated via a hot or cold/warm
migration as depicted here. 

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/migrationmedia/media/image6.png" width="600">

# Backup and disaster recovery for Skytap applications

*Disaster recovery* is the process of restoring application functionality in the wake of a catastrophic loss.

In the cloud, we acknowledge up front that failures will happen. Instead of trying to prevent failures altogether, the goal is to minimize the effects of a single failing component. Testing is one way to minimize these effects. You should automate testing your applications where possible, but you need to be prepared for when they fail. When this happens, having backup and recovery strategies becomes important.

Your tolerance for reduced functionality during a disaster is a business decision that varies from one application to the next. It might be acceptable for some applications to be unavailable or to be partially available with reduced functionality or delayed processing for a period of time. For other applications, any reduced functionality is unacceptable. 

## Disaster recovery plan

Start by creating a recovery plan. The plan is considered complete after it has been fully tested. Include the people, processes, and applications needed to restore functionality within the service-level agreement (SLA) you've defined for your customers.

Consider the following suggestions when creating and testing your disaster recovery plan:

- Include the process for contacting support and escalating issues. This information will help to avoid prolonged downtime as you work out the recovery process for the first time.
- Evaluate the business impact of application failures.
- Choose a cross-region recovery architecture for mission-critical applications.
- Identify a specific owner of the disaster recovery plan, including automation and testing.
- Document the process, especially any manual steps.
- Automate the process as much as possible.
- Establish a backup strategy for all reference and transactional data, and test backup restoration regularly.
- Set up alerts for the stack of the Skytap services consumed by your application.
- Train operations staff to execute the plan.
- Perform regular disaster simulations to validate and improve the plan.

## Backup strategy

Many alternative strategies are available for implementing distributed compute across regions. These must be tailored to the specific business requirements and circumstances of the application. At a high level, the approaches can be divided into the following categories:

- **Redeploy on disaster**: In this approach, the application is redeployed from scratch at the time of disaster. This is appropriate for non-critical applications that don’t require a guaranteed recovery time.

- **Warm Spare (Active/Passive)**: A secondary hosted service is created in an alternate region, and roles are deployed to guarantee minimal capacity; however, the roles don’t receive production traffic. This approach is useful for applications that have not been designed to distribute traffic across regions.

- **Hot Spare (Active/Active)**: The application is designed to receive production load in multiple regions. The cloud services in each region might be configured for higher capacity than required for disaster recovery purposes. Alternatively, the cloud services might scale-out as necessary at the time of a disaster and fail-over. This approach requires substantial investment in application design, but it has significant benefits. These include low and guaranteed recovery time, continuous testing of all recovery locations, and efficient usage of capacity.

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
>**Migration Solutions**
>* [Hot Migrations (Replication Sync)](solutions/HotMigrationOverview.md)
>* [Cold (Warm) Migrations (Backup and Restore)](solutions/ColdMigrationsOverview.md)

>**Design**
>* [Design Considerations for Azure](designconsiderationsazure.md)
 
<!--- >* [Design Considerations for IBM Cloud](designconsiderationsibm.md) --->


**Security**
> * [Skytap Security Pillar](../security/README.md)

