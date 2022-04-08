![](I:\Repos\Skytap\WAF\operations\ecosystems\SAP\/media/image1.png)

![](I:\Repos\Skytap\WAF\operations\ecosystems\SAP\/media/image3.png)

![](I:\Repos\Skytap\WAF\operations\ecosystems\SAP\/media/image11.png)

# SAP on Azure Deployments 

![](I:\Repos\Skytap\WAF\operations\ecosystems\SAP\/media/image12.jpeg)

> ![](I:\Repos\Skytap\WAF\operations\ecosystems\SAP\/media/image3.png)
>
> Business Drivers- the case for change - **SAP NetWeaver based
> workloads onIBM Power on Skytap on Azure**

1.  **Business Continuity: NetWeaver workloads requiring Cloud
    > Backup/DR**

2.  **Regulation and Compliance: NetWeaver workloads in Read-Only State
    > in Azure**

3.  ![](I:\Repos\Skytap\WAF\operations\ecosystems\SAP\/media/image25.png)**IBM
    > Power Data Centre exit : NetWeaver workloads to Azure**

4.  **Modernization of Business Processes**

    a.  Move NetWeaver workloads to SOA to enable them to interoperate
        with SAP

HANA based workloads or other Business Applications running on Azure

b.  Move NetWeaver workloads to SOA to enable them to be more easily
    transition to SAP HANA

![](I:\Repos\Skytap\WAF\operations\ecosystems\SAP\/media/image3.png) 5

## Sample SAP Architecture for Skytap on Azure

> ![](I:\Repos\Skytap\WAF\operations\ecosystems\SAP\/media/image26.jpeg)

![](I:\Repos\Skytap\WAF\operations\ecosystems\SAP\/media/image27.png)

## Customer Example -- (Large European Oil and Gas)

> ![](I:\Repos\Skytap\WAF\operations\ecosystems\SAP\/media/image41.jpeg)

.... SAP On-Premises Solution (AIX) that Migrated Nearly Unchanged to
Azure....

-   ![](I:\Repos\Skytap\WAF\operations\ecosystems\SAP\/media/image44.jpeg)***Drivers
    and Requirements:***

-   *Data Center Exit*

-   *Maintain Self-Service Control over ALL infrastructure*

-   *Ability to Upgrade and Test in*

> *Azure*

-   *Seamless Hybrid Connectivity between On-Premises and*

> *Azure*

-   *Seamless Hybrid Connectivity between x86 and AIX workloads in Azure
    (SAP and non-SAP interconnectivity)*

-   *Archive and Restore Capability for Compliance*

8

## Customer Example Overview (Large European Oil and Gas)

> [Platform]{.ul}
>
> [Requirements]{.ul}

### CUSTOMER / 

> ~MSP~Self-Service
>
> Capability
>
> (Migrate,
>
> Start/Stop,
>
> Networking,
>
> Modify
>
> CPU/Ram and
>
> Storage, etc.)

### CUSTOMER / 

> MSPAbility to
>
> Connect SAP and non-SAP Systems both
>
> On-Prem and
>
> CUSTOMER / In Azure
>
> MSP
>
> CUSTOMER /
>
> MSP

+-----------------------------------------------------------------------+
| Application Unchanged (SAP Basis Unified Management)                  |
|                                                                       |
| \[SAP: APE / APH ECC6, MPP BBPCRM 4.0,                                |
|                                                                       |
| MHP CRM 5.0, SAP R/3 Enterprise 4.7, GSP SAP R/3 Enterprise 4.6C\]    |
|                                                                       |
| \[Oracle: 10.2.0.4 and 10.2.0.2\]                                     |
+=======================================================================+
+-----------------------------------------------------------------------+

+-----------------------------------------------------------------------+
| > Operating System -- Patching, Versions, etc.                        |
|                                                                       |
| AIX 6.1                                                               |
+=======================================================================+
+-----------------------------------------------------------------------+

  -----------------------------------------------------------------------
  Infrastructure and Hypervisor
  -----------------------------------------------------------------------

  -----------------------------------------------------------------------

  -----------------------------------------------------------------------
  Infrastructure and Hypervisor
  -----------------------------------------------------------------------

  -----------------------------------------------------------------------

+-----------------------------------------------------------------------+
| > Data Center                                                         |
+=======================================================================+
+-----------------------------------------------------------------------+

+-----------------------------------------------------------------------+
| > Data Center                                                         |
+=======================================================================+
+-----------------------------------------------------------------------+

### **BEFOREAFTER**

9

![](I:\Repos\Skytap\WAF\operations\ecosystems\SAP\/media/image1.png)

![](I:\Repos\Skytap\WAF\operations\ecosystems\SAP\/media/image45.jpeg)

![](I:\Repos\Skytap\WAF\operations\ecosystems\SAP\/media/image46.jpeg)

> SAP Cloud and Infrastructure Operations Certification - SOA
>
> SAP granted Skytap Cloud and Infrastructure Operations Certification
> Capabilities based on successful audit. This certification is for SAP
> NetWeaver workloads. In addition, the certification process starts
> locally, at which point global consideration can be based on regional
> audits moving forward.

-   Type of Certification: Cloud and Infrastructure Operations Local
    (UK)

-   Platform Audited: Skytap on Azure

-   ![](I:\Repos\Skytap\WAF\operations\ecosystems\SAP\/media/image25.png)Date
    Granted: September 22, 2021 ● Microsoft DC Audited: AMS23/West
    Europe ● Eligible Products :

> ○ SAP Business Suite (SAP NetWeaver, SAP BusinessObjects, SAP ERP )
>
> ○ **[See Product Support Slide]{.ul}**

![](I:\Repos\Skytap\WAF\operations\ecosystems\SAP\/media/image3.png) 13

> ![](I:\Repos\Skytap\WAF\operations\ecosystems\SAP\/media/image49.png)SAP
> on Azure
>
> What are options for customers?

# ![](I:\Repos\Skytap\WAF\operations\ecosystems\SAP\/media/image50.png)SAP on Azure supported systems 

> SAP Business Suite on IBM POWER on SOA - detailed product specifics

-   ***Which versions of R/3 and ECC*** are possible - \[Assumes Skytap
    Supported Underlying Operating Systems\*\]

-   Includes: R/3 All Versions, ECC 5.0, ECC 6.0 up to v7, NetWeaver
    7.0+, AS ABAP 7.51, 7.52

-   ***R/3 and ECC Database Layer Support*** - \[Assumes Skytap
    Supported Underlying Operating Systems\*\]

-   *Oracle*: All standalone versions supported by Operating System in
    use.\*

-   Does not include Oracle RAC at this time.

-   Does include ASM

-   *DB2*: All standalone versions supported by Operating System in
    use.\*

-   *Informix*: All standalone versions supported by Operating System in
    use.\* \[NetWeaver Only\]

-   ![](I:\Repos\Skytap\WAF\operations\ecosystems\SAP\/media/image25.png)*SAP
    MaxDB*: All standalone versions supported by Operating System in
    use.\* \[Hot standby mode not tested in Skytap.\]

-   *SQLServer: Legacy versions of Windows are supported in Skytap and
    not Azure Native.*

-   ***\* Minimum Supported OS versions for Skytap in Azure:***

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

16
