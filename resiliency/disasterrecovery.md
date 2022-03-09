---
title: Principles of the reliability pillar
description: Describes the principles of the reliability pillar.
author: George Stamos - Director, Solutions Architect - Business Development
permalink: /resiliency/
---

# Principles of the reliability pillar

Building a reliable application in the cloud is different from traditional application development. While historically you may have purchased levels of redundant higher-end hardware to minimize the chance of an entire application platform failing, in the cloud, we acknowledge up front that failures will happen. Instead of trying to prevent failures altogether, the goal is to minimize the effects of a single failing component.

# Backup and disaster recovery for Skytap applications

*Disaster recovery* is the process of restoring application functionality in the wake of a catastrophic loss.

In the cloud, we acknowledge up front that failures will happen. Instead of trying to prevent failures altogether, the goal is to minimize the effects of a single failing component. Testing is one way to minimize these effects. You should automate testing your applications where possible, but you need to be prepared for when they fail. When this happens, having backup and recovery strategies becomes important.

Your tolerance for reduced functionality during a disaster is a business decision that varies from one application to the next. It might be acceptable for some applications to be unavailable or to be partially available with reduced functionality or delayed processing for a period of time. For other applications, any reduced functionality is unacceptable. 

## Disaster recovery plan

Start by creating a recovery plan. The plan is considered complete after it has been fully tested. Include the people, processes, and applications needed to restore functionality within the service-level agreement (SLA) you've defined.

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
>* [Design Considerations for IBM Cloud](designconsiderationsibm.md)


**Security**
> * [Skytap Security Pillar](../security/README.md)

