---
title: SAP NetWeaver Ecosystems in Skytap on Azure
author: George Stamos - Director, Solutions Architect - Business Development
---

# SAP on Azure Deployments 

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/ecosystems/SAP/media/image12.jpeg"><img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/ecosystems/SAP/media/image3.png">
<br>
<center><img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/ecosystems/SAP/media/image11.png" Width="800">
<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/ecosystems/SAP/media/image45.jpeg" Width="800"></center>

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

<center><img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/ecosystems/SAP/media/image26.jpeg" Width="800"></center>

## Customer Example -- (Large European Oil and Gas)

SAP On-Premises Solution (AIX) that Migrated Nearly Unchanged to
Azure

<center><img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/ecosystems/SAP/media/image44.jpeg"></center>

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

-   Linux LPAR Sizing: [\<]{.ul}16 vCPU and [\<]{.ul}512 GB RAM •
    Storage per LPAR: [\<]{.ul}64TB per LPAR

#### Next steps

**Main Overview**
> [Skytap Well-Architected Framework](../../README.md)

**Operational Excellence**
>[Skytap Operational Excellence Pillar](../README.md)
>* [Power Discovery](../Discovery/README.md)
>* [Connectivity](../connectivity/README.md)
>* [Ecosystems](README.md)

**Resiliency**
> [Skytap Resiliency Pillar](../../resiliency/README.md)

>**Design**
>* [Design Considerations for Azure](../../resiliency/designconsiderationsazure.md)
>* [Design Considerations for IBM Cloud](../../resiliency/designconsiderationsibm.md)

**Security**
> [Skytap Security Pillar](../../security/README.md)
