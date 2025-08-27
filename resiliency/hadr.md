---
title: Principles of the reliability pillar
description: Describes the principles of the reliability pillar.
author: Lagath Gunasinghe - Cloud Solutions Architect
permalink: /resiliency/high-availability/
---

# IBM I -- High Availability and Disaster Recovery

SKYTAP can support any IBM I HA and/or DR solution customers used to
have on-premises apart from the storage level replication solutions.

As SKYTAP provides the infrastructure for the IBM Power I platform, all
we need is the network connectivity from on-Premises to SKYTAP if need
to have HA/DR on SKYTAP or network connectivity in between two or more
SKYTAP/Azure regions to replicate from one region to another.

For High Availability, SKYTAP supports any logical replication software.
MIMIX, Quick EDD and NOMAX to name a few.

For Disaster Recovery, SKYTAP supports any variations of the above
software where they provide high RPO (Recovery Point Objective) and RTO
(Recovery Time Objective) with reduced license fee.

{{site.Brand}} also support Commvault solutions where they offer DR


## {{site.SoA}} IBM Power I HA/DR Considerations

### Defining RPO (Recovery Point Objective)

RPO, or** Recovery Point Objective**, is the threshold of how much data you can afford to lose since the last backup.

### How to calculate your RPO

Defining your company's RPO typically begins with examining how frequently backup takes place. Since backup can be intrusive to systems it is not typically performed more frequently than every several hours. This means that your backup RPO is probably measured in hours of data loss.

We'll illustrate with an example of how to use RPO. Let's say that you have a system outage at 1 PM. You know that the system performs a backup automatically at 12 PM. As the last time your information was saved in a usable format, the RPO should be 12 PM.

### Defining RTO (Recovery Time Objective)

RTO, or** Recovery Time Objective**, is the threshold for how quickly
you need to have an application's information restored.

### How to calculate your RTO

Here's an example of how you use RTO. You have an application that collects sensor data from mission-critical hardware. If you don't have visibility into how that hardware is performing, your company could suffer huge losses. The RTO for bringing that system back online is very short. However, if you have a less mission-critical application (let's say an app that you don't use very frequently that doesn't have a huge bearing on the business), the RTO could be considerably longer.

### Disaster recovery planning with RPO and RTO

Using these two primary measures will help you estimate your cost of downtime to better define your budget for an IT system continuity plan. Reviewing your RPO and RTO can also help you determine which technology will best meet your needs.

Finding the right balance of features and price to meet your RPO and RTO is one of the most critical things you can do to protect your business. For IT system continuity, there are three solution categories: backup, disaster recovery, and high availability.

-   **Backup** means keeping your data safe; in this situation, RPO is more critical than RTO.

-   **Disaster recovery** is the ability to recover data in case the production system is damaged, destroyed or becomes unavailable for an indeterminable period of time. A comprehensive disaster recovery solution that can restore data quickly and completely is required to meet low RPO and RTO thresholds.

-   **High availability** means avoiding down time and keeping your critical applications and data online -- a high availability solution is required for high RPO and RTO.

### Sample HA design on {{site.SoA}}

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/hadrmedia/media/image1.png" width="700">

### Additional Solutions

- **[IBM i PowerHA -- Geo Mirror -- {{site.Brand}} Configuration]({{ site.navigation.Resiliency }}solutions/PowerHAGeoMirror/)**

### Next steps

**Main Overview**
> [{{site.Brand}} Well-Architected Framework]({{ site.navigation.Home }})

**Operational Excellence**
> [{{site.Brand}} Operational Excellence Pillar]({{ site.navigation.Operations }})

**Resiliency**
> [{{site.Brand}} Resiliency Pillar]({{ site.navigation.Resiliency }})
> * [Migration]({{ site.navigation.Resiliency }}migrations)
> * [Protection]({{ site.navigation.Resiliency }}backups)
> * [Disaster Recovery]({{ site.navigation.Resiliency }}disaster-recovery)
>
> **Design**
> * [Design Considerations for Azure]({{ site.navigation.Resiliency }}design-considerations-azure)
> * [Design Considerations for IBM Cloud]({{ site.navigation.Resiliency }}design-considerations-ibm)

**Security**
> [{{site.Brand}} Security Pillar]({{ site.navigation.Security }})
