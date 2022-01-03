---
title: IBM i workload migration using Go Save/Restore, Option 22-23
description: Skytap Cold Migration Solution - IBM i workload migration using Go Save/Restore, Option-22,23
author: Tony Perez - Sales Engineer, Mayank Kumar - Cloud Solutions Architect
---

# IBM i workload migration using Go Save/Restore, Option 22-23

This guide is provided “as-is”. Information and views expressed in this document, including URL and other Internet website references, may change without notice. You bear the risk of using it.

This document does not provide you with any legal rights to any intellectual property in any product. You may copy and use this document for your internal, reference purposes.

### Table of Contents <a name="toc"></a>

* [Key Takeaways](#takeaways)
* [Before you begin](#begin)
* [Determining size to be backed up on ISO optical image](#determineisosize)
* [Create virtual optical media](#createvom)
* [Save, Option 22 to virtual optical media](#gosave)
* [Create virtual tape media](#createvtm)
* [Deploy IBM i in Skytap cloud](#deployinskytap)
* [Network configuration](#netconfig)
* [Go Restore, Option 23](#gorestore)
* [APPENDIX](#appendix)
  * [Example Commands](#examplecommands)
  * [Create virtual tape image catalog](#createvtic)


### Key Takeaways<a name="takeaways"></a>

These are some key takeaway for developers and operators to consider from this guide:

* Demonstrate how an IBM i workload can be migrated to Skytap on Azure using Go Save/Restore, Option 22-23.

* Full-system recovery including LIC, OS installation

#### Before you begin<a name="begin"></a>

* Install latest PTFs on IBM i with below individual PTFs installed

  * [Minimum](https://cloud.ibm.com/docs/power-iaas?topic=power-iaas-minimum-levels) PTF level for IBM i

* Utilized ASP should be less than 48% in IBM i LPAR. Amount of disk space can be reduced by doing clean up as mentioned in the link below:

  * [https://www.ibm.com/support/pages/reducing-system-asp-disk-space-dasd-storage-used](https://www.ibm.com/support/pages/reducing-system-asp-disk-space-dasd-storage-used)

* Azure storage account with blob storage container and valid SAS key

  * Create Azure storage account as required, and generate SAS token 

* AZCopy the virtual tapes from the NFS mounted directory into Azure object storage container

* Linux / Windows server in on-premise network, preferably close to IBM i server, preferably Linux with desktop environment over Windows, no specific storage/compute requirements, installation of AZCopy utility

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/media/image7.png" width="400">

###### *[Back to the Top](#toc)*
#### Determining size to be backed up on ISO optical image<a name="determineisosize"></a>

1. Go Disktasks, Option-1 to collect disk space information **OR**, same can be run using RTVDSKINF command

2. RTVDSKINF job will be submitted in WRKACTJOB, depending on the system size, command completion may vary system to system

3. Once the job completes, print information using PRTDSKINF or Go Disktasks, Option 2

4. In report type, select library

5. Use the following command:

```bash 
WRKSPLF
```
6. Then display PRTDSKINF report

7. Review QUSRSYS, QGPL, QSYS, LIC and IBM libraries size. Accordingly, create the optical media size

###### *[Back to the Top](#toc)*
#### Create virtual optical media<a name="createvom"></a>

[https://www.ibm.com/docs/en/i/7.2?topic=storage-setting-up-virtual-optical](https://www.ibm.com/docs/en/i/7.2?topic=storage-setting-up-virtual-optical) 

```bash
> CRTIMGCLG IMGCLG(skytap30GB) DIR(\'/skytap30GB\') 

> ADDIMGCLGE IMGCLG(skytap30GB) FROMFILE(\*NEW) TOFILE(skytap30GB.iso) IMGSIZ(30000)

> CRTDEVOPT DEVD(skyopt30gb) RSRCNAME(\*VRT)

> VRYCFG CFGOBJ(skyopt30gb) CFGTYPE(\*DEV) STATUS(\*ON)

> LODIMGCLG IMGCLG(skytap30GB) DEV(skyopt30gb)

> INZOPT NEWVOL(MYVOLUMEID) DEV(skyopt30gb) CHECK(\*NO) TEXT(DESCRIPTION)
```
###### *[Back to the Top](#toc)*
#### Save, Option 22 to virtual optical media<a name="gosave"></a>

1. Bring system to restricted state using the following command:
```bash
ENDSBS \*ALL \*IMMED
```
2. Go Save, Option22 and take below parameters on the command:

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/media/image6.png">

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/media/image4.png">

3. Save QGPL and QUSRSYS and append to existing ISO in Optical device

4. SAVLIB, F4 to prompt: (QGPL first, then QUSRSYS)

    -   Choose Existing Optical device

    -   F10 for more options

    -   Change parameters: 

    -   Spooled file data = \*NONE, to not include output queues 

    -   Save file data = \*NO 

5. Use the following command to bring system out of restricted state:

```bash 
STRSBS QCTL 
```

6. Create NFS export on directory where image catalog is loaded, so you can copy the ISO to a machine where it can be uploaded to Assets using the following commands:

```bash
ENDNFSSVR \*ALL

CHGNFSEXP OPTIONS(\'-F -O RW=,ANON=0\') DIR(\'/skytap30GB\')

STRNFSSVR \*ALL
```

7.  Begin uploading the ISO to Assets --

    -   Mount NFS exported directory from IBM i server to Linux/Windows machine 

    -   Load Skytap portal in browser of desktop session on Linux/Windows machine

    -   Log into Skytap, Click on 'Assets'

    -   Upload ISO to Skytap account, select region where your LPAR(s) to be restored in Skytap will be hosted

###### *[Back to the Top](#toc)*
#### Create virtual tape media<a name="createvtm"></a>

1. Create virtual tape image Catalog for Option 23 data

[https://www.ibm.com/docs/en/i/7.3?topic=tape-setting-up-virtual-storage](https://www.ibm.com/docs/en/i/7.3?topic=tape-setting-up-virtual-storage)

2.  Determine tape size based on overall data size, you must pre-create each tape required for the total Option 23 save

3. Bring system to restricted state and start Go SAVE, Option 23

4. Set up NFS exports for AZCopy Linux server to Azure using the following commands:
```bash
ENDNFSSVR \*ALL

CHGNFSEXP OPTIONS-F -O RW=,ANON=0 DIR 'directoryWhereTapeImageCatalogIsLocated'

STRNFSSVR \*ALL
```

5. Mount NFS directory above to Linux server in same network as source IBM i LPAR

Example:
```bash
mount -o nfsvers=3 10.0.0.1:/scratchASP/vtapes/home/skytap/scratchasp
```

6. Install AZCopy utility as required download here:

[https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy-v10](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy-v10)

7. extract AZCopy archive to directory

Example:
```bash
tar -xvf azcopy\_linux\_amd64\_10.12.2.tar.gz
```

8. Copy save files from NFS mounted location to Azure blob storage container

   [https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy-blobs-upload?toc=/azure/storage/blobs/toc.json\#upload-a-directory](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy-blobs-upload?toc=/azure/storage/blobs/toc.json\#upload-a-directory)

Windows Example:
```powershell
azcopy copy \'\<local-directory-path-of-NFS-mount\>\' \'https://\<storage-account-name\>.\<blob or dfs\>.core.windows.net/\<container-name\>\' \--recursive
```

Linux Example:
```bash
./azcopy copy '/home/LinuxUser/localNFSDirectory'https://<myStorageAccount>.blob.core.windows.net/containerName?\<SAS-Key>\ \--recursive
 ```

###### *[Back to the Top](#toc)*
#### Deploy IBM i in Skytap<a name="deployinskytap"></a>

1. Deploy IBM i from template

2. Delete load source drive from hardware settings in Skytap GUI

3. Add new disk(s) for System ASP

4. Set boot mode to D Manual mode in hardware settings

5. Boot LPAR from Skytap UI and wait to reach C200 4130 boot code (No Load source Found)

6. Click button in UI to mount Option 22 ISO from Assets to LPAR

7. On Install LIC screen, take Option 2 to Install LIC and Initialize system

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/media/image5.png">

8. Post LIC installation, do disk configuration

9. Initialize non configured disks and add to ASP

10. Install OS using below option:

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/media/image2.png">

11. Enable automatic configuration -- Y on set major system option screen.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/media/image1.png">

12. Change following on next screen:

  * Start print writers = N

  * Start system to restricted state = Y

  * Set major system options = Y

  * Define or change system at IPL = Y

13. Select Option 3 twice to change the following system values:

  * QALWOBJRST \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ \*ALL

  * QFRCCVNRST \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ 0

  * QINACTITV \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ \*NONE

  * QJOBMSGQFL \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ \*PRTWRAP

  * QJOBMSGQMX \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ 30 (minimum, 64 recommended)

  * QLMTDEVSSN \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ 0

  * QLMTSECOFR \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ 0

  * QMAXSIGN \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ \*NOMAX

  * QPFRADJ \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ 2

  * QPWDEXPITV \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ \*NOMAX

  * QSCANFSCTL \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ \*NOPOSTRST

  * QVFYOBJRST \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ 1

14. Press F3 twice and change the QSECOFR password. Note: the current QSECOFR password will be QSECOFR (capital letters)

15. Restore QGPL and QUSRSYS

16. RSTLIB, F4 to prompt

17. Select QGPL and QUSRSYS

18. Select existing optical Device

19. Select ALWOBJDIF as \*All and MBROPT as \*All

###### *[Back to the Top](#toc)*
#### Network configuration<a name="netconfig"></a>

1. WRKHDWRSC \*CMN, check resource name of ethernet port

2. Delete existing Ethline

3. Create new Ethline

4. Configure default route and DNS

5. IPL the LPAR in normal mode

###### *[Back to the Top](#toc)*
#### Go Restore, Option 23<a name="gorestore"></a>

1. Create NFS export directory on scratch ASP

2. Run the following commands to export:

```bash
ENDNFSSVR \*ALL

CHGNFSEXP OPTIONS(\'-F -O RW=,ANON=0\') DIR(\'/directoryWhereScratchASPisMounted\')

STRNFSSVR \*ALL
```

3.  Deploy Centos Desktop Machine from Template into Environment

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/media/image8.png">

4. Boot and configure root and secondary users in CentOS

5. Mount NFS directory from IBM i server in Skytap on Linux machine:

Example:
```bash
mount -o nfsvers=3 10.0.0.1:/scratchASP/vtapes/home/skytap/scratchasp
```

6. Install AZCopy utility, download here:

[https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy-v10](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy-v10)

7. extract AZCopy archive to directory

Example:
```bash
tar -xvf azcopy\_linux\_amd64\_10.12.2.tar.gz
```
8.  Copy save files from Azure blob storage container to mounted NFS export directory on target IBM i server in Skytap

[https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy-blobs-upload?toc=/azure/storage/blobs/toc.json\#upload-a-directory](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy-blobs-upload?toc=/azure/storage/blobs/toc.json\#upload-a-directory)

Windows Example:
```powershell
azcopy copy \'https://\<storage-account-name\>.\<blob or dfs\>.core.windows.net/\<container-name\>/  \<directory-path\>\'\'\<local-directory-path\>\' \--recursive
```
Linux Example:
```bash
./azcopy copy https://\<myStorageAccount>\.blob.core.windows.net/containerName?\<SAS-Key\>'/home/LinuxUserinSkytapCentos/localNFSDirectory'\ --recursive
```

9. Create virtual tape image catalog in /scratchASP/vtapes

10. Add virtual tape(s) to image catalog in directory above

11. GO Restore Option 23 from virtual tape image catalog above

 <img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/media/image9.png">

###### *[Back to the Top](#toc)*
#### APPENDIX:<a name="appendix"></a>

###### *[Back to the Top](#toc)*
#### Example commands:<a name="examplecommands"></a>

```bash
mkdir /mydir
```

```bash
sudo 
``` 
or 
```bash 
su
```
to elevate the user to root.

```bash 
mount -o nfsvers=3 10.0.0.1:/scratchASP/vtapes/home/skytap/scratchasp
```

You can check to see the directory is exported with this command if the mount is not successful:

```bash 
showmount -e 10.0.0.1 (IP address for IBMi LPAR)
```

###### *[Back to the Top](#toc)*
#### Create virtual tape image catalog<a name="createvtic"></a>

1. Run AZCopy to copy virtual tape(s) from Azure storage to the NFS mount
    -   Examples here:
   > [[https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy-blobs-download]{.underline}](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy-blobs-download)

2. Go Restore, Option 23

3. Choose virtual tape device, and other parameters as below:

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/media/image3.png">

4. Check for joblog once the restore is completed using DSPJOBLOG

5. Save the joblog using DSPJOBLOG Output(\*Print)

6. Restore any not saved object

***NOTE***: *Data Restoration is complete at this point*

7. Check PTF status and change the system values as required

## Next Steps

**Main Overview**
> [Skytap Well-Architected Framework](../../../README.md)

**Operational Excellence**
>[Skytap Operational Excellence Pillar](../../../operations/README.md)

**Resiliency**
>**Migration Solutions**
>* [Hot Migations (Replication Sync)](../HotMigrationOverview.md)
>* [Cold (Warm) Migrations (Backup and Restore)](../ColdMigrationsOverview.md)

>**Design**
>* [Design Considerations for Azure](../../designconsiderationsazure.md)
<!-- 
>* [Design Considerations for IBM Cloud](../../designconsiderationsibm.md)
-->

**Security**
> * [Skytap Security Pillar](../../../security/README.md)

