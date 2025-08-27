---
title: IBM i workload migration using Go Save/Restore, Option 22-23
description: Skytap Cold Migration Solution - IBM i workload migration using Go Save/Restore, Option 22-23
author: Tony Perez - Sales Engineer, Mayank Kumar - Cloud Solutions Architect
permalink: /resiliency/solutions/GoSave/
---

# IBM i workload migration using Go Save/Restore, Option 22-23

This guide is provided “as-is”. Information and views expressed in this document, including URL and other Internet website references, may change without notice and usage of the included material assumes this risk.

This document does not provide you with any legal rights to any intellectual property in any product. You may copy and use this document for your internal, reference purposes.

### Table of Contents <a name="toc"></a>

* [Key Takeaways](#takeaways)
* [Before you begin](#begin)
* [Determining size to be backed up on ISO optical image](#determineisosize)
* [Create virtual optical media](#createvom)
* [Save, Option 22 to virtual optical media](#gosave)
* [Save QGPL and QUSRSYS and append to existing ISO in Optical device](#appendiso)
* [Create virtual tape media](#createvtm)
* [Deploy IBM i in {{site.Brand}} cloud](#deployinskytap)
* [APPENDIX](#appendix)
  * [Example Commands](#examplecommands)


### Key Takeaways<a name="takeaways"></a>

These are some key takeaway for developers and operators to consider from this guide:

* Demonstrate how an IBM i workload can be migrated to {{site.SoA}} using Go Save/Restore, Option 22-23.

* Full-system recovery including LIC, OS installation

#### Before you begin<a name="begin"></a>

* Perform review to ensure there are no corrupted objects on the source LPAR

* Install latest PTFs on IBM i with below individual PTFs installed

  * <a href="https://cloud.ibm.com/docs/power-iaas?topic=power-iaas-minimum-levels" target="_blank">Minimum</a> PTF level for IBM i

* Utilized ASP should be less than 48% in IBM i LPAR. Amount of disk space can be reduced by doing clean up as mentioned in the link below:

  * <a href="https://www.ibm.com/support/pages/reducing-system-asp-disk-space-dasd-storage-used" target="_blank">https://www.ibm.com/support/pages/reducing-system-asp-disk-space-dasd-storage-used</a>

* Azure storage account with blob storage container and valid SAS key

  * Create Azure storage account as required, and generate SAS token 

* AZCopy the virtual tapes from the NFS mounted directory into Azure object storage container

* Linux / Windows server in on-premise network, preferably close to IBM i server, preferably Linux with desktop environment over Windows, no specific storage/compute requirements, installation of AZCopy utility

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/media/image7.png" width="400">

*Note: Many of the commands below will use example file names, directory
names and sizes. Please replace these according to your environment. 

*For example, in the command*

```bash
CRTIMGCLG IMGCLG(***skytap30GB***) DIR(\'*/**skytap30GB**\'*) 
```

*Replace ***skytap30GB*** to the variable in your environment.*

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

7. Review QUSRSYS, QGPL, QSYS, LIC and IBM libraries size. Accordingly, create the optical media size. (Generally, the maximum size seen for the sum of these is \~50 GB)

###### *[Back to the Top](#toc)*
#### Create virtual optical media<a name="createvom"></a>

<a href="https://www.ibm.com/docs/en/i/7.2?topic=storage-setting-up-virtual-optical" target="_blank">https://www.ibm.com/docs/en/i/7.2?topic=storage-setting-up-virtual-optical</a>

```bash
> CRTIMGCLG IMGCLG(skytap30GB) DIR(\'/skytap30GB\') 

> ADDIMGCLGE IMGCLG(skytap30GB) FROMFILE(\*NEW) TOFILE(skytap30GB.iso) IMGSIZ(30000)

> CRTDEVOPT DEVD(skyopt30gb) RSRCNAME(\*VRT)

> VRYCFG CFGOBJ(skyopt30gb) CFGTYPE(\*DEV) STATUS(\*ON)

> LODIMGCLG IMGCLG(skytap30GB) DEV(skyopt30gb)

> INZOPT NEWVOL(MYVOLUMEID) DEV(skyopt30gb) CHECK(\*NO) TEXT(DESCRIPTION)
```

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/gosavemedia/image1.png">

*Example image above shows 15 GB image size. Commands listed use 30 GB size. Size according to your own needs.*

###### *[Back to the Top](#toc)*
#### Save, Option 22 to virtual optical media (on Source System)<a name="gosave"></a>

1. Bring system to restricted state using the following command:
```bash
ENDSBS \*ALL \*IMMED
```

2. Go Save, Option22 and take below parameters on the command:

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/gosavemedia/image2.png">
<br>
<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/gosavemedia/image3.png">

3.  Change the parameters as shown in the screen shot above.

4.  At bottom of screen, it will show progress (ex. "Started processing 16177 objects, completed 14000 objects.").

#### Save QGPL and QUSRSYS and append to existing ISO in Optical device (on Source System)<a name="appendiso"></a>

1. End subsystems again (using command ENDSBS \*ALL \*IMMED) to save
    QGPL and QUSRSYS

2.  Save QGPL and QUSRSYS and append to existing ISO in Optical device.

3.  On the command line, type SAVLIB, F4 to prompt: (Specify QGPL first,
    then QUSRSYS)

4.  Choose Existing Optical device (in this example, we have been using '***SKYOPT30GB***')

5. F10 for more options

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

    -   Load {{site.Brand}} portal in browser of desktop session on Linux/Windows machine

    -   Log into {{site.Brand}}, Click on 'Assets'

    -   Upload ISO to {{site.Brand}} account, select region where your LPAR(s) to be restored in {{site.Brand}} will be hosted

###### *[Back to the Top](#toc)*
#### Create virtual tape media (on Source System)<a name="createvtm"></a>

1. Create virtual tape image Catalog for Option 23 data

<a href="https://www.ibm.com/docs/en/i/7.3?topic=tape-setting-up-virtual-storage" target="_blank">https://www.ibm.com/docs/en/i/7.3?topic=tape-setting-up-virtual-storage</a>

1. Use the following commands to create the virtual tape image catalog for Option 23 data:

```bash
CRTIMGCLG IMGCLG(CLG1) DIR(\'/CLG1DIR\') TYPE(\*TAP)(Create an empty tape catalog.)

ADDIMGCLGE IMGCLG(***CLG1***) FROMFILE(\*NEW) TOFILE(***VOL001***) IMGSIZ(***10000***) VOLNAM(***VOL001***)
```

Note:Repeat for each volume

```bash
CHGATR OBJ(\'***/tape/catalog1***\') ATR(\*ALWSAV) VALUE(\*Yes) Subtree(\*All)
```

2. Determine tape size based on overall data size, you must pre-create each tape required for the total Option 23 save

3. Bring system to restricted state (using command ENDSBS \*ALL \*IMMED) and run the command GO SAVE, and choose Option 23

4. On the "Specify Command Defaults" screen, specify device name according to your environment. Page down and Press Enter.

5. Set up NFS exports for AZCopy Linux server to Azure using the following commands:

```bash
ENDNFSSVR \*ALL

CHGNFSEXP OPTIONS-F -O RW=,ANON=0 DIR 'directoryWhereTapeImageCatalogIsLocated'

STRNFSSVR \*ALL
```

6. Mount NFS directory above to Linux server in same network as source IBM i LPAR

Example:

```bash
mount -o nfsvers=3 10.0.0.1:/scratchASP/vtapes/home/skytap/scratchasp
```

7. Install AZCopy utility as required download here:

<a href="https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy-v10" target="_blank">https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy-v10</a>

8. extract AZCopy archive to directory

Example:

```bash
tar -xvf azcopy\_linux\_amd64\_10.12.2.tar.gz
```

9. Copy save files from NFS mounted location to Azure blob storage container

<a href="https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy-blobs-upload?toc=/azure/storage/blobs/toc.json\#upload-a-directory" target="_blank">https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy-blobs-upload?toc=/azure/storage/blobs/toc.json\#upload-a-directory</a>

Windows Example:

```powershell
azcopy copy \'\<local-directory-path-of-NFS-mount\>\' \'https://\<storage-account-name\>.\<blob or dfs\>.core.windows.net/\<container-name\>\' \--recursive
```

Linux Example:

```bash
./azcopy copy '/home/LinuxUser/localNFSDirectory'https://<myStorageAccount>.blob.core.windows.net/containerName?\<SAS-Key>\ \--recursive
 ```

###### *[Back to the Top](#toc)*
#### Deploy IBM i in {{site.Brand}} (using NFS LPAR and Target System)<a name="deployinskytap"></a>

Before we initialize the target system, we must first transfer the Optical ISO image to IBM i NFS server. The file transfer method is up to user choice- examples of file transfer methods include FTP, AZCopy using Azure storage account, or BRMS + ICC. After transferring these files to the NFS LPAR, Follow the steps for IBM i recovery using IBM i NFS server to transfer files to the target system:

**Do these steps on the NFS LPAR**

-   WRKHDWRSC \*CMN -- Press Enter

Make note of the Communication Resources that are Operational -- it will
be associated with the Ethernet line(s)

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/gosavemedia/image4.png">


##### Create Virtual Optical Device<a name="createvod"></a>

1.  From the NFS LPAR use the following commands:
```bash
CRTDEVOPT DEVD(***OPTVRT01***) RSRCNAME(\*VRT)
```
3. VARY ON the Optical drive by Choosing Option 8 in WRKDEVD Optvrt01 command.

##### Create Image Catalog<a name="createimagecatalog"></a>
1.  From the NFS LPAR use the following commands:
```bash
CRTIMGCLG IMGCLG(***IMGCATALOG***) DIR(\'***/DIR***\') TYPE(\*OPT) CRTDIR(\*YES)

*CRTDIR(\*YES) (Note: only necessary if directory does not exist)
```

Directory specified here should be the same directory as where the .iso
file was created.

##### Add Image to Image Catalog<a name="addimagetocatalog"></a>
1.  From the NFS LPAR use the following commands:
```bash
ADDIMGCLGE IMGCLG(ORTPREM) FROMFILE('***/IMGCATALOG/{{site.Brand}}30GB.iso***') TOFILE('\****fromfile***')
```

2.  Use F10 to see all parameters when adding

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/gosavemedia/image5.png">

*Example view of screen after pressing F10 to see parameters*

##### Load Image Catalog<a name="loadimagecatalog"></a>
From the NFS LPAR load image catalog to Optical drive

1. Type WRKIMGCLG -- Press Enter

Virtual Device name will be the device you created in prior steps.

2. Type 8 -- Press Enter

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/gosavemedia/image6.png">

##### Verify Image Catalog<a name="verifyimagecatalog"></a>
Note: This may take a few minutes.

1. From the NFS LPAR on the next screen, for the image catalog you just created

2. Type 10 -- Press Enter

3. On Verify Image Catalog screen:

    -   "Verify type" = \*LIC

    -   "Network file server share" = \*YES

    -   Other settings stay default

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/gosavemedia/image7.png">
<br>
<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/gosavemedia/image8.png">
<br>
<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/gosavemedia/image9.png">

##### Start the NFS server and change NFS export options<a name="startnfsandupdateoptions"></a>
1.  From the NFS LPAR use the following commands:
```bash
STRNFSSVR SERVER(\*All)
```

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/gosavemedia/image10.png">

```bash
CHGNFSEXP OPTIONS('-i -o ro') DIR('/**xx**')
```
*Don't forget the single quotation marks!*

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/gosavemedia/image11.png">

##### Change object authorities and TFTP attributes<a name="changeattributes"></a>
1.  From the NFS LPAR use the following commands:
```bash
CHGAUT OBJ(\'**/*Dir'***) USER(\*PUBLIC) DTAAUT(\*RWX) SUBTREE(\*ALL)

CHGTFTPA AUTOSTART(\*YES) ALTSRCDIR(\'**/*Dir'***)

CHGAUT OBJ(\'**/*Dir'***) USER(QTFTP) DTAAUT(\*RX) SUBTREE(\*ALL)
```

##### Restart the TFTP server<a name="restarttftpserv"></a>
This is how the client and the target LPAR will be communicate.

1. From the NFS LPAR use the following commands:
```bash
ENDTCPSVR SERVER(\*TFTP)

STRTCPSVR SERVER(\*TFTP)
```

##### Enable the IBM i Client (Target)
 **Follow the steps below on the IBM i Client (Target) LPAR**

1. Login to SST (Service Tools) by typing **STRSST** then press enter.

2. Enter the following information:

-   **Service Tools User ID:** The service tools user ID you sign on
    with. (Default is QSECOFR)

-   **Password:** The password associated with this user ID. (Default is
    QSECOFR)

-   *If you do not know the QSECOFR SST password, it can be reset using
    CHGDSTPWD \*Default*

2.  **Select option 8 -- Work with service tools User IDs and Devices**

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/gosavemedia/image12.png">

-   Press F13 and select the LAN adapter\
    The LAN adapter needs to be on the same VLAN as IBM i NFS server.

3.  **Configure service tools LAN adapter:**

-   IP version allowed = IPV4

-   Internet address = you can use the same IP of the client (TARGET)

-   Gateway router address = use the same gateway on the client

-   Subnet mask = use the same

-   Press F7=Store

-   Press F13= Deactivate

-   Press F14=Activate

**Use F3 to exit out of Service Tools.**

***In some cases, you may not find the LAN adapter in SST if the LPAR is
has only one adapter. Follow below steps to disable the ethernet line
and TCP services. Once the IP and Ethline are varied Off, the adapter is
free and can be seen in the SST.***

-   CFGTCP, Option 1

-   Use Option 10 to end the IP

-   WRKLIND

-   Use option 8(Work with status) in front of Ethline

-   Use option 2 to vary off

4.  **Verify port 3000 status as active using NETSTAT**

    -   From command line type NETSTAT, specify \*CNN -- Press F14 to
        display port numbers

    -   *TCP must be started*

5.  **Create client server optical device**

-   From command line type CRTDEVOPT, type the choices below, Press F4
    to prompt

    -   Device Description = *Can be any name here*

    -   Resource Name = \*VRT

    -   Device Type = \*RSRCNAME

-   Local internet address = \*SRVLAN

-   Remote internet address = the IP address of the IBM i VSI NFS Server

-   Network image directory = '**/xx'**

Use Option 1 to Vary On the optical device.

6.  **Vary on the optical device created in previous step and verify.**

    -   Access the remote image catalog using WRKIMGCLGE IMGCLG(\*DEV)
        DEV(***OPTVRT01**)*

    -   *Or, WRKIMGCLGE then F4 to Prompt. On the next screen:*

        -   *Image Catalog = \*DEV*

        -   *Device Name = Optical device name*

7.  **Start network install:**

    -   STRNETINS press F4 to prompt

-   Network optical device = Optical Device

-   Installation option = \*LIC

-   Keylock mode = \*MANUAL

**This completes step sections 5A and 5B.** **The next steps will
continue with Step 5 which is the process of initializing and
configuring the target system in {{site.Brand}}.**

2.  **Installing LIC:**

    -   On Install LIC screen, choose Option 1 to Install Licensed
        Internal Code.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/gosavemedia/image13.png">

-   F10 to Continue

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/gosavemedia/image14.png">


-   Choose Option 2 to Install LIC and Initialize system (On the target
    system that we will be building from the scratch

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/gosavemedia/image15.png">

-   Press F10 to continue

3.  **Post LIC Installation, Do Disk Initialization: Initialize non
    configured disks and add to ASP**

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/gosavemedia/image16.png">

-   Select Option 3 to Enter Dedicated Service Tools (DST).

    -   Username: QSECOFR is default

    -   Password: QSECOFR is default

-   From the DST Menu

    -   Select **Option 4 -- Work with Disk Units** from DST menu

    -   Select **Option 1- Work with Disk Configuration** on the with
        Disk Units display.

-   To add Disk to ASP and Balance ASP

    -   Select **Option 3- Work with ASP configuration** on the Work
        with Disk Configuration display.

    -   Select **Option 3- Add units to ASPs,** or, to add units to ASPs
        and also balance data choose **Option 8- Add units to ASPs and
        balance data** on the Work with ASP Configuration display.

    -   Select **option 3** to add units to existing ASPs.

-   Enter a 1 in each "Specify ASP" field. Press enter each time.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/gosavemedia/image17.png">

-   A "Problem Report" screen will appear. Press F10 to ignore problems,
    F10 to add and balance.

Disk Configuration will begin. This process will take several minutes.
When this completes, move on to install the operating system.

4.  **Install OS using below option (Select Option 2- Install the
    operating system):**

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/gosavemedia/image18.png">

In the device type, Select Option 5 -- network device

5.  **Log in using QSECOFR**

6.  Enable automatic configuration -- Y on set major system option
    screen.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/gosavemedia/image19.png">

7.  **Change following on next screen:**

-   Set date and time.

-   Choose System Time Zone

-   Start print writers = N

-   Start system to restricted state = Y

-   Set major system options = Y

-   Define or change system at IPL = Y

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/gosavemedia/image20.png">

Press enter to continue.

5.  **Select Option 3 twice (Select System Value Commands > Work with
    System Values) to change the following system values:**

-   QALWOBJRST \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ \*ALL

-   QFRCCVNRST \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ 0

-   QINACTITV \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ \*NONE

-   QJOBMSGQFL \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ \*PRTWRAP

-   QJOBMSGQMX \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ 30 (minimum, 64
    recommended)

-   QLMTDEVSSN \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ 0

-   QLMTSECOFR \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ 0

-   QMAXSIGN \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ \*NOMAX

-   QPFRADJ \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ 2

-   QPWDEXPITV \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ \*NOMAX

-   QSCANFSCTL \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ \*NOPOSTRST

-   QVFYOBJRST \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ 1

5.  **Press F3 twice and change the QSECOFR password.**

> *Note: the current QSECOFR password will be QSECOFR (capital letters).
> The new password must be between 6 and 8 characters and is
> case-sensitive.*

6.  ***Create optical device using CRTDEVOPT, Press F4***

-   *Local internet address = \*SRVLAN*

-   *Remote internet address = the ip address of the IBM i NFS Server*

-   *Network image directory = '**/xx'***

-   *Vary ON the device using WRKDEVD command*

7.  **Restore QGPL and QUSRSYS**

-   RSTLIB, F4 to prompt

-   Select QGPL and QUSRSYS

-   Select existing optical Device

-   F10

-   MBROPT (Database member option) to \*ALL

-   Page down

-   Select ALWOBJDIF (Allow object Differences) as \*ALL

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/gosavemedia/image21.png">

-   Press enter to begin restoring. QUSRSYS and QGPL should have 0
    "object not restored" when complete.

*Note: If you see the error message below, "File not selected..." when
running command, F1 on error to view logs and scroll to bottom. As long
as you see that "0 objects not restored", QGPL and QUSRSYS restore was
successful.*

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/gosavemedia/image22.png">

Error message shown here

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/gosavemedia/image23.png">

"0 objects not restored" in Job Logs.

8.  **Go Restore, Option 22 using below options:**

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/gosavemedia/image24.png">

*\*Specify device name according to your environment.*

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/gosavemedia/image25.png">

Type "G" and press enter to Go Ahead.

*If you see Error Messages, hit F1. View Job Logs. Page down to read
errors logs. There will be a few objects which will not be restored as
the "allow Object to be Saved" Attribute for those objects was set to N.
F3 to Exit and move on.*

**Step 6: Network configuration (on Target System)**

*Once the LAN adapter is added in the UI, a new Ethernet line needs to
be created, with TCP configured for it.*

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/gosavemedia/image26.png">

**6.1. Configure DHCP**

-   From command line, type WRKHDWRSC \*CMN

-   Check resource name associated with Communication Resource-

    -   IBM i 7.2/7.4 is typically **CMN04**,

    -   IBM i 7.3 is typically **CMN05**.

    -   You will use one of these in the next command.

-   See that CMN03/CMN04/CMN05 is The Ethline. Select resource with
    option 5 to view details.

-   Delete any existing Ethline (or, if line exists, vary on with
    option 8. If line doesn't exist, use option 1 to create it with Name
    Ethernet or whatever you want. Vary on with 1 afterward. Exit to
    main menu using F3)

-   Exit using F3.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/gosavemedia/image27.png">

**6.2. Create the Ethernet Line (if you deleted existing ethline)**

-   CRTLINETH LIND(ETHLINE2) RSRCNAME(***CMN05***) 

-   Type WRKCFGSTS \*LIN

    -   Option 1 to VARY ON the ETHLINE2

    -   Wait 10-20 seconds, hit F5 to refresh and confirm if ETHLITCP is
        Varied On.

**6.3. Configure Default route and DNS using CFGTCP Command**

-   From command line, type CFGTCP

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/gosavemedia/image28.png">

-   **Option 2 to work with routes.**

-   **Remove any state route with option 4**

-   Option 1- Work with Interfaces

-   If one doesn't exist:

    -   Add one with the internet address of \*IP4DHCP with Line
        Description of ETHLINE2

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/gosavemedia/image29.png">

*Use option 1 to add \*IP4DHCP*

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/gosavemedia/image30.png">
<br>
<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/gosavemedia/image31.png">

-   Hit enter twice to save it

-   Go to \*IP4DHCP, selection F11 to view status

If Interface Status is Inactive, use Option 9 to start it

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/gosavemedia/image32.png">

-   F5 to refresh

-   F3 twice to go back to the main console screen

On Work with Configuration Status Screen

VARY OFF any other VETHLINE

**6.4 Normal IPL**

-   PRDWNSYS \*IMMED to power system down

-   Bring system back up with a normal IPL

-   Choose Time Zone

-   Press enter through any errors- you will see some as we have not yet
    finished the restore. (We have only completed Option 22 restore,
    have yet to perform Option 23 restore to complete the full system
    restore).

-   Start subsystems manually STRSBS SBSD(QCTL), or CALL QSYS/QSTRUP

**Step 7: Go Restore, Option 23 (on Target System)**

1.  **If using FTP to transfer Option 23 backup to LPAR:**

    -   Make sure the Ethline is Varied ON and IP is Active

    -   STRTCPSVR \*FTP to start the FTP server

    -   FTP the files from FTP Server **IP Address**

2.  **Create virtual tape image catalog**

    -   From the command line type CRTIMGCLG

    -   Name your image catalog

    -   Name your directory that the image catalog will be located in

    -   Image catalog type is \*TAP

-   CRTDEVTAP as shown in below screen shot:

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/gosavemedia/image33.png">

-   WRKCFGSTS - \*DEV

-   1 to VARY ON '**TAPVRT**' (the virtual tape device you created)

3.  Add virtual tape(s) to image catalog in directory above

4.  **Load image catalog to virtual tape drive**

    -   WRKIMGCLG

    -   Option 8 to load **IMGCATALOG** (the name of your image catalog)
        with **TAPVRT** (the virtual tape device )

5.  **GO Restore Option 23 from virtual tape image catalog above**

    -   type GO RESTORE 23 -- All user data

    -   Subsystems will be ended, restore process will begin

6.  **Choose virtual tape device, and other parameters as below:**

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/gosavemedia/image24.png">

7.  Check for job log once the restore is completed using DSPJOBLOG

8.  Save the job log using DSPJOBLOG Output(\*Print)

9.  Restore any not saved object

10. Data Restoration is complete at this point

11. Check PTF status (WRKPTFGRP) and change the system values as
    required (WRKSYSVAL)

12. **Check the job log from the restoration to ensure the restoration
    was completed successfully:**

    -   WRKJOBLOG

    -   Select the Job Log for the restore with Option 5, to read
        through. Can also search through the logs for "Restore Not
        Completed" to target any issues listed in the logs.

13. Take a normal IPL to bring the system up gracefully.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/gosavemedia/image34.png">

14. Change system name using CHGNETA

#### APPENDIX:<a name="appendix"></a>

1. Deploy IBM i from template

2. Delete load source drive from hardware settings in {{site.Brand}} GUI

3. Add new disk(s) for System ASP

4. Set boot mode to D Manual mode in hardware settings

5. Boot LPAR from {{site.Brand}} UI and wait to reach C200 4130 boot code (No Load source Found)

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

5. Mount NFS directory from IBM i server in {{site.Brand}} on Linux machine:

Example:
```bash
mount -o nfsvers=3 10.0.0.1:/scratchASP/vtapes/home/skytap/scratchasp
```

6. Install AZCopy utility, download here:

<a href="https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy-v10" target="_blank">https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy-v10</a>

7. extract AZCopy archive to directory

Example:
```bash
tar -xvf azcopy\_linux\_amd64\_10.12.2.tar.gz
```
8.  Copy save files from Azure blob storage container to mounted NFS export directory on target IBM i server in {{site.Brand}}

<a href="https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy-blobs-upload?toc=/azure/storage/blobs/toc.json\#upload-a-directory" target="_blank">https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy-blobs-upload?toc=/azure/storage/blobs/toc.json\#upload-a-directory</a>

Windows Example:
```powershell
azcopy copy \'https://\<storage-account-name\>.\<blob or dfs\>.core.windows.net/\<container-name\>/  \<directory-path\>\'\'\<local-directory-path\>\' \--recursive
```
Linux Example:
```bash
./azcopy copy https://\<myStorageAccount>\.blob.core.windows.net/containerName?\<SAS-Key\>'/home/LinuxUserin{{site.Brand}}Centos/localNFSDirectory'\ --recursive
```

9. Create virtual tape image catalog in /scratchASP/vtapes

10. Add virtual tape(s) to image catalog in directory above

11. GO Restore Option 23 from virtual tape image catalog above

 <img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/media/image9.png">

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

### Next steps

**Main Overview**
> [{{site.Brand}} Well-Architected Framework]({{ site.navigation.Home }})

**Operational Excellence**
> [{{site.Brand}} Operational Excellence Pillar]({{ site.navigation.Operations }})

**Resiliency**
> [{{site.Brand}} Resiliency Pillar]({{ site.navigation.Resiliency }})
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
> [{{site.Brand}} Security Pillar]({{ site.navigation.Security }})
