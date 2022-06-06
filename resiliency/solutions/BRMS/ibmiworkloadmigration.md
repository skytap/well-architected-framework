---
Title: IBM i workload migration from on-prem to Skytap using BRMS ICC
Description: Skytap Cold Migration Solution - IBM i workload migration from on-premises to Skytap using BRMS ICC
Authors: Tony Perez - Sales Engineer, Mayank Kumar - Cloud Solutions Architect, Matthew Romero, Technical Product Marketing Manager
permalink: /resiliency/solutions/brms
---
# IBM i workload migration from on-premises to Skytap using BRMS ICC

This guide is provided “as-is”. Information and views expressed in this document, including URL and other Internet website references, may change without notice and usage of the included material assumes this risk.

This document does not provide you with any legal rights to any intellectual property in any product. You may copy and use this document for your internal, reference purposes.

# Table of Contents <a name="toc"></a>

* [Key takeaways](#takeaways)
* [Before you begin](#begin)
* [Full system backup of IBM i source using BRMS ICC](#start)
* [Create ICC FTP resource](#ftpresourcecreate)
* [Run the primary backup from console](#primarybackupcreate)
* [Run the second backup from console](#secondbackupcreate)
* [Change BRMS Control Group QCLDBIPL01](#changecontrolgroup)
* [Transfer backup media to ICC FTP resource](#transferbackuptoiccftp)
* [Generate BRMS recovery report](#generatebrmsreport)
* [Copy backup media from ICC FTP resource to IBM i LPAR on cloud](#movebackuptocloud)
* [IBM i recovery from IBM i NFS server](#recoveryfromibminfs)
* [Configure IBM i client server (target LPAR)](#configuretargetlpar)
* [Set System date, time, and time zone same as current [Source System]](#settimesource)
* [Recover the BRMS libraries on target system](#recovertotarget)

## Key Takeaways <a name="takeaways"></a>

Here are key takeaways for developers and operators to consider from this guide:

* Demonstrate how an IBM i workload can be migrated to Skytap on Azure using BRMS ICC. In this document you will learn how to take backup on IBM i using BRMS ICC and transfer it to cloud using FTP.

 * Full-system recovery in the cloud using IBM i as NFS server to an IBM i
     VM as target system.

## Before you begin <a name="begin"></a>

The below LPPs need to be installed on IBM i LPAR:

* 5770-SS1 Option 18: Media and Storage Extensions

* 5770-SS1 Option 44: Encrypted Backup Enablement (Optional)

* 5770-BR1 \*BASE

* 5770-BR1 Option 1: Network feature (Optional)

* 5770-BR1 Option 2: Advanced Functions feature (Optional)

* 5733ICC \*BASE IBM Cloud Storage Solutions for i

* 5733ICC Option 1: Cloud Storage

* 5733ICC Option 2: Advanced

* 5733ICC Option 3: Reserved -- Option 3,4,5,6,7\

* Install latest PTFs on IBM i with below individual PTFs installed

  * <a href="https://cloud.ibm.com/docs/power-iaas?topic=power-iaas-minimum-levels" target="_blank">Minimum</a>
 PTF level for IBM i

* Utilized ASP should be less than 48% in IBM i LPAR

###### *[Back to the Top](#toc)*

# Full system backup of IBM i source using BRMS ICC <a name="start"></a>

## Create ICC FTP resource <a name="ftpresourcecreate"></a>

Create ICC FTP resource where IBM i backup will be transferred. FTP resource can be IBM i or Linux system. For this document, we will be using Linux in our examples.

In the case of large data, it is recommended to use Linux system On-Prem and transfer the data to Linux system on cloud using AZCopy.

***NOTE***: *If you have 5 disk arms on-prem with xyz storage, then re-create that setup in your Skytap LPAR.*

1. Create FTP resource using the below command: 

```
===> CRTFPRICC
```
The root directory is the directory in the FTP server where we want to transfer the data and the resource URI will be IP of FTP server.

Example: *\<FTP server IP>:\<port number>./\<root directory>*

![](media/image2.png)
![](media/image12.png)

Once completed, you should see: "Resource "TEST1" was created for resource type FTP".

###### *[Back to the Top](#toc)*
## Create the backup control groups <a name="controlgroupscreate"></a>

1. Once the resource is created, run the following command:

```
===> INZBRM \*DATA\
```
    
Once you press \<ENTER\> wait for the command to complete, there is no confirmation message that is displayed. 
This command will automatically create the below control groups for the Test1 resource created in the previous step.

* QCLDBGRP01

* QCLDBIPL01

* QCLDBSYS01

* QCLDBUSR01


2. Put the system in restricted state using the below command:

```
===> ENDSBS \*ALL \*IMMED\
```

Wait for the message: "System ended to restricted condition."

or use command:
```
===> WRKSBS
```

And refresh until you see it has completed.

3. Change subsystems to process for Control Group QCLDBSYS01 using the following commands:
```
===> WRKCTLGBRM
```
4. Take Option 9 in front of QCLDBSYS01.
5. Enter the values matching the screen below, make sure to put "\*NO\" for restart. 
6. Press \<ENTER\> once you have all the values filled in. 
7. Press F3 to exit.
8. Change Restart to "\*NO for Seq 10 Subsystem \*ALL".

![](media/image1.png)

###### *[Back to the Top](#toc)*
## Run the primary backup from console <a name="primarybackupcreate"></a>
1. Start backup using below command:
```
===> STRBKUBRM CTLGRP(QCLDBSYS01) SBMJOB(\*NO)**
```

2. Check for error messages once the backup is complete.

***NOTE***: *You will see messages similar to: "1 of 120 libraries processed....84 objects saved."*

***NOTE***: *The backup will take a while to complete.*

***NOTE***: *This first backup command will save USER data to virtual tape.*

***NOTE***: *When the processing is finished you will see a message similar to: "Control group QCLDBSYS01 type \*BKU processing is complete."*

###### *[Back to the Top](#toc)*
## Change BRMS Control Group QCLDBIPL01 <a name="changecontrolgroup"></a>

1. Change BRMS Control Group QCLDBIPL01 using command:
```
===\> **WRKCTLGBRM\
```
2. Select *QCLDBIPL01* 
3. Select *Option 8=Change attributes* 
4. Page down once, change *Automatically backup media information* to \*LIB
5. Append to media to \*NO.\

![](media/image6.png)

6. Select Option 9=Subsystems to process
7. Change Restart to \*YES for Seq 10 Subsystem \*ALL \

###### *[Back to the Top](#toc)*
## Run the second backup from console <a name="secondbackupcreate"></a>

1. Run the second backup from console:

```
===> STRBKUBRM CTLGRP(QCLDBIPL01) SBMJOB(\*NO)\
```
***NOTE***: *The second backup command saves SYSTEM DATA to optical media.*

You should see a message similar to:  \"STRTCP Started for QSECOFR....\"

***NOTE***: *QCLDBIPL01 and QCLDBSYS01 control groups are used for entire system backup, and QCLDBUSR01 and QCLDBGRP01 are used for incremental backups.*

2. Once the backups are complete check for the media using command:
```
===> WRKMEDBRM.
```
3. Take Option 6 in front of any one of them. 
This will show the medias used for backup and the order as well in which they are needed to add them in the catalog in the NFS server.

###### *[Back to the Top](#toc)*
## Transfer backup media to ICC FTP resource <a name="transferbackuptoiccftp"></a>

1. Start FTP server using command:
```
===> STRTCPSVR \*FTP\
```

It may be already started, output will be: \"FTP Server Starting\"

2. Start QICC subsystem using command:
```
===> STRSBS QICC/QICCSBS
```
3. Move the media to FTP resource using command:
```
===> WRKMEDBRM
```

4. Find all the media objects that have the \"+\" (plus sign) next to them. They should end with \"OPT\" and \"TAP. 
5. Put an \"8\" (move) next to all of them with the plus sign. 

You might have to page down to find all of them.
 
6. Press \<ENTER\> after putting 8 next to
     each entry.

You should see a screen similar to this:

![](media/image14.png)

7. Change \"Storage location\" to \"TEST1\" that was created earlier:

![](media/image13.png)

8. And press \<ENTER\>


***NOTE***: *QCLDBIPL01 will use virtual optical media and QCLDBSYS01 will use virtual tape media.*

9. After moving the media, check the status of the transfer using the below command:

```
===> WRKSTSICC Status(\*All)
```

You will see the Volume Name, Status, and Complete % for each file
transfer. Wait until all Volumes have Successfully been completed to
proceed to the next step.

###### *[Back to the Top](#toc)*
## Generate BRMS recovery report <a name="generatebrmsreport"></a>

1. Generate BRMS recovery report using below command:

```
===>  **STRRCYBRM OPTION(\*CTLGRP) ACTION(\*REPORT) CTLGRP((QCLDBSYS01 1)(QCLDBIPL01 2))**
```
###### *[Back to the Top](#toc)*
## Copy backup media from ICC FTP resource to IBM i LPAR on cloud <a name="movebackuptocloud"></a>
(This step can be skipped if backup is being transferred to IBM i server in 4^th^ step)

This IBM i LPAR will be used as NFS server to build the target IBM i
LPAR

1. Create a directory in IFS where you want to copy the media from FTP server using the below command:

```
MKDIR '/XX', where xx is the directory name
```     
2. Start FTP server and QICC subsystem if not already started using the below commands:
```
STRTCPSVR \*FTP
```
```
STRSBS QICC/QICCSBS
```
3.  Copy the media from FTP server to IBM i LPAR using the below command, where LOCALFILE will be the directory created in previous step/media volume name and CLOUDFILE will be the file name FTP server with syntax QBRMS\_xx.
```
CPYFRMCLD RESOURCE(Test) Async(\*No) CLOUDFILE(\'QBRMS\_IBMI74/QO3911\')LOCALFILE(\"/xx/QO3911\')
```
###### *[Back to the Top](#toc)*
## IBM i recovery from IBM i NFS server <a name="recoveryfromibminfs"></a>

1. Create image catalog and add media entry to image catalog using
     below commands in IBM i NFS server:
```
> CRTDEVOPT DEVD(INSTALL) RSRCNAME(\*VRT)
```
2. VARY ON the Optical drive
```
>   CRTIMGCLG IMGCLG(XXX) DIR(\'/XX\') TYPE(\*OPT) CRTDIR(\*YES)
```
```
>   ADDIMGCLGE IMGCLG(XXX) FROMFILE('media name') TOFILE('media name')
```
3. Load image catalog to Optical drive

4. Verify image catalog with verify type as \*LIC and NFSSHR as \*YES

5. Start the NFS server and change NFS export options using below
    commands:
```
>   STRNFSSVR SERVER(\*All)
```
```
>   CHGNFSEXP OPTIONS('-i -o ro') DIR('/xx)
```
![](media/image8.png)

6. Change object authorities and TFTP attributes:

```
> CHGAUT OBJ(\'/xx\') USER(\*PUBLIC) DTAAUT(\*RWX) SUBTREE(\*ALL)
```
```
> CHGTFTPA AUTOSTART(\*YES) ALTSRCDIR(\'/xx\')
```
```
> CHGAUT OBJ(\'/xx\') USER(QTFTP) DTAAUT(\*RX) SUBTREE(\*ALL)
```
7. Restart the TFTP server:
```
> ENDTCPSVR SERVER(\*TFTP)
```
```
> STRTCPSVR SERVER(\*TFTP)
```
###### *[Back to the Top](#toc)*
## Configure IBM i client server (target LPAR)<a name="configuretargetlpar"></a>

1. Login to SST and select Option 8 -- Work with service tools User IDs
     and Devices

2. Press F13 and select the LAN adapter

***NOTE***: *The LAN adapter needs to be on the same VLAN as IBM i NFS server*

3. Configure service tools LAN adapter:
  *  IP version allowed = IPV4
  *  Internet address = you can use the same IP of the client (TARGET)
  *   Gateway router address = use the same gateway on the client
  *  Subnet mask = use the same
  *  Press F7=Store
  *  Press F13=Deactivate
  *  Press F14=Activate
  
4.  Verify port 3000 status as active using NETSTAT \*CNN and use F14 to
    display port numbers

5. Create client server optical device
  *  CRTDEVOPT, Press F4 to prompt
  *  Local internet address = \*SRVLAN
  *  Remote internet address = the IP address of the IBM i VSI NFS Server
  *  Network image directory = '/xx'

![](media/image10.png)

6. Vary on the optical device created in previous step and verify you can access the remote image catalog using WRKIMGCLGE IMGCLG(\*DEV) DEV(Opt dev)

![](media/image4.png)

7. Start network install to begin scratch installation starting from LIC
     installation. Prior to installation, make sure to document all
     current network information, same may be needed after restoration.

8. Start network install:
  *  STRNETINS press F4 to prompt
  *  Network optical device = Optical Device
  *  Installation option = \*LIC
  *  Keylock mode = MANUAL

![](media/image9.png)

9. On Install LIC screen, take Option 2 to Install LIC and Initialize system

![](media/image11.png)

10. Post LIC installation, do disk configuration

11. Initialize non configured disks and add to ASP

12. Install OS using below option:

![](media/image5.png)

13. On Install device type, Select Option-2 -- Network device

14. Configure the network device:
  *  Server IP = the IP address of the SOURCE NFS IBM i VSI
  *  Path Name = the name of the Directory where the image volumes are located
  *  Press F10 = Continue

15. Press Enter twice to confirm and on Install the OS screen, take Install Option as 2

![](media/image3.png)

17. Select Option 1=Restore programs and language objects from the current media set

18. Select Option 2=Keep for Job and output queues

19. Select Option 1=Yes for distribute operating system and on available disk units

20. Change system information -- 1 to restore

21. Change following on next screen:
  *  Start print writers = N
  *  Start system to restricted state = Y
  *  Set major system options = Y
  *  Define or change system at IPL = Y
###### *[Back to the Top](#toc)*
## Set System date, time, and time zone same as current [Source System]<a name="settimesource"></a>

1. Enable automatic configuration -- Y

![](media/image7.png)

2. Select Option 3 twice to change the following system values:

```
>  QALWOBJRST \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ \*ALL

>   QFRCCVNRST \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ 0

>   QINACTITV \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ \*NONE

>   QJOBMSGQFL \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ \*PRTWRAP

>   QJOBMSGQMX \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ 30 
```
(30 minimum, 64 recommended)
```
> QLMTDEVSSN \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ 0

>   QLMTSECOFR \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ 0

>   QMAXSIGN \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ \*NOMAX

>   QPFRADJ \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ 2

>   QPWDEXPITV \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ \*NOMAX

>   QSCANFSCTL \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ \*NOPOSTRST

>   QVFYOBJRST \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ 1
```

3. Press F3 twice and change the QSECOFR password.

***NOTE***: *The current QSECOFR password will be QSECOFR (capital letters)*

###### *[Back to the Top](#toc)*
## Recover the BRMS libraries on target system<a name="recovertotarget"></a>
1. CRTDEVOPT, Press F4
2. Local internet address = \*SRVLAN
3. Remote internet address = the ip address of the IBM i NFS Server
4. Network image directory = '/xx'
5. Vary ON the device
6. Verify you can access the image catalog:
```
> WRKIMGCLGE IMGCLG(\*DEV) DEV(INSTALL)

> CHGMSGQ MSGQ(QSYSOPR) DLVRY(\*NOTIFY) SEV(99)
```

7. Recover BRMS libraries, follow the [BRMS recovery report](#generatebrmsreport)
8. RSTLIB SAVLIB(saved-item) DEV(optical device) VOL(media name)
     OPTFILE(\'\')
9. Recover the BRMS related media information using the recovery steps in the BRMS recovery report:
```
>   RSTOBJ OBJ(\*ALL) SAVLIB(saved-item) DEV(device-name) VOL(media name) OPTFILE(\'\') MBROPT(\*ALL) ALWOBJDIF(\*COMPATIBLE)
>   INZBRM OPTION(\*SETAUT)
>   SETUSRBRM USER(QSECOFR) USAGE(\*ADMIN)
>   INZBRM OPTION(\*DEVICE)
```

10. Verify installation device using WRKDEVBRM

11. Follow step010 in the recovery report to recover user profiles

***NOTE***: *TCP/IP must be started so BRMS can transfer media required by a
recovery from the cloud.*

12a To continue the recovery in restricted state, run the
following commands:
```
> STRTCP STRSVR(\*NO) STRIFC(\*NO) STRPTPPRF(\*NO) STRIP6(\*YES) STRTCPIFC
INTNETADR(\'nnn.nnn.nnn.nnn\')
```
Where 'nnn' is IP of recovery system

12b. Otherwise, Start all subsystems using 
```
> STRSBS QCTL, STRTCP
```

13. Run the following to setup Cloud volumes:
```
  >   CALL QBRM/Q1AOLD PARM(\'CLOUD \' \'FIXDRVOL ' \'xxxxx1\' \'xxxxx2\' \'xxxxx3\' \'xxxxxn\')
```
14. Volume names can be found from BRMS report file name QP1A2RCY
```
>  STRRCYBRM OPTION(\*SYSTEM) ACTION(\*RESTORE)
```

***NOTE***: *Press F9 on the Select Recovery Items display to go to the Restore Command Defaults display.*

15. Ensure the tape device name or media library device name is correct
     for the Device prompt

16. Ensure \*SAVLIB is specified for the Restore to library prompt

17. Ensure \*SAVASP is specified for the Auxiliary storage pool prompt

18. ENSURE \*YES is specified for create parent directories

19. If you are recovering to a different system or a different logical
partition, you must specify the following:
*  \*ALL for the Database member option prompt
*  \*COMPATIBLE for the Allow object differences prompt
*  \*NONE for the System resource management prompt

20. Press \"Enter\" to return to the Select Recovery Items display

21. Restore \*SAVSECDTA which will restore user profiles. Change QSECOFR
     password using the below command:
```
   >   CHGUSRPRF USRPRF(QSECOFR) PASSWORD(new-password)
```
22. Recover configuration data(\*Savcfg) and required system libraries

23. Do network configuration and add IP in the target system

24. Ping the cloud resource URI to verify a working network to cloud
     storage

25. Create Virtual tape device and Vary ON the same:
```
    >  CRTDEVTAP DEVD(TOR1CLDTAP) RSRCNAME(\*VRT)
```
26. Move the media to \*Home location

27. Recover User libraries, DLO, IFS as per the BRMS recovery report
```
> UPDPTFINF

>   RSTAUT \*All

>   DSPJOBLOG Output(\*Print)
```
28. Change the system values as required and do a Normal IPL of the
    system.

## Next Steps

**Main Overview**
> [Skytap Well-Architected Framework](../../../)

**Operational Excellence**
>[Skytap Operational Excellence Pillar](../../../operations/)

**Resiliency**
>[Skytap Resiliency Pillar](../../)
>* [Migration](../../migrations)
>* [Protection](../../backups)
>* [Disaster Recovery](../../disaster-recovery)
>* [High Availability](../../ibmi-disaster-recovery)
>
>**Migration Solutions**
>* [Hot Migrations (Replication Sync)](../hot-migrations)
>* [Cold (Warm) Migrations (Backup and Restore)](../cold-migrations)
>
>**Design**
>* [Design Considerations for Azure](../../design-considerations-azure)
>* [Design Considerations for IBM Cloud](../../design-considerations-ibm)


**Security**
> * [Skytap Security Pillar](../../../security/)
