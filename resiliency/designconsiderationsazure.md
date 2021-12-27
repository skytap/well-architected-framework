---
title: Reliability Design Considerations for Azure
author: George Stamos - Director, Solutions Architect - Business Development
---

***Skytap for Azure Support and Limits (IBM i)***


**Compute**

LPAR Sizing and OS Support
for Power

- S922 System p Frames
- IBM i P10 License Class
- Up to 4 vCPU / 512 GB RAM per LPAR
- CPW up to 42472 per LPAR
- Supports:
    - IBM i:  7.2 TR 9, 7.3 TR 5, 7.4

**Network**

- IPSec (SDN) / PNC Connectivity Between Skytap and On-Premises
- Range: 1 GB/s Limit
- Layer 2 networking in environment
- Range: Burst to 2.7 GB/s Limit
- 10 NICs Per Host
- 10 VPN Connections Per Account


**Storage**

- Limit of 32 Disks for POWER
- Each Disk Limited to 2TB 
- Storage performance scalability with VM/LPAR RAM capacity
- IOPS up to 30K per LPAR

***Skytap for Azure Support and Limits (AIX)***

**Compute**

LPAR Sizing and OS Support for Power

- S922 System p Frames
- IBM i P10 License Class
- Up to 4 vCPU / 512 GB RAM per LPAR
- Higher RAM capacity supported on as needed basis (~1.6TB)
- Supports:
    - AIX: 6.1 TL 9 SP 10
    - AIX: 7.1 TL 4 SP 4, 
    - AIX: 7.2 TL 1, SP 2

**Network**

- IPSec (SDN) / PNC Connectivity Between Skytap and On-Premises
- Range: 1 GB/s Limit
- Layer 2 networking in environment
- Range: Burst to 2.7 GB/s Limit
- 10 NICs Per Host
- 10 VPN Connections Per Account


**Storage**
- Limit of 32 Disks for POWER
- Each Disk Limited to 2TB 
- Storage performance scalability with VM/LPAR RAM capacity
- IOPS up to 30K per LPAR

### Next steps

**Main Overview**
> [Skytap Well-Architected Framework](../README.md)

**Operational Excellence**
>[Skytap Operational Excellence Pillar](../operations/README.md)
>* [Connectivity Overview](../operations/connectivity/README.md)
>* [Getting Started with Azure Networking](../operations/connectivity/skytaponazureconnectivity.md)

<!--- >* [Getting Started with IBM Cloud Networking](../operations/connectivity/skytaponibmconnectivity.md) --->

**Resiliency**
> [Skytap Resiliency Pillar](README.md)

>**Migration Solutions**
>* [Hot Migrations (Replication Sync)](solutions/HotMigrationOverview.md)
>* [Cold (Warm) Migrations (Backup and Restore)](solutions/ColdMigrationsOverview.md)

<!-- 
>**Design**
>* [Design Considerations for IBM Cloud](designconsiderationsibm.md)
-->

**Security**
> * [Skytap Security Pillar](../security/README.md)

