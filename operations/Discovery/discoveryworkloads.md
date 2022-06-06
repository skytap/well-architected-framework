---
title: Skytap Power Discovery - Workloads
author: George Stamos, Director, Solutions Architect- Business Development
permalink: /operations/Discovery/workloads
---
# Skytap Power Discovery - Workloads
#### The following section outlines IBM Power Workload discovery: <a name="toc"></a>

* [Goals](#goals)
* [Considerations and Questions](#qanda)
  * [Organizational](#qandaorg)
  * [Application](#qandaapp)
  * [Scenario](#qandascene)
* [Skytap on Azure Support and Limits](#designforazure)
* [Skytap on IBM Cloud Support and Limits](#designforibm)
* [Sample Outputs](#samples)

### Skytap Power Discovery for Workloads - Goals <a name="goals"></a>

The goal of discovery is to develop the proper framework for your
workloads running in Skytap. It can be further defined by these
supporting goals:

* Uncover exact on-premises workloads to be migrated at the LPAR/VM level
* Match existing data center workloads as closely as possible in Skytap
* Match or exceed existing data center performance
* Deeply understand usage patterns
*  Deliver a well-reasoned cost proposal and practical migration approach

### Skytap Power Discovery for Workloads - Considerations and Questions <a name="qanda"></a>

There are relevant considerations to be made and questions to ask your
organization during the discovery phase aligned to three key areas:

**Organizational<a name="qandaorg"></a>**

1.  Are your workloads internally managed or outsourced?

2.  Which compliance regulations are you required to adhere to?

3.  Are there unique considerations to your IBM Power environment?

4.  What approval is needed for networking changes, remote access,
    software agent install and maintenance windows?

**Application<a name="qandaapp"></a>**

1.  Which third party applications are in use?

2.  What technologies do you prefer to use for backup, disaster recovery
    (DR) and/or high availability (HA)?

3.  Can you describe the existing network set up between LPARs and WAN
    (ExpressRoute) set up?

4.  Are there specific licensing requirements to be aware of?

**Scenario<a name="qandascene"></a>**

1.  What is your Recovery Point Objective (RPO)/Recovery Time Objective
    (RPO) for the migration?

2.  Do you have development environments that can be powered down when
    not in use?

3.  What are the approximate number of active users in your IBM Power
    environment? Which time zones do your users operate in?

### Skytap Power Discovery for Workloads - Skytap for Azure Support and Limits (AIX and IBM i)<a name="designforazure"></a>

> * [Design Considerations for Azure](../../resiliency/design-considerations-azure)

<!--- ### Skytap Power Discovery for Workloads - Skytap for IBM Cloud Support and Limits (AIX and IBM i)<a name="designforibm"></a>

> * [Design Considerations for IBM Cloud](../../resiliency/design-considerations-ibm) --->

### Skytap Power Discovery for Workloads - Sample Outputs<a name="samples"></a>

**AIX**

After performing the initial inventory of LPARs, execute the following
commands and catalog into Bill of Materials (BOM) for the migration (can
also run a SNAP command):

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/Discovery/discoveryworkloadsmedia/image1.png">

**IBM i (SYSSTS, PTF, SFW Output)**

After performing the initial inventory of LPARs, you can execute the
following commands and catalog into the BOM for the migration.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/Discovery/discoveryworkloadsmedia/image2.png">

**Example BOM for Migration -- AIX, IBM i, Spot Windows/Linux**

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/Discovery/discoveryworkloadsmedia/image3.png">

###### *[Back to the Top](#toc)*
## Next steps

**Main Overview**
> [Skytap Well-Architected Framework](../../)

**Operational Excellence**
>[Skytap Operational Excellence Pillar](../)
>* [Power Discovery](../Discovery/) > [Power Discovery & Design - Ecosystems](./ecosystems)
>* [Connectivity](../connectivity/)
>* [Ecosystems](../ecosystems/)

**Resiliency**
>[Skytap Resiliency Pillar](../../resiliency/)
>* [Migration](../../resiliency/migrations)
>* [Protection](../../resiliency/backups)
>* [Disaster Recovery](../../resiliency/disaster-recovery)
>* [High Availability](../../resiliency/ibmi-disaster-recovery)
>
>**Migration Solutions**
>* [Hot Migrations (Replication Sync)](../../resiliency/solutions/hot-migrations)
>* [Cold (Warm) Migrations (Backup and Restore)](../../resiliency/solutions/cold-migrations)
>
>**Design**
>* [Design Considerations for Azure](../../resiliency/design-considerations-azure)
>* [Design Considerations for IBM Cloud](../../resiliency/design-considerations-ibm)

**Security**
> [Skytap Security Pillar](../../security/)
