---
title: Reliability Design Considerations for IBM Cloud
author: George Stamos, Director, Solutions Architect - Business Development
---
***Skytap for Azure Support and Limits (IBM i)***


**Compute**

LPAR Sizing and OS Support
for Power

<li>S922 System p Frames
<li>IBM i P10 License Class
<li>Up to 4 vCPU / 512 GB RAM per LPAR
<li>CPW up to 42472 per LPAR
<li>Supports:
IBM i:  7.2 TR 9, 7.3 TR 5, 7.4


**Network**

<li>IPSec (SDN) / PNC Connectivity Between Skytap and On-Premises		<li>Range: 1 GB/s Limit
<li>Layer 2 networking in environment
 <li>Range: Burst to 2.7 GB/s Limit
<li>10 NICs Per Host
<li>10 VPN Connections Per Account


**Storage**
<li> Limit of 32 Disks for POWER
<li>Each Disk Limited to 2TB 
<li>Storage performance scalability with VM/LPAR RAM capacity
<li>IOPS up to 30K per LPAR


***Skytap for Azure Support and Limits (AIX)***


**Compute**

LPAR Sizing and OS Support
for Power

<li>S922 System p Frames
<li>IBM i P10 License Class
<li>Up to 4 vCPU / 512 GB RAM per LPAR
<li>Higher RAM capacity supported on as needed basis (~1.6TB)
<li>Supports:

AIX: 6.1 TL 9 SP 10

AIX: 7.1 TL 4 SP 4, 

AIX: 7.2 TL 1, SP 2



**Network**

<li>IPSec (SDN) / PNC Connectivity Between Skytap and On-Premises		<li>Range: 1 GB/s Limit
<li>Layer 2 networking in environment
 <li>Range: Burst to 2.7 GB/s Limit
<li>10 NICs Per Host
<li>10 VPN Connections Per Account


**Storage**
<li> Limit of 32 Disks for POWER
<li>Each Disk Limited to 2TB 
<li>Storage performance scalability with VM/LPAR RAM capacity
<li>IOPS up to 30K per LPAR

### Next steps

**Main Overview**
> [Skytap Well-Architected Framework](../README.md)

**Operational Excellence**
>[Skytap Operational Excellence Pillar](../operations/README.md)
>* [Connectivity Overview](../operations/connectivity/README.md)
>* [Getting Started with IBM Cloud Networking](../operations/connectivity/skytaponibmconnectivity.md)

**Resiliency**
> [Skytap Resiliency Pillar](README.md)

>**Migration Solutions**
>* [Hot Migrations (Replication Sync)](solutions/HotMigrationOverview.md)
>* [Cold (Warm) Migrations (Backup and Restore)](solutions/ColdMigrationsOverview.md)

>**Design**
>* [Design Considerations for Azure](designconsiderationsazure.md)

**Security**
> * [Skytap Security Pillar](../security/README.md)

