---
title: Reliability Design Considerations for Azure
author: George Stamos - Director, Solutions Architect * Business Development
permalink: /resiliency/design-considerations-azure/
---

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

* S922 System p Frames
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

### Next steps

**Main Overview**
> [Skytap Well-Architected Framework]({{ site.navigation.Home }})

**Operational Excellence**
> [Skytap Operational Excellence Pillar]({{ site.navigation.Operations }})
> * [Connectivity Overview]({{ site.navigation.Operations }}connectivity/)
> * [Getting Started with Azure Networking]({{ site.navigation.Operations }}connectivity/azure)
> * [Getting Started with IBM Cloud Networking]({{ site.navigation.Operations }}/connectivity/ibm)

**Resiliency**
> [Skytap Resiliency Pillar]({{ site.navigation.Resiliency }})
> * [Migration]({{ site.navigation.Resiliency }}migrations)
> * [Protection]({{ site.navigation.Resiliency }}backups)
> * [Disaster Recovery]({{ site.navigation.Resiliency }}disaster-recovery)
> * [High Availability]({{ site.navigation.Resiliency }}ibmi-disaster-recovery)
>
> **Design**
> * [Design Considerations for IBM Cloud]({{ site.navigation.Resiliency }}design-considerations-ibm)

**Security**
> [Skytap Security Pillar]({{ site.navigation.Security }})
