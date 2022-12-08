---
title: SAP NetWeaver Ecosystems in Skytap on Azure
author: George Stamos - Director, Solutions Architect - Business Development
---

# SAP on Azure Deployments 

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/ecosystems/azurenative/solutions/SAP/media/image12.jpeg"><img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/ecosystems/azurenative/solutions/SAP/media/image3.png">
<br>
<center><img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/ecosystems/azurenative/solutions/SAP/media/image11.png" Width="800">
<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/ecosystems/azurenative/solutions/SAP/media/image45.jpeg" Width="800"></center>

This reference architecture illustrates a set of proven practices for running SAP NetWeaver on AIX in a Skytap on Azure environment. The database in this example is Oracle 19C, but Database Tier may be AnyDB (supported by underlying AIX operating system and SAP NetWeaver).

## Business Drivers - the case for change
**SAP NetWeaver based workloads on IBM Power on Skytap on Azure**

1.  Business Continuity: NetWeaver workloads requiring Cloud Backup/DR
2.  Regulation and Compliance: NetWeaver workloads in read-only state in Azure
3.  IBM Power Data Center exit : NetWeaver workloads to Azure
4.  Modernization of Business Processes:

    a.  Move NetWeaver workloads to Skytap on Azure to enable them to inter-operate
 with SAP HANA based workloads or other Business Applications running on Azure

    b.  Move NetWeaver workloads to Skytap on Azure to enable them to be more easily transition to SAP HANA

## Sample SAP Architecture for Skytap on Azure

The first diagram shows SAP NetWeaver migration options and running state considerations from a high level with detailed descriptions on options for migration.

<center><img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/ecosystems/azurenative/solutions/SAP/media/image26.jpeg" Width="800"></center>

The second diagram provides specific recommendations on the overall architectural design and components used for the migration, including Azure native components working in conjunction with Skytap on Azure dedicated IBM POWER systems. 

<center><img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/ecosystems/azurenative/solutions/SAP/media/referencearchitecture02.png" Width="800"></center>

The numbers in the diagram correspond to the following data flow.
1.	A user on-premises uses a web browser to connect to Azure through Azure ExpressRoute, which creates a private connection. This web-based app provides a modern interface for the services that run on the AIX LPARs in Skytap on Azure.
2.	Azure Data Box Gateway is deployed on-premises next to the datacenter's existing AIX infrastructure, which includes a temporary AIX Network Installation Management (NIM) server for the purposes of full backup and full restore. Data Box Gateway loads the data and completes the system restoration on Azure. AIX backups run using the operating system's native mksyb and savevg commands.
3.	Files that are backed up to Data Box Gateway are migrated to the organization's Azure Blob Storage account through Azure Private Link, an endpoint for privately accessing Azure services.
4.	In the Skytap on Azure environment, the NIM server running Unix is used to restore the base AIX operating system to the LPARs in Skytap on Azure.
5.	The AIX LPAR is rebooted. Any data volume groups are restored through the Data Box Gateway via the Network File System (NFS) protocol. This process is repeated for each LPAR to be restored.

The third diagram is an illustration of utilizating Commvault Backup 
and Recovery for both the migration and for the on-going orchestration of backups in Azure blob storage. 

<center><img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/ecosystems/azurenative/solutions/SAP/media/referencearchitecture03.png" Width="800"></center>

The numbers in the diagram correspond to the following data flow.
1.	A user on-premises uses a web browser to connect to Azure through Azure ExpressRoute, which creates a private connection. This web-based app provides a modern interface for the services that run on the AIX LPARs in Skytap on Azure.
2.	Azure Data Box Gateway is deployed on-premises next to the datacenter's existing AIX infrastructure, which includes a Commvault Media Agent. Full and incremental backups of both the SAP NetWeaver Central Services and Application Servers, as well as the Oracle DB are applied to the Commvault Media Agent and then moved to the Azure Data Box Gateway.
3.	In addition to providing the automation for the initial full backup for the migration, A Commvault CommServer in Azure will also orchestrate the backup policy that applies to the running SAP NetWeaver and Oracle DB in the Skytap on Azure.
4.	Data from the initial full backup, as well as any incremental backups are stored in an organization’s Azure Blob Storage account.
5.	Reading from the Commvault Media Agent in the Skytap on Azure environment, both full AIX LPARs, as well as incremental file and DB backups may be restored to the AIX LPARs running in Skytap on Azure.


Lastly, the final 
diagram brings many of these components together in a diagram of a highly available solution with replication between 
Azure geographic regions for SAP NetWeaver and Oracle on AIX.

<center><img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/ecosystems/azurenative/solutions/SAP/media/referencearchitecture04.png" Width="800"></center>

The numbers in the diagram correspond to the following data flow.
1.	The three layers represent application, database and optional protection that comprise the entirety of the SAP application stack. Each of these components within Skytap on Azure may be asynchronously replicated to other Azure regions for Highly Available failover and Disaster Recovery scenarios. In the example above, Primary is represented by West Europe (Amsterdam) and secondary is North Europe (Dublin).
2.	Using an express route connection, Precisely MIMIX for AIX may be used for seamless TCP/IP replication between regions. In some cases, particularly when consistently large rates of data change occur, Oracle Data Guard may be used for Oracle standby replication between regions over a TCP/IP connection via ExpressRoute. In such a case, Precisely MIMIX for AIX would still handle volume group replication between regions for other application components.
3.	In addition to the highly available solution utilizing replication, a protection solution may also be utilized for rollback operations at a transactional, file or bare metal layer. In this example, backups are orchestrated utilizing Commvault writing directly through an access node to geo-redundant (GRS) blob storage in Azure.
4.	Lastly, a mirrored architecture exists within the secondary region, North Europe. This allows for seamless failover in the event of a disaster and an uninterrupted cadence of protection orchestration.

## Customer Example -- (Large European Oil and Gas)

SAP On-Premises Solution (AIX) that Migrated Nearly Unchanged to
Azure

<center><img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/ecosystems/azurenative/solutions/SAP/media/image44.jpeg"></center>

***Drivers and Requirements:***

-   *Data Center Exit*

-   *Maintain Self-Service Control over ALL infrastructure*

-   *Ability to Upgrade and Test in Azure*

-   *Seamless Hybrid Connectivity between On-Premises and Azure*

-   *Seamless Hybrid Connectivity between x86 and AIX workloads in Azure (SAP and non-SAP interconnectivity)*

-   *Archive and Restore Capability for Compliance*


### SAP Cloud and Infrastructure Operations Certification - Skytap on Azure

SAP granted Skytap Cloud and Infrastructure Operations Certification based on successful audit. This certification is for SAP NetWeaver workloads. In addition, the certification process starts locally, at which point global consideration can be based on regional audits moving forward.

- **Type of Certification:** Cloud and Infrastructure Operations Local (UK)
- **Platform Audited:** Skytap on Azure
- **Date Granted:** September 22, 2021
- **Microsoft DC Audited:** AMS23/West Europe
- **Eligible Products:** SAP Business Suite (SAP NetWeaver, SAP BusinessObjects, SAP ERP)

## SAP on Azure - What are options for customers?

#### SAP on Azure supported systems :

##### SAP Business Suite on IBM POWER on Skytap on Azure - detailed product specifics

-   ***Which versions of R/3 and ECC*** are possible - \[Assumes Skytap
    Supported Underlying Operating Systems\*\]

-   Includes: R/3 All Versions, ECC 5.0, ECC 6.0 up to v7, NetWeaver
    7.0+, AS ABAP 7.51, 7.52

-   ***R/3 and ECC Database Layer Support*** - \[Assumes Skytap Supported Underlying Operating Systems\*\]

-   *Oracle*: All standalone versions supported by Operating System in use*

-   Does not include Oracle RAC at this time

-   Does include ASM

-   *DB2*: All standalone versions supported by Operating System in use*

-   *Informix*: All standalone versions supported by Operating System in use

-  *SAP MaxDB*: All standalone versions supported by Operating System in use\* \[Hot standby mode not tested in Skytap.\]

-   *SQLServer: Legacy versions of Windows are supported in Skytap and not Azure Native*

**\* = NetWeaver Only**

***Minimum Supported OS versions for Skytap in Azure:***

-   AIX: AIX 6.1 TL9 SP10, AIX 7.1 TL4 SP4, AIX 7.2 TL1 SP2

-   IBM i: 7.2 TR9, 7.3 TR8, 7.4 TR2

-   RHEL on POWER, SUSE Enterprise Server on POWER, Ubuntu Server on
    POWER


> *In the case of any tier of the R/3 or ECC architecture, it will need
> to run on an underlying Skytap supported operating system, and have
> requirements that fall within the Skytap supported maximum limits.*

-   ***Current technical requirements/limitations for Netweaver based
    workloads***

-   AIX LPAR Sizing: [\<]{.ul}16 vCPU and [\<]{.ul}512 GB RAM

-   IBM i LPAR Sizing: [\<]{.ul}4 vCPU and [\<]{.ul}512 GB RAM

-   Linux LPAR Sizing: [\<]{.ul}16 vCPU and [\<]{.ul}512 GB RAM

-   Storage per LPAR: [\<]{.ul}64TB per LPAR

## Next steps

**Main Overview**
> [Skytap Well-Architected Framework](../../../../../README.md)

**Operational Excellence**
> [Skytap Operational Excellence Pillar](../../../../README.md)
> * [Power Discovery](../../../../Discovery/README.md)
> * [Connectivity](../../../../connectivity/README.md)
> * [Ecosystems](../../../../ecosystems/README.md)

**Resiliency**
> [Skytap Resiliency Pillar](../../../../../resiliency/README.md)

**Security**
> [Skytap Security Pillar](../../../../../security/README.md)
