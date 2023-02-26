---
title: IBM i workload migration using Go Save/Restore, Option 22-23
description: Skytap Cold Migration Solution - IBM i workload migration using Go Save/Restore, Option 22-23
author: Tony Perez - Sales Engineer, Mayank Kumar - Cloud Solutions Architect
permalink: /resiliency/solutions/go-save
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
* [Create virtual tape media](#createvtm)
* [Deploy IBM i in Skytap cloud](#deployinskytap)
* [Network configuration](#netconfig)
* [Go Restore, Option 23](#gorestore)
* [APPENDIX](#appendix)
  * [Example Commands](#examplecommands)
  * [Create virtual tape image catalog](#createvtic)


### Key Takeaways<a name="takeaways"></a>

This documentation will demonstrate how an IBM I workload can be
migrated to an LPAR on Skytap using an IBM i backup and recovery method
referred to as "Go, Save Option 22,23", because of the commands which
are used to take and restore the backups on the IBM i system.

The goal of this document is to perform a full-system recovery including
LIC and OS installation.

#### Before you begin<a name="begin"></a>

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

```Powershell
CRTIMGCLG IMGCLG(***skytap30GB***) DIR(\'*/**skytap30GB**\'*) 
```

*Replace ***skytap30GB*** to the variable in your environment.*

###### *[Back to the Top](#toc)*
#### Determining size to be backed up on ISO optical image<a name="determineisosize"></a>

1.  Go Disktasks, Option-1 to collect disk space information
    (<https://www.ibm.com/support/pages/go-disktasks-overview> )

2.  OR, same can be run using RTVDSKINF command

3.  RTVDSKINF job will be submitted in WRKACTJOB, depending on the
    system size, command completion may vary system to system

4.  Once the job completes, print information using PRTDSKINF or Go
    Disktasks, Option 2.

5.  In report type, select library

6.  WRKSPLF, display PRTDSKINF report

7.  Review QUSRSYS, QGPL, QSYS, LIC and IBM libraries size. Accordingly,
    create the optical media size

    1.  Add these up to size our<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/gosavemedia/ optical image, since Option 22 save
        includes LIC and Objects. (Generally, the maximum size seen for
        the sum of these is \~50 GB)

###### *[Back to the Top](#toc)*
#### Create virtual optical media<a name="createvom"></a>

<a href="https://www.ibm.com/docs/en/i/7.2?topic=storage-setting-up-virtual-optical" target="_blank">https://www.ibm.com/docs/en/i/7.2?topic=storage-setting-up-virtual-optical</a>

```bash
> CRTIMGCLG IMGCLG(skytap30GB) DIR(\'/skytap30GB\') 

> ADDIMGCLGE IMGCLG(skytap30GB) FROMFILE(\*NEW) TOFILE(skytap30GB.iso) IMGSIZ(30000)
```

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/gosavemedia/image1.png">

*Example image above shows 15 GB image size. Commands listed use 30 GB
size. Size according to your own needs.*

3.  CRTDEVOPT DEVD(***skyopt30GB***) RSRCNAME(\*VRT)

4.  VRYCFG CFGOBJ(***skyopt30GB***) CFGTYPE(\*DEV) STATUS(\*ON)

5.  LODIMGCLG IMGCLG(***skytap30GB***) DEV(***skyopt30GB***)

6.  INZOPT NEWVOL(***MYVOLUMEID***) DEV(***skyopt30GB***) CHECK(\*NO)
    TEXT(DESCRIPTION)

**Step 2: Save, Option 22 to virtual optical media (on Source System)**

1.  Bring system to restricted state using ENDSBS \*ALL \*IMMED

2.  Go Save, Option22 (run command GO SAVE , then choose option 22)

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/gosavemedia/image2.png">
<br>
<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/gosavemedia/image3.png">

3.  Change the parameters as shown in the screen shot above.

4.  At bottom of screen, it will show progress (ex. "Started processing
    16177 objects, completed 14000 objects.").

**Step 3: Save QGPL and QUSRSYS to append to existing ISO (on Source
System)**

1.  End subsystems again (using command ENDSBS \*ALL \*IMMED) to save
    QGPL and QUSRSYS

2.  Save QGPL and QUSRSYS and append to existing ISO in Optical device:

-   On the command line, type SAVLIB, F4 to prompt: (Specify QGPL first,
    then QUSRSYS)

-   Choose Existing Optical device (in this example, we have been using
    '***SKYOPT30GB***')

-   F10 for more options

-   Change parameters: 

-   Spooled file data = \*NONE, to not include output queues 

-   Save file data = \*NO 

**Step 4: Create virtual tape media (on Source System)**

Create virtual tape image Catalog for Option 23 data

<a href="https://www.ibm.com/docs/en/i/7.3?topic=tape-setting-up-virtual-storage" target="_blank">[https://www.ibm.com/docs/en/i/7.3?topic=tape-setting-up-virtual-storage]{.ul}</a>

1.  CRTIMGCLG IMGCLG(CLG1) DIR(\'/CLG1DIR\') TYPE(\*TAP)(Create an empty
    tape catalog.)

2.  ADDIMGCLGE IMGCLG(***CLG1***) FROMFILE(\*NEW) TOFILE(***VOL001***)
    IMGSIZ(***10000***) VOLNAM(***VOL001***)

    -   Repeat for each volume

3.  CHGATR OBJ(\'***/tape/catalog1***\') ATR(\*ALWSAV) VALUE(\*Yes)
    Subtree(\*All)

4.  Determine tape size based on over-all data size, you must pre-create
    each tape required for the total Option 23 save.

5.  Bring system to restricted state (using command ENDSBS \*ALL
    \*IMMED) and run the command GO SAVE, and choose Option 23

6.  On the "Specify Command Defaults" screen, specify device name
    according to your environment. Page down and Press Enter.

**Step 5: Deploy IBM i in Skytap cloud (using NFS LPAR and Target
System)**

1.  Before we initialize the target system, we must first transfer the
    Optical ISO image to IBM i NFS server. The file transfer method is
    up to user choice- examples of file transfer methods include FTP,
    AZCopy using Azure storage account, or BRMS + ICC. After
    transferring these files to the NFS LPAR, Follow the steps for IBM i
    recovery using IBM i NFS server to transfer files to the target
    system:

# **Step 5A: Do these steps on the NFS LPAR**

-   WRKHDWRSC \*CMN -- Press Enter

Make note of the Communication Resources that are Operational -- it will
be associated with the Ethernet line(s)

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/gosavemedia/image4.png">{width="6.133333333333334in"


> **5A.1: Create Virtual Optical Device**

-   Sign on to your NFS LPAR

-   CRTDEVOPT DEVD(***OPTVRT01***) RSRCNAME(\*VRT)

-   VARY ON the Optical drive

    -   Do this by Choosing Option 8 in WRKDEVD Optvrt01 command.

> **5A.2: Create image catalog**

-   CRTIMGCLG IMGCLG(***IMGCATALOG***) DIR(\'***/DIR***\') TYPE(\*OPT)
    CRTDIR(\*YES)

    -   *CRTDIR(\*YES) only necessary if directory does not exist*

Directory specified here should be the same directory as where the .iso
file was created.

## **5A.3: Add image to Image Catalog**

-   ADDIMGCLGE IMGCLG(ORTPREM)
    FROMFILE('***/IMGCATALOG/Skytap30GB.iso***')
    TOFILE('\****fromfile***')

-   Use F10 to see all parameters when adding

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/gosavemedia/image5.png">

*Example view of screen after pressing F10 to see parameters*

## **5A.4: Load Image Catalog** 

-   Load image catalog to Optical drive

    -   Type WRKIMGCLG -- Press Enter

    -   Virtual Device name will be the device you created in prior
        steps

    -   Type 8 -- Press Enter

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/gosavemedia/image6.png">

## **5A.6: Verify image Catalog (May take a few minutes)**

-   On the next screen, for the image catalog you just created

-   Type 10 -- Press Enter

-   On Verify Image Catalog screen:

    -   "Verify type" = \*LIC

    -   "Network file server share" = \*YES

    -   Other settings stay default

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/gosavemedia/image7.png">
<br>
<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/gosavemedia/image8.png">
<br>
<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/gosavemedia/image9.png">

**5A.7: Start the NFS server and change NFS export options**

-   STRNFSSVR SERVER(\*All)

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/gosavemedia/image10.png">

-   CHGNFSEXP OPTIONS('-i -o ro') DIR('/**xx**')

    -   *Don't forget the single quotation marks!*

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/GoSave/gosavemedia/image11.png">

## **5A.8: Change object authorities and TFTP attributes:**

-   CHGAUT OBJ(\'**/*Dir'***) USER(\*PUBLIC) DTAAUT(\*RWX)
    SUBTREE(\*ALL)

-   CHGTFTPA AUTOSTART(\*YES) ALTSRCDIR(\'**/*Dir'***)

-   CHGAUT OBJ(\'**/*Dir'***) USER(QTFTP) DTAAUT(\*RX) SUBTREE(\*ALL)

## **5A.9: Restart the TFTP server (This is how the client and the target LPAR will be communicate):**

-   ENDTCPSVR SERVER(\*TFTP)

-   STRTCPSVR SERVER(\*TFTP)

**Step 5B: Follow the steps below on the IBM i Client (Target) LPAR**

1.  **Login to SST (Service Tools) by typing STRSST then press enter**

    -   Enter the following information:

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
configuring the target system in Skytap.**

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

### Next Steps

**Main Overview**
> [Skytap Well-Architected Framework]({{ site.navigation.Home }})

**Operational Excellence**
> [Skytap Operational Excellence Pillar]({{ site.navigation.Operations }})

**Resiliency**
> [Skytap Resiliency Pillar]({{ site.navigation.Resiliency }})
> * [Migration]({{ site.navigation.Resiliency }}migrations)
> * [Protection]({{ site.navigation.Resiliency }}backups)
> * [Disaster Recovery]({{ site.navigation.Resiliency }}disaster-recovery)
> * [High Availability]({{ site.navigation.Resiliency }}ibmi-disaster-recovery)
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
