---
Title: IBM i workload migration from on-prem to Skytap using Microsoft Azure Data Box
Description: Skytap Cold Migration Solution - IBM i workload migration from on-premises to Skytap using Microsoft Azure Data Box
Authors: Ateesh Sharma – Skytap Cloud Solutions Engineer, Richard Field – Skytap Cloud Solutions Architect, Matthew Romero - Technical Product Marketing Manager
---
# IBM i Migration to Skytap on Azure Using Microsoft Azure Data Box
This guide is provided “as-is”. Information and views expressed in this document, including URL and other Internet website references, may change without notice and usage of the included material assumes this risk.

This document does not provide you with any legal rights to any intellectual property in any product. You may copy and use this document for your internal, reference purposes.

# Table of Contents <a name="toc"></a>

* [Introduction](#intro)
* [Key takeaways](#takeaways)
* [Prerequisites](#begin)
* [Order and configure the data box](#orderandconfig)
* [Setting up data box in customer Data Center](#setuponprem)
* [Performing backups of LPARs on data box](#backup2databox)
* [Copying backup .iso files from windows to data box](#isocopy)
* [Shipping data box to Azure](#ship2azure)
* [Restoring data to Skytap LPAR](#restoreLPAR)
* [Timing estimates](#estimates)
* [Next Steps](#nextsteps)

## Introduction <a name="intro"></a>
There are multiple strategies to migrate IBM i LPARs to [Skytap on Azure](https://www.skytap.com/skytap-on-azure/). Migration using [Microsoft Azure Data Box](https://www.youtube.com/watch?v=DKREXDCuGqk) is suitable when the customer has multiple Tera Bytes (>10) of data on LPAR The data box is delivered to customer data center and attached to windows server using SMB. The windows server folder is mounted to IBM i LPAR using NFS. Data is written from IBM i to windows folder and copied manually to the mounted Data Box path. 

This document provides overview of steps of this process and to perform a restore on a Skytap hosted LPAR.

Data cannot be copied directly from IBM i to MDM as they used different transfer protocols. Any host-based replication tool can be used to perform the delta sync between the on-prem LPAR and cloud LPAR after the initial restore is done using Micorosoft Azure Data Box.

## Key Takeaways <a name="takeaways"></a>

The objective of this document is to capture steps to perform below activities

1.  Order and configure data box.

2.  Attach data box to on-prem windows box using SMB.

3.  Mount windows box folder to IBM i using NFS.

4.  Take IBM i backups using windows NFS mount.

5.  Copy backup from windows folder to Data Box drive.

6.  Send Data box to azure cloud data center and restore data to cloud LPAR.


## Prerequisites <a name="Begin"></a>

* On prem-server should be at the latest PTF levels.

* Customer should have an existing [Azure Storage Account](https://learn.microsoft.com/en-us/azure/storage/common/storage-account-overview) and [Skytap SaaS subscription](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/skytapinc.skytap-on-azure-main1?tab=overview).

    3.  The on-prem LAPR should be on V7R2 or later OS version of IBM i

    4.  There should be a 10 Gig FC port available on server/switch that
        > can be used for communication with databox

    5.  Sufficient downtime should be provided for backups

    6.  Required LPPs should be installed on the LPAR

    7.  Transceivers for fiber ports on the databox to make connection

    8.  On prem windows server with sufficient resources. Disk space
        > should be more than the amount of backup to be taken on each
        > LPAR

4.  []{#_heading=h.3znysh7 .anchor}Order and configure the data box:

> Please reference to "Azure Databox order instructions" document

5.  []{#_heading=h.2et92p0 .anchor}Setting up data box in customer DC

    1.  **Setting up Data box in customer DC**

```{=html}
<!-- -->
```
a)  Use the steps in below link to physically install the data box in
    > customer DC

> [[https://docs.microsoft.com/en-us/azure/databox/data-box-deploy-set-up]{.ul}](https://docs.microsoft.com/en-us/azure/databox/data-box-deploy-set-up)

b)  Add a windows server in the environment with more disk space than
    > the amount of backup to be taken.

c)  Assign an additional IP to the IBM i LPAR, the ip will be used for
    > DST LAN console

d)  Assign ips to windows VM and MDM device, The solution architecture
    > should look like below with all 10 Gb connections

> ![Diagram Description automatically
> generated](I:\Repos\Skytap\WAF\resiliency\solutions\databoxmigrationsmedia/media/image2.png){width="6.263888888888889in"
> height="2.9756944444444446in"}

1.  **Connect Databox to windows server (SMB)**

```{=html}
<!-- -->
```
a)  Login to the Databox interface using the login credentials provided
    > by azure

b)  Go to connect and copy-\> NFS-\> add the ips of windows and IBM i
    > LPAR and save to allow communication.

c)  Go to connect and copy-\>settings-\> enable SMB signing and restart
    > the databox. (This allows data to be copied from windows to
    > databox drive)

d)  On windows command line run below command to mount Databox path on y
    > drive:

> Net use y:
> [[\\\\192.168.2.10\\uksouthstg_BlockBlob]{.ul}](about:blank)
> /user:\<user ID> enter and provide password
>
> Both user id and password you can get from DBx SMB settings page
>
> For example:
>
> Net use y:
> [[\\\\**10.121.21.151**\\nascskytapdatabox_BlockBlob]{.ul}](about:blank)
> /user:nascskytapdatabox

1.  **Connecting IBM i LPAR to Windows server (NFS)**

```{=html}
<!-- -->
```
a)  **WINDOWS** - Install and configure NFS client on windows server

-   Install NFS Server through Server Manager icon on taskbar

    -   Server manager-\> manage -\> Add roles and features-\> next-\>
        > role based-\>next-\>expand file and storage services-\> File
        > and iSCSI services-\> Server for NFS-\>add features-\> next-\>
        > install- wait for install to complete

    -   Server manager -\> tools-\> NFS-\>right click -\> property it
        > should be TCP+UDP-\>activity logging -\> select all-\> apply
        > ok-\> stop and start the service

-   Setup Folder to share via NFS

    -   Create a folder called 'backup' on drive where the backup will
        > be done

    -   Right click on backup folder on windows -\> property-\>
        > advanced-\> change-\> type everyone-\> add-\>select
        > principle-\> everyone-\> full control-\> apply OK\
        > (this can be done on individual files too (this allows data
        > copy from NFS folder to DBx drive)

    -   Do the same in property -\> security advanced.

-   Go to property of folder (backup) to be shared-\> NFS sharing -\>
    > manage-\> select share-\> select allow anon access-\> permission
    > -\> select read-write and allow root access for ALL MACHINES-\>
    > apply and OK

b)  **IBM i** - Mount NFS folder on IBM i directory

-   Create mount path on iSeries server

> MKDIR DIR('/windows')

-   Add the cfgtcp option 12, host name , domain name and \*local

-   Cfgtcp 10: add entry for local ip with hostname and domain name, and
    > entries for windows server

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

c)  Assign the LAN console IP using SST using steps in below link

> [[https://www.ibm.com/docs/en/i/7.3?topic=dst-configuring-service-tools-server-using-sst]{.ul}](https://www.ibm.com/docs/en/i/7.3?topic=dst-configuring-service-tools-server-using-sst)
>
> Below is an example
>
> ![Graphical user interface, text Description automatically
> generated](I:\Repos\Skytap\WAF\resiliency\solutions\databoxmigrationsmedia/media/image1.png){width="6.263888888888889in"
> height="2.5944444444444446in"}

d)  Create device by running below commands

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

> Create and initialize sufficient optical iso files on windows server
> so that the backup can completed successfully.
>
> You can take below backups depending on your migration strategy

-   GO save 21

-   Go save 22+ 23

-   Individual libraries backups

-   Backups of save files for tape migration activity:

> For tape migration customer needs to identify libraries/directories
> whose backups they want to migrate to cloud. This method is not
> applicable for copying full tapes to cloud, we can only migrate
> backups of selected libraries/directories to cloud. Below are the
> high-level steps

1.  Identify the tapes and data to be migrated to cloud

2.  Restore the data on the on prem server in library identifying the
    > backups details (date and content). Do this for all tapes

3.  Save the libraries in .iso file named according to the content
    > (021120Daily)

4.  Move the data from Databox to Cloud blob and keep it there for
    > future use

Perform backup go save option 21/22/23 or save individual libraries
using SAVLIB command, just specify the device as NFSDEV01

7.  []{#_heading=h.3dy6vkm .anchor}Copying backup .iso files from
    windows to databox

> The backup files need to be copied from backups folder to the required
> folder in the DBx drive on windows server.
>
> After this the Databox is ready to be shipped to azure DC.

8.  []{#_heading=h.1t3h5sf .anchor}Shipping databox to Azure cloud DC

> Please reference to "Tutorial to return Azure Databox" document

9.  []{#_heading=h.4d34og8 .anchor}Restoring data to Skytap LPAR

    1.  The DBx data will be copied to azure Block Blob storage account.

> The iso files need to be downloaded from azure blob to Skytap LPAR
> using azure storage explorer.

2.  Once the backup .iso files are downloaded to Skytap Windows VM, FTP
    > the required files to NFS IBM i LPAR or target IBM i LPAR
    > depending on backups strategy

3.  To restore from a GO save 21 backup refer to the below document

> IBM i migration to Skytap -- GO save option 21

4.  To restore from a GO save 22+23 backup refer to the below document

> IBM i migration to Skytap -- GO save option 22+23

5.  Restore from individual libraries backups

> Restore the libraries from the .iso image file by loading it on an
> image catalog and using RSTLIB command

6.  Tape migration restores

-   Restore the backup iso file from cloud blob to windows server

-   FTP from windows to IBM i target LPAR

-   Create image catalog specifying the directory where .iso is.

-   ADDIMGCLGE for all .iso file

-   Restore the libraries from the .iso file to target LPAR's required
    > library

10. []{#_heading=h.2s8eyo1 .anchor}Timing estimates

> Below is the time estimate of some time-consuming activities

-   Creation of .iso on windows NFS mount :3 hrs to create 1\*400 GB
    > .iso

> Best approach is to create 4 .iso file on parallel sessions (takes
> around 5 hrs)

-   Copy of file from windows to Databox SMB drive: 1\*400 Gb file took
    > 1 hr 45 mins

> Running 2 parallel copy gave best speed in our tests

-   Copy file from blob to windows: 2\*400 GB files copy in parallel
    > took 2 hrs 30 mins

-   FTP from windows VM to IBM i target LPAR (same environment): 1\*400
    > GB files in parallel took 1 hr 10 mins

> Parallel FTP did not increase the overall speed, ensure that the IBM i
> FTP parameters are tuned to give best transfer speed

### Next steps

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
>* [Cold (Warm) Migrations (Backup and Restore)](./cold-migrations)
>
>**Design**
>* [Design Considerations for Azure](../design-considerations-azure)
>* [Design Considerations for IBM Cloud](../design-considerations-ibm)

**Security**
> * [Skytap Security Pillar](../../security/)