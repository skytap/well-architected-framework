---
title: Skytap Power Discovery - Workloads
author: George Stamos, Director, Solutions Architect- Business Development
---
**Skytap on Azure -- Power Workload Discovery**

The following section outlines IBM Power Workload discovery:

-   Goals

-   Considerations and Questions

-   Skytap on Azure Support and Limits

-   Sample Outputs

**IBM Power Workload Discovery Goals**

The goal of discovery is to develop the proper framework for your
workloads running in Skytap on Azure. It can be further defined by these
supporting goals:

-   Uncover exact on-premises workloads to be migrated at the LPAR/VM
    level

-   Match existing data center workloads as closely as possible in
    Skytap

-   Match or exceed existing data center performance

-   Deeply understand usage patterns

-   Deliver a well-reasoned cost proposal and practical migration
    approach

**IBM Power Workload Discovery Considerations and Questions**

There are relevant considerations to be made and questions to ask your
organization during the discovery phase aligned to three key areas:

**Organizational**

1.  Are your workloads internally managed or outsourced?

2.  Which compliance regulations are you required to adhere to?

3.  Are there unique considerations to your IBM Power environment?

4.  What approval is needed for networking changes, remote access,
    software agent install and maintenance windows?

**Application**

1.  Which third party applications are in use?

2.  What technologies do you prefer to use for backup, disaster recovery
    (DR) and/or high availability (HA)?

3.  Can you describe the existing network set up between LPARs and WAN
    (ExpressRoute) set up?

4.  Are there specific licensing requirements to be aware of?

**Scenario**

1.  What is your Recovery Point Objective (RPO)/Recovery Time Objective
    (RPO) for the migration?

2.  Do you have development environments that can be powered down when
    not in use?

3.  What are the approximate number of active users in your IBM Power
    environment? Which time zones do your users operate in?

**IBM Power Workload Discovery Skytap for Azure Support and Limits (AIX
and IBM i)**

Compute: LPAR Sizing and OS Support

IBM Power (IBM i)

-   S922 System p Frames

-   IBM I P10 License Class

-   Up to 4 vCPU/512 GB RAM per LPAR

-   CPW up to 42472 per LPAR

-   Supports IBM i: 7.2 TR 9, 7.3 TR 5, 7.4

IBM Power (AIX)

-   S922 System p Frames

-   Up to 16 vCPU/512 GB RAM per LPAR

-   Supports AIX: 6.1 TL 9 SP10, 7.1 TL 4 SP 4, 7.2 TL 1, SP 2

Network

-   IPSec (SDN)/PNC Connectivity between Skytap and On-Premises (Range:
    1 GB/s Limit)

-   Layer 2 networking in environment (Range: Burst to 2.7 GB/s Limit)

-   10 NICs per host

-   10 VPN Connections per account

Storage

-   32 Disks for Power limit

-   Each disk limited to 2TB

-   Storage performance scalability with VM/LPAR RAM capacity

-   Power -- up to 32 drives at 2TB each -- 64 TB max, IOPS up to 30K
    per LPAR

**IBM Power Workload Discovery Sample Outputs**

**AIX**

After performing the initial inventory of LPARs, execute the following
commands and catalog into Bill of Materials (BOM) for the migration (can
also run a SNAP command):

<img src=".\discoveryworkloadsmedia\image1.png">

**IBM i (SYSSTS, PTF, SFW Output)**

After performing the initial inventory of LPARs, you can execute the
following commands and catalog into the BOM for the migration.

<img src=".\discoveryworkloadsmedia\image2.png">

**Example BOM for Migration -- AIX, IBM I, Spot Windows/Linux**

<img src=".\discoveryworkloadsmedia\image3.png">

## Next steps

**Main Overview**
> [Skytap Well-Architected Framework](../../README.md)

**Operational Excellence**
>[Skytap Operational Excellence Pillar](README.md)
>* [Power Discovery](../Discovery/README.md)
       > [Power Discovery & Design - Ecosystems](discoveryecosystems.md)
>* [Connectivity](../Connectivity/README.md)

**Resiliency**
> [Skytap Resiliency Pillar](../../resiliency/README.md)

**Security**
> [Skytap Security Pillar](../../security/README.md)
