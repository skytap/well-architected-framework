---
title: Principles of the reliability pillar
description: Describes the principles of the reliability pillar.
author: George Stamos - Director, Solutions Architect - Business Development
permalink: /resiliency/disaster-recovery/
---

# Backup and disaster recovery for Skytap workloads

*Disaster recovery* is the process of restoring workload functionality in the wake of a catastrophic loss.

In the cloud, we acknowledge up front that failures will happen. Instead of trying to prevent failures altogether, the goal is to minimize the effects of a single failing component. Testing is one way to minimize these effects. You should automate testing your workloads where possible, but you need to be prepared for when they fail. When this happens, having backup and recovery strategies becomes important.

Your tolerance for reduced functionality during a disaster is a business decision that varies from one workload to the next. It might be acceptable for some workloads to be unavailable or to be partially available with reduced functionality or delayed processing for a period of time. For other workloads, any reduced functionality is unacceptable. 

## Disaster recovery plan

Start by creating a recovery plan. The plan is considered complete after it has been fully tested. Include the people, processes, and workloads needed to restore functionality within the service-level agreement (SLA) you've defined.

Consider the following suggestions when creating and testing your disaster recovery plan:

- Include the process for contacting support and escalating issues. This information will help to avoid prolonged downtime as you work out the recovery process for the first time.
- Evaluate the business impact of workload failures.
- Choose a cross-region recovery architecture for mission-critical workloads.
- Identify a specific owner of the disaster recovery plan, including automation and testing.
- Document the process, especially any manual steps.
- Automate the process as much as possible.
- Establish a backup strategy for all reference and transactional data, and test backup restoration regularly.
- Set up alerts for the stack of the Skytap services consumed by your workload.
- Train operations staff to execute the plan.
- Perform regular disaster simulations to validate and improve the plan.

## Disaster recovery strategy

Many alternative strategies are available for implementing distributed compute across regions. These must be tailored to the specific business requirements and circumstances of the workload. At a high level, the approaches can be divided into the following categories:

- **Redeploy on disaster**: In this approach, the workload is redeployed from scratch at the time of disaster. This is appropriate for non-critical workloads that don’t require a guaranteed recovery time.

- **Warm Spare (Active/Passive)**: A secondary hosted service is created in an alternate region, and roles are deployed to guarantee minimal capacity; however, the roles don’t receive production traffic. This approach is useful for workloads that have not been designed to distribute traffic across regions.

- **Hot Spare (Active/Active)**: The workload is designed to receive production load in multiple regions. The cloud services in each region might be configured for higher capacity than required for disaster recovery purposes. Alternatively, the cloud services might scale-out as necessary at the time of a disaster and fail-over. This approach requires substantial investment in workload design, but it has significant benefits. These include low and guaranteed recovery time, continuous testing of all recovery locations, and efficient usage of capacity.

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
> * [Migration]({{ site.navigation.Resiliency }}migrations)
> * [Protection]({{ site.navigation.Resiliency }}backups)
> * [High Availability]({{ site.navigation.Resiliency }}ibmi-disaster-recovery)

**Security**
> [Skytap Security Pillar]({{ site.navigation.Security }})
