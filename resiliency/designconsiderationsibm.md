---
title: Reliability Design Considerations for IBM Cloud
author: George Stamos, Director, Solutions Architect - Business Development
permalink: /resiliency/design-considerations-ibm
---
***Skytap for IBM Cloud Support and Limits (IBM i)***


**Compute**

LPAR Sizing and OS Support
for Power

* S922 System p Frames
* IBM i P10 License Class
* Up to 4 vCPU / 512 GB RAM per LPAR
* CPW up to 42472 per LPAR
* Supports:
IBM i:  7.2 TR 9, 7.3 TR 5, 7.4


**Network**

* IPSec (SDN) / PNC Connectivity Between Skytap and On-Premises		
  *  Range: 750 Mbps Limit
* Layer 2 networking in environment
  * Range: Burst to 2.7 Gbps Limit
* 10 NICs Per Host
* 10 VPN Connections Per Account


**Storage**
*  Limit of 32 Disks for POWER
* Each Disk Limited to 2TB 
* Storage performance scalability with VM/LPAR RAM capacity
* IOPS up to 30K per LPAR


***Skytap for IBM Cloud Support and Limits (AIX)***


**Compute**

LPAR Sizing and OS Support
for Power

* S922 System p Frames
* IBM i P10 License Class
* Up to 4 vCPU / 512 GB RAM per LPAR
* Higher RAM capacity supported on as needed basis (~1.6TB)
* Supports:

  * AIX: 6.1 TL 9 SP 10
  * AIX: 7.1 TL 4 SP 4
  * AIX: 7.2 TL 1, SP 2



**Network**

* IPSec (SDN) / PNC Connectivity Between Skytap and On-Premises		
  *  Range: 750 Mbps Limit
* Layer 2 networking in environment
  * Range: Burst to 2.7 Gbps Limit
* 10 NICs Per Host
* 10 VPN Connections Per Account


**Storage**
* Limit of 32 Disks for POWER
* Each Disk Limited to 2TB 
* Storage performance scalability with VM/LPAR RAM capacity
* IOPS up to 30K per LPAR

### Next steps

**Main Overview**
> [Skytap Well-Architected Framework](../)

**Operational Excellence**
>[Skytap Operational Excellence Pillar](../operations/)
>* [Connectivity Overview](../operations/connectivity/)
>* [Getting Started with Azure Networking](../operations/connectivity/azure)
>* [Getting Started with IBM Cloud Networking](../operations/connectivity/ibm)

**Resiliency**
> [Skytap Resiliency Pillar](./)
>* [Migration](./migrations)
>* [Protection](./backups)
>* [Disaster Recovery](./disaster-recovery)
>* [High Availability](./ibmi-disaster-recovery)
>
>**Design**
>* [Design Considerations for Azure](./design-considerations-azure)

**Security**
> * [Skytap Security Pillar](../security/)

