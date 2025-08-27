---
title: Principles of the reliability pillar
description: Describes the principles of the reliability pillar.
author: George Stamos - Director, Solutions Architect - Business Development
permalink: /resiliency/backups/
---


# Backup for {{site.Brand}} workloads

In the cloud, we acknowledge up front that failures will happen. Instead of trying to prevent failures altogether, the goal is to minimize the effects of a single failing component. As such we recommend that you be prepared for when they fail. When this happens, having backup and recovery strategies becomes important.

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

### Additional Solutions

- **[iSCSI VTL backups for IBM i running on {{site.SoA}}]({{ site.navigation.Resiliency }}solutions/VTL/IBMi-iSCSI-VTL-Backups)**
- **[DSI iSCSI VTL backups for IBM i running on {{site.SoA}}]({{ site.navigation.Resiliency }}solutions/VTL/DSI-iSCSI-VTL-Backups)**

### Next steps

**Main Overview**
> [{{site.Brand}} Well-Architected Framework]({{ site.navigation.Home }})

**Operational Excellence**
> [{{site.Brand}} Operational Excellence Pillar]({{ site.navigation.Operations }})

**Resiliency**
> [{{site.Brand}} Resiliency Pillar]({{ site.navigation.Resiliency }})
> * [Migration]({{ site.navigation.Resiliency }}migrations)
> * [Disaster Recovery]({{ site.navigation.Resiliency }}disaster-recovery)
> * [High Availability]({{ site.navigation.Resiliency }}high-availability)
>
> **Design**
> * [Design Considerations for Azure]({{ site.navigation.Resiliency }}design-considerations-azure)
> * [Design Considerations for IBM Cloud]({{ site.navigation.Resiliency }}design-considerations-ibm)

**Security**
> [{{site.Brand}} Security Pillar]({{ site.navigation.Security }})
