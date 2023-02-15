---
Title: IBM i PowerHA -- Geo Mirror -- Skytap Configuration

Description: Skytap HA Solution - Skytap on Azure using IBM i PowerHA - Geo Mirror.
Authors: Mike Neil - Vice President, Technical Field Operations
---
# IBM i PowerHA -- Geo Mirror -- Skytap Configuration<a name="objective"></a>

This document explains how to configure a basic two-node geographic
mirroring environment within Skytap on Azure across different regions
with Network connectivity in place, leveraging local storage to the
partition. We will use an IBM i PowerHA two-node cluster (Primary node
configured in Region 1 and Backup Node in Region 2) with Geo Mirroring.
It is important to note that the user can use this document to extend
their on-premises HA/DR environment, and/or use this as a pattern for
having both Production and HA/DR entirely in the cloud.

This guide is provided “as-is”. Information and views expressed in this document, including URL and other Internet website references, may change without notice and usage of the included material assumes this risk.

This document does not provide you with any legal rights to any intellectual property in any product. You may copy and use this document for your internal, reference purposes.

## Table of Contents<a name="toc"></a>

* [Objective](#objective)
* [Overview](#overview)
* [IBM i PowerHA - Geographic Mirroring Pre-Flight Requirements](#preflight)
* [Implement Geographic Mirroring](#implement)
  * [Create cluster and start cluster nodes](#createandstartnodes)
  * [Add nodes to the device domain](#addnodes)
  * [Create independent ASP](#createindependentasp)
  * [Create iASP Device Description on Backup Node](#creatiASPBackup)
  * [Create the Device CRG (Cluster Resource Group)](#createcrg)
* [Configure Geographic Mirroring](#configuregeomirror)
  * [Start the Device CRG (Cluster Resource Group)](#startcrg)
  * [Vary on the IASP](#varyoniasp)
  * [Start the ASP session](#startaspsession)
* [Next Steps](#nextsteps)

##  Overview <a name="overview"></a>
Skytap on Azure is the ultimate solution for IT professionals who use IBM Power hardware. It offers a specialized cloud environment specifically designed for IBM Power and x86 workloads, seamlessly bridging the gap between legacy systems and cutting-edge cloud innovation. With the increasing demand for hybrid cloud providers, IT professionals can now easily migrate their existing applications and create new ones, all while extending their strategy to include traditional IBM Power Systems workloads, including those running on the trusted IBM i Operating System.

IBM i customers rely on Skytap for both production and disaster recovery/high availability (DR/HA) use cases. For DR/HA, they have the flexibility to host just the DR/HA environment in Skytap while keeping the production environment on-premises, or they can opt to have both environments fully hosted in Skytap on Azure. This allows them to easily implement a comprehensive DR/HA strategy that meets their specific business needs.

Several technologies are native to IBM i that can be used to provide a data replication solution for the platform. One such solution is IBM PowerHA SystemMirror for i (PowerHA).

PowerHA offers a complete end-to-end integrated clustering solution for DR/HA. PowerHA provides a data and application resiliency solution that is an integrated extension of the IBM i operating system and storage management architecture. Among other features is the design objective of providing application HA during both planned and unplanned outages.

PowerHA geographic mirroring offers a straightforward, cost-effective, HA solution for small to mid-sized organizations. Typically, PowerHA geographic mirroring is used with internal disk storage, and it provides alternative solutions that require additional configuration and management associated with an external storage device.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/powerha-geomirror-media/image1.png">

## IBM i PowerHA - Geographic Mirroring Pre-Flight Configurations<a name="preflight"></a>

1.  Provisioning of IBM i LPAR (VM) as Primary Node on cloud.skytap.com including PowerHA Licensed Program license inclusion in Region 1. In particular, make sure the VM has the correct number of disk LUNS and capacity.

2.  Provisioning of IBM i LPAR (VM) as Backup Node on cloud.skytap.com including PowerHA Licensed Program license inclusion in Region 2.

Note: Match the number and size of LUNS on the primary VM.

As a prerequisite for this procedure, ensure both the IBM i nodes have Network attributes of **Allowing add to Cluster**are set to **\*ANY** as shown below in the screen capture. This can be changed via the **CHGNETA** command.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/powerha-geomirror-media/image2.png" width="500">

3.  The best practice is to verify PTFs and other prerequisites on both IBM i nodes are up to date.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/powerha-geomirror-media/image3.png" width="500">

4.  Provisioning of Windows VM as a node to host IBM i ACS (Access Client Solutions). This will be used for the 5250 Emulator and Navigator for i.

5.  Testing of Communication between the three nodes via network reachability. The network provisioning across Geo locations would include the following:

a)  Provisioning/Configuration of VPN between regions via WAN links.

b)  Each WAN and VPN configured within it should have its own subnets.

c)  The VMs must be attached to the configured WAN/VPNs.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/powerha-geomirror-media/image4.png" width="300">

<br>

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/powerha-geomirror-media/image5.png" width="300">

All Three nodes can interact with each other over the network. Refer to the following screen capture as an example.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/powerha-geomirror-media/image6.png">

6.  Configuration of PowerHA (Primary and Backup Nodes) including Activation of nodes, Domain Name additions, iASP configurations, Cluster Resource Groups and associated device registration, GEO Mirror Configurations and testing of Geo Mirror based switch over.

###### *[Back to the Top](#toc)*

## Implement Geographic Mirroring<a name="implement"></a>

To configure geographic mirroring using IBM Navigator for i, follow the steps below in a browser.

In a web browser, create a session with the URL system:2001, where the system is the hostname of the system. To configure using Green Screen, follow the steps below:

### Create cluster ([CRTCLU](https://www.ibm.com/support/knowledgecenter/ssw_ibm_i_74/cl/crtclu.htm)) and start cluster nodes ([STRCLUNOD](https://www.ibm.com/support/knowledgecenter/ssw_ibm_i_74/cl/strclunod.htm))<a name="createandstartnodes"></a>

```bash
CRTCLU CLUSTER(\<ClusterName>)) NODE((\<PrimaryNodeName>(\<PrimaryNode_IP_Interface>))(\<BackupNodeName>(\<BackupNode_IP_Interface>))
```

Example:
```bash
CRTCLU CLUSTER(GEOCLU1) NODE((PRIMARY (\'10.0.0.1\'))(BACKUP(\'10.0.0.2\')))
```
After the cluster is created and nodes are defined for it, you can start the cluster. From the primary node, type the following command strings:

```bash
STRCLUNOD CLUSTER(CLUSTER1) NODE(PRIMARY)

STRCLUNOD CLUSTER(GCLUSTER1) NODE(BACKUP)
```

**Note:** Before you continue, check the previous work. Ensure both nodes are active by using the **DSPCLUIN**F command, or WRKCLU, Opt.6.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/powerha-geomirror-media/image7.png" width="600">

### Add nodes to the device domain ([ADDDEVDMNE](https://www.ibm.com/support/knowledgecenter/ssw_ibm_i_74/cl/adddevdmne.htm))<a name="addnodes"></a>

```bash
ADDDEVDMNE CLUSTER(CLUSTER1) DEVDMN(DEVDMN) NODE(PRIMARY)

ADDDEVDMNE CLUSTER(CLUSTER1) DEVDMN(DEVDMN) NODE(BACKUP)
```

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/powerha-geomirror-media/image8.png" width="600">

**Note:** Before you continue, check the previous work. Ensure both nodes show they are part of the device domain, using the **DSPCLUINF** command, or **WRKCLU, Opt 7**.

### Create independent ASP ([CFGDEVASP](https://www.ibm.com/support/knowledgecenter/ssw_ibm_i_74/cl/cfgdevasp.htm))<a name="createindependentasp"></a>

Create the iASP from a command line interface from the primary node:

```bash
CFGDEVASP ASPDEV(PHAIASP) ACTION(\*CREATE) TYPE(\*PRIMARY) PROTECT(\*NO) ENCRYPT(\*NO) UNITS(\*SELECT)
```

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/powerha-geomirror-media/image9.png" width="600">

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/powerha-geomirror-media/image10.png" width="600">

### Create iASP Device Description on Backup Node ([CRTDEVASP](https://www.ibm.com/support/knowledgecenter/ssw_ibm_i_74/cl/crtdevasp.htm))<a name="creatiASPBackup"></a>

```bash
CRTDEVASP DEVD(PHAIASP) RSRCNAME(PHAIASP)
```

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/powerha-geomirror-media/image11.png">

#### Create the Device CRG (Cluster Resource Group), but do NOT start the CRG yet ([CRTCRG](https://www.ibm.com/support/knowledgecenter/ssw_ibm_i_74/cl/crtcrg.htm))<a name="createcrg"></a>

```bash
**CRTCRG CLUSTER(CLUSTER1) CRG(PHACRG) CRGTYPE(\*DEV) EXITPGM(\*NONE) USRPRF(\*NONE) RCYDMN((PRIMARY \*PRIMARY \*LAST PRIMARY (10.0.0.1))(BACKUP \*BACKUP 1 BACKUP (10.0.0.2)) CFGOBJ((GEOIASP))**
```
<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/powerha-geomirror-media/image12.png" width="600">

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/powerha-geomirror-media/image13.png" width="600">

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/powerha-geomirror-media/image14.png" width="600">

### Configure Geographic Mirroring ([CFGGEOMIR](https://www.ibm.com/support/knowledgecenter/ssw_ibm_i_74/cl/cfggeomir.htm))<a name="configuregeomirror"></a>

```bash
CFGGEOMIR ASPDEV(PHAIASP) ACTION(\*CREATE) SRCSITE(\*) TGTSITE(\*) SSN(BACKUP/PRIMARY/PHASSN) DELIVERY(\*SYNC) UNIT (\*SELECT) CLUSTER(\*) CRG(\*) MODE(\*SYNC) PRIORITY(\*HIGH)
```

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/powerha-geomirror-media/image15.png" width="600">

Once the command finishes successfully, post that and perform the following steps.

#### Start the CRG ([STRCRG](https://www.ibm.com/support/knowledgecenter/ssw_ibm_i_74/cl/strcrg.htm))<a name="startcrg"></a>

```bash
STRCRG CLUSTER(CLUSTER1) CRG(PHACRG)
```

#### Vary on the IASP ([VRYCFG](https://www.ibm.com/support/knowledgecenter/ssw_ibm_i_74/cl/vrycfg.htm))<a name="varyoniasp"></a>

```bash
VRYCFG CFGOBJ(PHAIASP) CFGTYPE(\*DEV) STATUS(\*ON)
```

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/powerha-geomirror-media/image16.png" width="600">

Once varied on, you should see the following message.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/powerha-geomirror-media/image17.png" width="600">

#### Start the ASP session ([DSPASPSSN](https://www.ibm.com/support/knowledgecenter/ssw_ibm_i_74/cl/dspaspssn.htm))<a name="startaspsession"></a>

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/powerha-geomirror-media/image18.png" width="600">

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/powerha-geomirror-media/image19.png" width="600">

### GEO Mirror Setup stands complete.

*NOTE: Customer is responsible for restoring the data to the i ASP on the primary.*

In summary, Skytap on Azure combined with the power and flexibility of IBM PowerHA SystemMirror for i (PowerHA) offers a complete end-to-end cloud platform to support an organization\'s mission-critical applications as a part of its overall cloud strategy. This solution can be deployed to support both Primary and HA/DR systems fully in Skytap on Azure, or a hybrid approach where the Primary or HA/DR system remains on-prem.

#### Next steps<a name="nextsteps"></a>

**Main Overview**
> [Skytap Well-Architected Framework](../../README.md)

**Operational Excellence**
>[Skytap Operational Excellence Pillar](../../operations/README.md)

**Resiliency**
>[Skytap Resiliency Pillar](../README.md)
>* [Migration](../migrations.md)
>* [Protection](../backups.md)
>* [Disaster Recovery](../disasterrecovery.md)
>* [High Availability](../ibmihadr.md)
>
>**Migration Solutions**
>* [Cold (Warm) Migrations (Backup and Restore)](./ColdMigrationsOverview.md)
>* [Hot Migrations (Replication Sync)](./HotMigrationOverview.md)
>
>**Design**
>* [Design Considerations for Azure](../designconsiderationsazure.md)
>* [Design Considerations for IBM Cloud](../designconsiderationsibm.md)

**Security**
> * [Skytap Security Pillar](../../security/README.md)
