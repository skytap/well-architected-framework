---
Title: IBM i workload migration from on-prem to Skytap on Azure using Microsoft Azure Data Box
Description: Skytap Cold Migration Solution - IBM i workload migration from on-premises to Skytap on Azure using Microsoft Azure Data Box
Authors: Ateesh Sharma � Skytap Cloud Solutions Engineer, Richard Field � Skytap Cloud Solutions Architect, Matthew Romero - Technical Product Marketing Manager
---
# IBM i Migration to Skytap on Azure Using Microsoft Azure Data Box
This guide is provided �as-is�. Information and views expressed in this document, including URL and other Internet website references, may change without notice and usage of the included material assumes this risk.

This document does not provide you with any legal rights to any intellectual property in any product. You may copy and use this document for your internal reference purposes.

# Table of Contents <a name="toc"></a>

* [Introduction](#intro)
* [Key Takeaways](#takeaways)
* [Prerequisites](#begin)
* [Order and Configure the Azure Data Box](#orderandconfig)
* [Setting up Azure Data Box in Customer Data Center](#setuponprem)
  * [Connect Azure Data Box to Windows Server (SMB)](#connectdatabox2smb)
  * [Connecting IBM i LPAR to Windows Server (NFS)](#LPAR2NFS)
* [Performing Backups of LPARs on Azure Data Box](#backup2databox)
* [Copying Backup .ISO Files from Windows to Azure Data Box](#isocopy)
* [Shipping Azure Data Box to Microsoft](#ship2azure)
* [Restoring data to Skytap LPAR](#restoreLPAR)
* [Timing Estimates](#estimates)
* [Next Steps](#nextsteps)

## Introduction <a name="intro"></a>
There are multiple strategies to migrate IBM i LPARs to <a href="https://www.skytap.com/skytap-on-azure/" target="_blank">Skytap on Azure</a>. 

Migration using <a href="https://www.youtube.com/watch?v=DKREXDCuGqkMicrosoft" target="_blank">Azure Data Box</a> is suitable when the customer has multiple Terabytes (TB) (>10) of data on an LPAR. The Azure Data Box is delivered to a customer data center and attached to a Windows server using SMB. The Windows server folder is mounted to a IBM i LPAR using NFS. Data is then written from IBM i to the Windows folder and copied manually to the mounted Azure Data Box path. 

This document provides an overview of the steps of this process and how to perform a restore on a Skytap hosted LPAR.

Data cannot be copied directly from IBM i to MDM as they used different transfer protocols. Any host-based replication tool can be used to perform the delta sync between the on-prem LPAR and cloud LPAR after the initial restore is done using Azure Data Box.

## Key Takeaways <a name="takeaways"></a>

The objective of this document is to capture steps to perform the following activities:

1.  Order and configure Azure Data Box.

2.  Attach Azure Data Box to on-prem Windows box using SMB.

3.  Mount Windows box folder to IBM i using NFS.

4.  Take IBM i backups using Windows NFS mount.

5.  Copy backup from Windows folder to Azure Data Box drive.

6.  Send Azure Data Box to Microsoft Azure Data Center and restore data to cloud LPAR.


## Prerequisites <a name="Begin"></a>

* On prem-server should be at the latest PTF levels.

* Customer should have an existing <A href="https://learn.microsoft.com/en-us/azure/storage/common/storage-account-overview" target="_blank">Azure Storage Account</a> and an active <a href="https://azuremarketplace.microsoft.com/en-us/marketplace/apps/skytapinc.skytap-on-azure-main1?tab=overviewSkytap" target="_blank">Skytap on Azure SaaS subscription</a>.

* The on-prem LPAR should be on V7R2 or later OS version of IBM i.

* There should be a 10 Gig FC port available on server/switch that can be used for communication with the Azure Data Box.

* Sufficient downtime should be provided for backups.

* Required LPPs should be installed on the LPAR.

* Transceivers for fiber ports on the Azure Data Box to make connection.

* On-prem Windows server with sufficient resources. Disk space should be more than the amount of backup to be taken on each LPAR.

## Order and configure the Azure Data Box <a name="orderandconfig"></a>

> Please reference the <a href="https://learn.microsoft.com/en-us/azure/databox/data-box-deploy-ordered" target="_blank">Azure Data Box order instructions</a> document.

## Setting up an Azure Data Box in customer data center  <a name="setuponprem"></a>

Use the steps in the link below to physically install the Azure Data Box in the customer data center.

> <a href="https://docs.microsoft.com/en-us/azure/databox/data-box-deploy-set-up" target="_blank">https://docs.microsoft.com/en-us/azure/databox/data-box-deploy-set-up</a>

* Add a Windows server in the environment with more disk space than the amount of backup to be taken.

* Assign an additional IP to the IBM i LPAR, the IP will be used for DST LAN console.

* Assign IPs to Windows VM and MDM device. The solution architecture should look like below with all 10 Gb connections.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/databoxmigrationsmedia/media/image2.png" width="700">

## Connect Azure Data Box to Windows server (SMB) <a name="connectdatabox2smb"></a>

1. Login to the Azure Data Box interface using the login credentials provided by Azure. 

1. Go to connect and copy-\> NFS-\> add the IPs of Windows and IBM i LPAR and save to allow communication.

1. Go to connect and copy-\>settings-\> enable SMB signing and restart the Data Box. (This allows data to be copied from Windows to Data Box drive)

1. On Windows command line run the below command to mount the Data Box path on y drive:

```powershell
Net use y: \\192.168.2.10\\uksouthstg_BlockBlob /user:\<user ID>
``` 

Both user id and password you can get from DBx SMB settings page

For example:
```powershell
Net use y: \\10.121.21.15\\nascskytapdatabox_BlockBlob /user:nascskytapdatabox
``` 
## Connecting IBM i LPAR to Windows server (NFS) <a name="LPAR2NFS"></a>

**WINDOWS** - Install and configure NFS client on Windows server

-   Install NFS Server through Server Manager icon on taskbar

    -   Server manager-\> manage -\> Add roles and features-\> next-\> role based-\> next-\> Expand file and storage services-\> File and iSCSI services-\> Server for NFS-\> Add Features-\> next-\> Install- wait for install to complete

    -   Server manager -\> Tools-\> NFS-\> Right Click -\> Property (it should be TCP+UDP)-\> Activity Logging -\> Select All-\> apply -\> ok-\> Stop and Start the Service

-   Setup Folder to share via NFS

    -   Create a folder called 'backup' on drive where the backup will be done.

    -   Right-click on backup folder on Windows -\> property-\> Advanced-\> change-\> Type: Everyone-\> Add-\> Select Principle-\> Everyone-\> Full control-\> Apply -\> OK (this can be done on individual files too) (this allows data copy from NFS folder to DBx drive)

    -   Do the same in property -\> security advanced.

-   Go to property of folder (backup) to be shared-\> NFS sharing -\>
    > manage-\> select share-\> select allow anon access-\> permission
    > -\> select read-write and allow root access for ALL MACHINES-\>
    > apply and OK

**IBM i** - Mount NFS folder on IBM i directory

-   Create mount path on iSeries server

> MKDIR DIR('/windows')

-   Add the cfgtcp option 12, host name, domain name and \*local

-   Cfgtcp 10: add entry for local IP with hostname and domain name, and
    > entries for Windows server

-   Mount the NFS server directory on path created

> Mount Type(\*NFS) MFS('\<ip of windows>:/\<windows folder>')
> MNTOVRDIR('/\<server directory>')
>
> For example:
>
> Mount Type(\*NFS) MFS('**10.74.74.157**:/backup')
> MNTOVRDIR('/windows')

a)  Create iso images for backup by running below commands

-   CALL PGM (QP2TERM)

-   cd /windows

-   dd if=/dev/zero of=IMAGE01.ISO bs=1M count=100000 (100 GB)

-   dd if=/dev/zero of=IMAGE02.ISO bs=1M count=100000 (100 GB)

> Create number of images depending on size of backup data

b)  Created volume list by running below commands

-   touch VOLUME_LIST

-   echo 'IMAGE01.ISO W' >\> VOLUME_LIST

-   echo 'IMAGE02.ISO W' >\> VOLUME_LIST

-   verify using "cat VOLUME_LIST" command\
    > F3 out

c)  Assign the LAN console IP using SST using steps in the link below

> [[https://www.ibm.com/docs/en/i/7.3?topic=dst-configuring-service-tools-server-using-sst]{.ul}](https://www.ibm.com/docs/en/i/7.3?topic=dst-configuring-service-tools-server-using-sst)

Below is an example:

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/databoxmigrationsmedia/media/image1.png" width="700">

d)  Create device by running the below commands

-   CRTDEVOPT DEVD(NFSDEV01) RSRCNAME(\*VRT) LCLINTNETA(\*SRVLAN)

-   RMTINTNETA(\<window server ip> ) NETIMGDIR('/backup')

-   VRYCFG CFGOBJ(NFSDEV01) CFGTYPE(DEV) STATUS(\*ON)

> Run below command to verify that you can see backup images created

-   WRKIMGCLGE \*DEV NFSDEV01

e)  Initialize the volumes

-   LODIMGCLGE IMGCLG(\*DEV) IMGCLGIDX(2) DEV(NFSDEV01)

-   INZOPT NEWVOL(IVOL02) DEV(NFSDEV01) CHECK(\*NO)

-   LODIMGCLGE IMGCLG(\*DEV) IMGCLGIDX(1) DEV(NFSDEV01)

-   INZOPT NEWVOL(IVOL01) DEV(NFSDEV01) CHECK(\*NO)

> Run these commands for all the iso images created

6.  []{#_heading=h.tyjcwt .anchor}Performing backups of LPARs on databox

> Create and initialize sufficient optical iso files on Windows server
> so that the backup can completed successfully
>
> You can take below backups depending on your migration strategy

-   GO save 21

-   Go save 22+ 23

-   Individual libraries backups

-   Backups of save files for tape migration activity:

> For tape migration the customer needs to identify libraries/directories
> whose backups they want to migrate to the cloud. This method is not
> applicable for copying full tapes to cloud, we can only migrate
> backups of selected libraries/directories to cloud. Below are the
> high-level steps:

1.  Identify the tapes and data to be migrated to the cloud

2.  Restore the data on the on-prem server in library identifying the
    > backups details (date and content). Do this for all tapes.

3.  Save the libraries in .ISO file named according to the content
    > (021120Daily)

4.  Move the data from Data Box to Cloud blob and keep it there for
    > future use

Perform backup go save option 21/22/23 or save individual libraries
using SAVLIB command, just specify the device as NFSDEV01

7.  []{#_heading=h.3dy6vkm .anchor}Copying backup .ISO files from
    windows to Data Box

> The backup files need to be copied from backups folder to the required
> folder in the DBx drive on Windows server.
>
> After this the Data Box is ready to be shipped to the Azure DC.

8.  []{#_heading=h.1t3h5sf .anchor}Shipping Azure Data Box to Azure DC

> Please reference to "Tutorial to return Azure Data Box" document

9.  []{#_heading=h.4d34og8 .anchor}Restoring data to Skytap LPAR

    1.  The DBx data will be copied to the Azure Block Blob storage account.

> The iso files need to be downloaded from Azure blob to Skytap LPAR
> using Azure Storage Explorer.

2.  Once the backup .ISO files are downloaded to Skytap Windows VM, FTP
    > the required files to NFS IBM i LPAR or target IBM i LPAR
    > depending on backups strategy

3.  To restore from a GO save 21 backup refer to the below document

> IBM i migration to Skytap -- GO save option 21

4.  To restore from a GO save 22+23 backup refer to the below document

> IBM i migration to Skytap -- GO save option 22+23

5.  Restore from individual libraries backups

> Restore the libraries from the .ISO image file by loading it on an
> image catalog and using RSTLIB command

6.  Tape migration restores

-   Restore the backup iso file from cloud blob to Windows server

-   FTP from Windows to IBM i target LPAR

-   Create image catalog specifying the directory where .ISO is.

-   ADDIMGCLGE for all .ISO file

-   Restore the libraries from the .ISO file to target LPAR's required
    > library

10. []{#_heading=h.2s8eyo1 .anchor}Timing estimates

> Below is the time estimate of some time-consuming activities

-   Creation of .ISO on Windows NFS mount :3 hrs to create 1\*400 GB
    > .ISO

> Best approach is to create 4 .ISO file on parallel sessions (takes
> around 5 hrs)

-   Copy of file from Windows to Data Box SMB drive: 1\*400 Gb file took
    > 1 hr 45 mins

> Running 2 parallel copy gave best speed in our tests

-   Copy file from blob to Windows: 2\*400 GB files copy in parallel
    > took 2 hrs 30 mins

-   FTP from Windows VM to IBM i target LPAR (same environment): 1\*400
    > GB files in parallel took 1 hr 10 mins

> Parallel FTP did not increase the overall speed, ensure that the IBM i
> FTP parameters are tuned to give best transfer speed

#### Next steps

**Main Overview**
> [Skytap Well-Architected Framework](../../)

**Operational Excellence**
>[Skytap Operational Excellence Pillar](../../operations/)

**Resiliency**
>[Skytap Resiliency Pillar](../)
>* [Migration](../migrations)
>* [Protection](../backups)
>* [Disaster Recovery](../disaster-recovery)
>* [High Availability](../ibmi-disaster-recovery)
>
>**Migration Solutions**
>* [Hot Migrations (Replication Sync)](./hot-migrations)
>
>**Design**
>* [Design Considerations for Azure](../design-considerations-azure)
>* [Design Considerations for IBM Cloud](../design-considerations-ibm)

**Security**
> * [Skytap Security Pillar](../../security/)
