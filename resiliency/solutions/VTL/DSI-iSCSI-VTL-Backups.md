---
Title: iSCSI VTL backups - DSI Solution
Description: DSI iSCSI VTL implementation in Skytap IBM i LPARs and the process to perform bare metal recovery
Authors: Mayank Kumar - Cloud Solutions Architect
permalink: /resiliency/solutions/VTL/DSI-iSCSI-VTL-Backups/
---
# iSCSI VTL backups for IBM i running on Skytap on Azure

This guide is provided “as-is”. Information and views expressed in this document, including URL and other Internet website references, may change without notice and usage of the included material assumes this risk.

This document does not provide you with any legal rights to any intellectual property in any product. You may copy and use this document for your internal, reference purposes.

# Table of Contents <a name="toc"></a>

* [Introduction](#_Toc125454662)
* [Pre-Requisites](#prerequisites)
* [Installation](#install)
* [Backups](#backups)

### Introduction<a name="_Toc125454662"></a>
DSI is an iSCSI VTL which provides high performance backup/restore
functionality in a cloud environment. Like other VTLs, DSI is a
disk-based backup solution which emulates physical tape. Since VTL
technology uses disks to back up data, it eliminates the media and
mechanical errors that can occur with physical tapes and drives.

### Pre-Requisites<a name="prerequisites"></a>

IBM i LPARs should be upgraded to the latest PTFs prior to VTL
installation.

Skytap has pre-built templates that can be used to configure the DSI VTL in a Skytap environment.

### Installation<a name="install"></a>

IBM i can be configured using SQL services or advanced analysis macro from the service tool. SQL services is the preferred method and the steps are documented below:

1.  From your IBM i Secure Remote Access (SRA) console in Skytap use the following commands:

```bash
Strsql

Select \* from iscsi_info
```

Note: This command describes iSCSI initiator on IBM i, it should be empty as shown in the below screenshot:
<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/VTL/dsiiscsivtlmedia/image1.png" width="700">

2.  Next, create the initiator on IBM i using the command in the below image where Target Name will be the target VTL name. The IP of the VTL also needs to be mentioned as shown below:

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/VTL/dsiiscsivtlmedia/image2.png" width="700">

3.  Then, get the initiator name to add in DSI GUI as shown below:

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/VTL/dsiiscsivtlmedia/image3.png" width="500">

4.  Login to the VTL GUI and add the license keys from the configuration wizard:

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/VTL/dsiiscsivtlmedia/image4.png" width="300">

5.  Add IBM i client using the step below:

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/VTL/dsiiscsivtlmedia/image5.png" width="300">

6.  IPL the storage IOP from IBM i SST. The IOP resource name can be found from <tt>WRKHDWRSC \*STG</tt>with type <tt>298A</tt>. Once the IOP is IPLed, the IBM i IP will reflect as shown below and the client can be added.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/VTL/dsiiscsivtlmedia/image6.png" width="700">
<br>
<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/VTL/dsiiscsivtlmedia/image7.png" width="700">
<br>
<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/VTL/dsiiscsivtlmedia/image8.png" width="700">

7.  Assign the VTL to the IBM i using the step below:

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/VTL/dsiiscsivtlmedia/image9.png" width="400">
<br>
<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/VTL/dsiiscsivtlmedia/image10.png" width="700">

8.  Now the client will have the VTL assigned and in the IBM i OS, you can check in <tt>WRKHDWRSC \*STG</tt>, that the storage IOP has the VTL assigned. If not, IPL the IOP again.

Note: It may take around 5 minutes for the VTL resources to show up in IBM i. The same can be checked using <tt>WRKMLBSTS</tt> as well.

###### *[Back to the Top](#toc)*

### Backup<a name="backup"></a>

1.  From your IBM i Secure Remote Access (SRA) console in Skytap use the following commands: <tt>INZBRM \* Device</tt> to add the VTL in BRMS.

2.  You can create a new media class or edit the existing one with command <tt>WRKCLSBRM</tt>.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/VTL/dsiiscsivtlmedia/image11.png" width="700">

3.  Change media policy to use the media class: <tt>GO BRMS</tt>, Option 11, Option 7.

4.  Edit the control group and add the media policy, backup device.

5.  Add the media <tt>(WRKMEDBRM)</tt> in BRMS or by using the below command:

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/VTL/dsiiscsivtlmedia/image12.png" width="700">

6.  Start backup using <tt>STRBKUBRM</tt>

7.  Check the logs using <tt>DSPLOGBRM</tt> once the backup is completed

Note: For automated weekly full system backups use control groups <tt>QNFSSYSFUL</tt> and <tt>QNFSIPLFUL</tt>. These backup tapes can also be used for bare metal recovery by using an NFS server and BRMS recovery report. These control groups can be added using <tt>INZBRM \*NFSCTLG</tt>. For bare metal recovery, you need to restore the <tt>QNFSIPLFUL</tt> backup on an IBM i LPAR capable of hosting the image as NFS mount. Once the LIC, OS are restored with TCP/IP started, the VTL can be used to restore the user data by following the BRMS recovery report.

###### *[Back to the Top](#toc)*

### Next steps

**Main Overview**
> [Skytap Well-Architected Framework]({{ site.navigation.Home }})

**Operational Excellence**
> [Skytap Operational Excellence Pillar]({{ site.navigation.Operations }})

**Resiliency**
> [Skytap Resiliency Pillar]({{ site.navigation.Resiliency }})
> * [Migration]({{ site.navigation.Resiliency }}migrations)
> * [Protection]({{ site.navigation.Resiliency }}backups)
> * [Disaster Recovery]({{ site.navigation.Resiliency }}disaster-recovery)
> * [High Availability]({{ site.navigation.Resiliency }}high-availability)
>
> **Migration Solutions**
> * [Hot Migrations (Replication Sync)]({{ site.navigation.Resiliency }}solutions/hot-migrations)
> * [Cold (Warm) Migrations (Backup and Restore)]({{ site.navigation.Resiliency }}solutions/cold-migrations)
>
> **Design**
> * [Design Considerations for Azure]({{ site.navigation.Resiliency }}design-considerations-azure)
> * [Design Considerations for IBM Cloud]({{ site.navigation.Resiliency }}design-considerations-ibm)

**Security**
> [Skytap Security Pillar]({{ site.navigation.Security }})