**[iSCSI VTL backups - IBM i]{.ul}**

# Table of Contents {#table-of-contents .TOC-Heading}

[Introduction 1](#_Toc125454662)

[Scope 2](#_Toc125454663)

[Prerequisites 2](#prerequisites)

[1. Overview of iSCSI VTL implementation on IBM i 2](#_Toc125454665)

[2. VTL - BRMS configuration and backups 2](#_Toc125454666)

[2.1 Setting up VTL in BRMS 2](#setting-up-vtl-in-brms)

[2.2 Using VTL in BRMS 7](#using-vtl-in-brms)

[2.3 Full system backups for migration
7](#full-system-backups-for-migration)

[3. Recovery to target LPAR using NFS 8](#_Toc125454670)

[3.1 NFS server steps 8](#nfs-server-steps)

[3.2 Setup LPAR for install 10](#setup-lpar-for-install)

[3.3 Perform scratch install 11](#perform-scratch-install)

[3.4 BRMS Recovery 15](#brms-recovery)

[]{#_Toc125454662 .anchor}Introduction\
VTLs are common in backup implementations in IBM i environments. The
features like flexibility, deduplication, compression, and replication
make them perfect for most backup environments.\
\
Most of the VTLs work using Fiber connection, but Power FC connections
are not allowed in all cloud providers due to architectural constraints.
Therefore, in cloud implementations, we must use iSCSI-based VTLs. A
limitation of iSCSI VTL is that it cannot take any backups which need a
server to be in restricted mode (Go Save 21,22,23/\*SYSTEM etc.) because
the server shuts down the TCP during these backups. As a workaround, you
can use specially created control groups which help in automating the
restricted backups on IBM i servers.\
\
This document overviews the iSCSI VTL implementation in Skytap IBM i
LPARs and the process to perform bare metal recovery (full system
backups and recovery to target LPAR).

[]{#_Toc125454663 .anchor}Scope

This document covers the following topics:

-   Overview of iSCSI VTL implementation on IBM i LPARs in Skytap

-   Configuration of control groups for restricted backups on iSCSI VTL

-   Steps to take backups

-   Recovery: Restore to NFS LPAR

-   Recovery: Installation of LIC and OS on target LPAR

-   Recovery: Restore of rest of the data on target LPAR

# Prerequisites 

-   On prem-server should be at the latest **Program Temporary Fix
    (PTFs)** level. The link below provides the minimum level of PTFs
    for each OS version:\
    [https://www.ibm.com/support/pages/system/files/inline-files/IBM i
    Support for iSCSI VTL
    1.4.pdf](https://www.ibm.com/support/pages/system/files/inline-files/IBM%20i%20Support%20for%20iSCSI%20VTL%201.4.pdf)

-   Source, target and NFS LPARs should be at a supported OS version

-   Required LPPs (MSE and BRMS) should be installed on the LPARs

1.  []{#_Toc125454665 .anchor}Overview of iSCSI VTL implementation on
    IBM i

> Once the iSCSI VTL is configured and attached to IBM i LPARs, it shows
> as a normal physical tape library with tapes in it for backup. From an
> IBM i OS perspective, once they are connected to the LPAR, there is no
> difference between a VTL and a physical tape library. The VTL can be
> used for BRMS or native IBM i backups.

2.  []{#_Toc125454666 .anchor}VTL - BRMS configuration and backups

## 2.1 Setting up VTL in BRMS

Below are the steps to configure iSCSI VTL on Power LPAR (UNIX):

1.  Install iSCSI VTL on UNIX on Power.

2.  Configure VTL and assign library and tapes to IBM i LPARs (source,
    NFS and Target).

3.  On the VTL Java console, go to "Virtual tape libraries" and click on
    "New".

> ![image001.png](I:\Repos\Skytap\WAF\resiliency\solutions\VTL\iscsivtlmedia/media/image1.png){width="4.951922572178478in"
> height="1.9209284776902886in"}

4.  After the library is created, you can create new tapes.

> ![image010.png](I:\Repos\Skytap\WAF\resiliency\solutions\VTL\iscsivtlmedia/media/image2.png){width="3.9230774278215224in"
> height="1.8518875765529308in"}
>
> Follow the wizard to create new tapes.

5.  On the IBM i LPARs, create an iSCSI entry using SQL, for example:

> CALL
> QSYS2.ADD_ISCSI_TARGET(TARGET_NAME=>\'iqn.2000-03.com.falconstor:stvtl1003a.vs.target\',
> TARGET_HOST_NAME=>\'10.0.1.6\',
> INITIATOR_NAME=>\'iqn.1994-05.com.ibm:stvtl1003a.vs.target\')
>
> (On IBM i LPAR) Start Service Tool => Hardware service manager =>
> Logical hardware services => System bus services => find "298A-001"
> Model => Opt (6) => IPL I/O processor

6.  (Java Console) Add a new iSCSI client:

> ![image002.png](I:\Repos\Skytap\WAF\resiliency\solutions\VTL\iscsivtlmedia/media/image3.png){width="2.990384951881015in"
> height="1.4833234908136483in"}![image003.png](I:\Repos\Skytap\WAF\resiliency\solutions\VTL\iscsivtlmedia/media/image4.png){width="4.940275590551181in"
> height="3.528846237970254in"}

7.  Select the Initiator Name you entered through the SQL.

> ![image004.png](I:\Repos\Skytap\WAF\resiliency\solutions\VTL\iscsivtlmedia/media/image5.png){width="5.111805555555556in"
> height="3.6309689413823274in"}
>
> ![image005.png](I:\Repos\Skytap\WAF\resiliency\solutions\VTL\iscsivtlmedia/media/image6.png){width="5.11215113735783in"
> height="3.634615048118985in"}

8.  Complete the wizard to create this client.

> ![image006.png](I:\Repos\Skytap\WAF\resiliency\solutions\VTL\iscsivtlmedia/media/image7.png){width="5.096153762029746in"
> height="3.657704505686789in"}

9.  Create a target for this client.

> ![image007.png](I:\Repos\Skytap\WAF\resiliency\solutions\VTL\iscsivtlmedia/media/image8.png){width="3.875in"
> height="1.9532709973753282in"}

10. Enter the target name to be exactly the same as in the SQL
    statement.

> ![image008.png](I:\Repos\Skytap\WAF\resiliency\solutions\VTL\iscsivtlmedia/media/image9.png){width="4.875in"
> height="3.494109798775153in"}

11. Click on Next and Finish.

12. Assign the virtual library to this iSCSI target:

> ![image012.png](I:\Repos\Skytap\WAF\resiliency\solutions\VTL\iscsivtlmedia/media/image10.png){width="4.875in"
> height="2.307247375328084in"}

Choose the library in the assignment and follow the wizard to the end.

>  
>
> (On IBM i LPAR) Repeat steps. Start Service Tool => Hardware service
> manager => Logical hardware services => System bus services => find
> "298A-001" Model => Opt (6) => IPL I/O processor

13.   (IBM i) wrkmlbsts, you should be able to see the new media library
    shows up.

    2.  ##  Using VTL in BRMS

Once the VTL is configured on the LPAR and as visible in the WRKMLBSTS
command, it is ready to perform native backups. To use the VTL for BRMS,
especially for full system backups, perform the following steps:

1.  INZBRM \*DEVICE\
    To add this device in BRMS, create the media class, media policies
    (move and media) add backup tapes etc., as per your requirements.

2.  INZBRM \*NFSCTLG\
    The above command will create 4 control groups:

-   QNFSIPLFUL: Full OS+ backup on Optical

-   QNFSSYSFUL: Full system and user backup on VTL tape

-   QNFSIPLINC: Cumulative OS+ backups on optical

-   QNFSSYSINC: Cumulative System and user backup on VTL

3.  Assign the media policy you created for VTL to QNFSSYSFUL and
    QNFSSYSINC control groups. Do not change the media policy for
    QNFSIPLFUL and QNFSIPLINC, because, for these two control groups,
    the data will be written first on an image catalogue and then
    automatically to VTL tapes using the media policy of QNFSSYSFUL and
    QNFSSYSINC media policy details. Below is a screenshot of how these
    control groups should look:\
    \
    \
    ![](I:\Repos\Skytap\WAF\resiliency\solutions\VTL\iscsivtlmedia/media/image11.png){width="6.263888888888889in"
    height="0.6104166666666667in"}\
    \
    At this stage, you are ready to use the VTL as a normal tape library
    for your weekly/daily backups, or as a full system backup for
    migration.

## 2.3 Full system backups for migration

To perform a full system backup for migration, perform the below steps
on the source LPAR.

1.  STRBKUBRM CTLGRP(QNFSSYSFUL) SBMJOB(\*NO)\
    This backup will be taken on VTL tape.

2.  STRBKUBRM CTLGRP(QNFSIPLFUL) SBMJOB(\*NO) OMITS(\*IGNORE)\
    This backup will be run in restricted mode. This will run OS+ backup
    on an image catalogue -\> verify the image catalogue -\> copy the
    image catalogue data to VTL tape using the media policy of the
    QNFSSYSFUL control group.

3.  Verify that both backups control groups have been completed
    successfully.

4.  Print the recovery report by running the STRMNTBRM command.

5.  Mail this report out of the server and at this point, you can shut
    down the source LPAR.

```{=html}
<!-- -->
```
3.  []{#_Toc125454670 .anchor}Recovery to target LPAR using NFS

> On Skytap, you need to create 2 LPARs (NFS and Target) and assign VTL
> to both. All the steps we are going to follow now will be in the
> system recovery report generated in the last step. Below explains the
> steps for better understanding.

## 3.1 NFS server steps

Perform the below steps on the NFS LPAR to perform LIC and OS restore on
target LPAR using NFS.

1.  Ensure that the VTL is visible on the NFS LPAR.

2.  Move the tape on which the backup of the source server was done to
    the NFS server's VTL.

> Highlight the tape you want to move.
>
> ![image011.png](I:\Repos\Skytap\WAF\resiliency\solutions\VTL\iscsivtlmedia/media/image12.png){width="4.75in"
> height="1.5861417322834646in"}
>
> Click on "Move to Vault."
>
> Find the tape in Vault and Client on "Move to Virtual Library" then
> follow the GUI and choose the library you want the tape to move to.
>
>  
>
> ![image013.png](I:\Repos\Skytap\WAF\resiliency\solutions\VTL\iscsivtlmedia/media/image13.png){width="4.8557688101487315in"
> height="2.3465955818022746in"}
>
> ![image009.png](I:\Repos\Skytap\WAF\resiliency\solutions\VTL\iscsivtlmedia/media/image14.png){width="4.9504079177602796in"
> height="3.479005905511811in"}

3.  INZBRM \*DEVICE\
    To add this device to BRMS.

4.  Restore the \*LINK information from the backups tape, restoring the
    data required for LIC and OS installation. The details will be in
    Step 1 of the recovery report. Below is an example:

![](I:\Repos\Skytap\WAF\resiliency\solutions\VTL\iscsivtlmedia/media/image15.png){width="6.263888888888889in"
height="0.7534722222222222in"}

5.  Run commands in the recovery report to create an image catalogue,
    below are examples:

> ![Text Description automatically
> generated](I:\Repos\Skytap\WAF\resiliency\solutions\VTL\iscsivtlmedia/media/image16.png){width="6.263888888888889in"
> height="0.8118055555555556in"}

6.  Verify the image catalogue with options TYPE(\*LIC) NFSSHR(\*YES).

7.  Set up NFS export options.

8.  Run the below commands to allow the target server to read from
    install files using NFS:

-   STRNFSSVR \*ALL

-   CHGNFSEXP OPTIONS('-i -o ro')
    DIR('/QIBM/UserData/BRMS/cloud/Q1ACQ20571')

-   CHGAUT OBJ('/QIBM/UserData/BRMS/cloud/Q1ACQ20571') USER(\*PUBLIC)
    DTAAUT(\*RX) SUBTREE(\*ALL)

-   CHGTFTPA AUTOSTART(\*YES)
    ALTSRCDIR('/QIBM/UserData/BRMS/cloud/Q1ACQ20571')

-   CHGAUT OBJ('/QIBM/UserData/BRMS/cloud/Q1ACQ20571') USER(QTFTP)
    DTAAUT(\*RX) SUBTREE(\*ALL)

-   ENDTCPSVR SERVER(\*TFTP)

-   STRTCPSVR SERVER(\*TFTP)

The NFS LPAR is now ready for the install.

## 3.2 Setup LPAR for install

> On the target LPAR, you must create a LAN console which can scratch
> install the LIC and OS on the target LPAR using the NFS LPARs image
> catalogue as the source.

1.  Start the target LPAR.

2.  Vary off the interactive interface (\*IP4DHCP).

![](I:\Repos\Skytap\WAF\resiliency\solutions\VTL\iscsivtlmedia/media/image17.png){width="6.263888888888889in"
height="0.6347222222222222in"}

3.  Note down the cmn resource for the Ethernet port available on the
    > server, normally it\'s CMN03 WRKHDWRSC \*CMN.

4.  Vary off the LIND

> VRYCFG CFGOBJ(ETHLINE) CFGTYPE(\*LIN) STATUS(\*OFF).

5.  Set up the LAN console in SST by performing below steps:

> STRSST -\> option 8-\> F13 (select LAN adapter) -\> option 1

6.  Specify the IP details of the LPAR.

![Graphical user interface, text Description automatically
generated](I:\Repos\Skytap\WAF\resiliency\solutions\VTL\iscsivtlmedia/media/image18.png){width="6.263888888888889in"
height="3.640277777777778in"}

7.  Press F7-\> F13-\> F14 -\> F3 out of the SST.

8.  Verify that port 3000 is in listen status.

> NETSTAT \*CNN -\> F14-\> search for port 3000

9.  Create an optical device on target LPAR pointing to the NFS LPAR
    > (10.0.0.2).

CRTDEVOPT DEVD(optvrt01) RSRCNAME(\*VRT) LCLINTNETA(\*SRVLAN)
RMTINTNETA(10.0.0.2) NETIMGDIR('/QIBM/UserData/BRMS/cloud/Q1ACQ20571')

10. VRYCFG CFGOBJ(OPTVRT01) CFGTYPE(DEV) STATUS(\*ON).

11. WRKIMGCLGE IMGCLG(\*DEV) DEV(OPTVRT01).

The above command should show the .iso file on the NFS LPAR.

## 3.3 Perform scratch install 

Perform the below steps to install LIC, and OS on the target LPAR:

1.  STRNETINS with the below parameters:

![Graphical user interface Description automatically
generated](I:\Repos\Skytap\WAF\resiliency\solutions\VTL\iscsivtlmedia/media/image19.png){width="6.263888888888889in"
height="1.6388888888888888in"}

> It will take some time and will show you the "Install LIC Screen."

2.  Proceed with LIC installation by taking Option 1.

> ![Text Description automatically
> generated](I:\Repos\Skytap\WAF\resiliency\solutions\VTL\iscsivtlmedia/media/image20.png){width="6.263888888888889in"
> height="3.526388888888889in"}

![Graphical user interface, text Description automatically
generated](I:\Repos\Skytap\WAF\resiliency\solutions\VTL\iscsivtlmedia/media/image21.png){width="6.263888888888889in"
height="3.7368055555555557in"}

3.  Choose Option 2 to Install LIC and Initialize the system (on the
    target system that we will be building from the scratch).

![Graphical user interface, text Description automatically generated
with medium
confidence](I:\Repos\Skytap\WAF\resiliency\solutions\VTL\iscsivtlmedia/media/image22.png){width="6.263888888888889in"
height="1.4979166666666666in"}

4.  Press F10 to continue.

5.  Post LIC Installation, do Disk Initialization if you have more than
    1 LUN assigned on target LPAR.

6.  Install OS using the below option (Select Option 2- Install the
    operating system):

![A screenshot of a computer Description automatically generated with
medium
confidence](I:\Repos\Skytap\WAF\resiliency\solutions\VTL\iscsivtlmedia/media/image23.png){width="6.263888888888889in"
height="2.872916666666667in"}

7.  Log in using **QSECOFR**.

8.  Enable automatic configuration -- Y on the set major system option
    screen.

![Graphical user interface Description automatically generated with low
confidence](I:\Repos\Skytap\WAF\resiliency\solutions\VTL\iscsivtlmedia/media/image24.png){width="6.263888888888889in"
height="1.4909722222222221in"}

9.  Change the following on the next screen:

-   Set date and time

-   Choose System Time Zone

-   Start print writers = N

-   Start system to restricted state = Y

-   Set major system options = Y

-   Define or change the system at IPL = Y

![Text Description automatically
generated](I:\Repos\Skytap\WAF\resiliency\solutions\VTL\iscsivtlmedia/media/image25.png){width="6.263888888888889in"
height="2.948611111111111in"}

10. Press Enter to continue. 

11. Select Option 3 twice (Select System Value Commands > Work with
    System Values) to change the following system values:

-   QALWOBJRST     \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_   
    > \*ALL                           

-   QFRCCVNRST     \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_   
    > 0                              

-   QINACTITV      \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_    \*NONE          

-   QJOBMSGQFL     \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_   
    > \*PRTWRAP                       

-   QJOBMSGQMX     \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_    30 (minimum, 64
    > recommended)   

-   QLMTDEVSSN     \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_    0          

-   QLMTSECOFR     \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_    0          

-   QMAXSIGN       \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_    \*NOMAX     

-   QPFRADJ        \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_    2          

-   QPWDEXPITV     \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_    \*NOMAX     

-   QSCANFSCTL     \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_    \*NOPOSTRST 

-   QVFYOBJRST     \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_    1      

12. Press F3 twice and change the **QSECOFR** password.** **

*Note: the current QSECOFR password will be QSECOFR (capital letters).*

## 3.4 BRMS Recovery

At this stage, the LIC and OS are installed on the target LPAR and you
can proceed by taking these next steps in the recovery report.

1.  Recreate the virtual optical device on the target LPAR:

> CRTDEVOPT DEVD(OPTVRT01) RSRCNAME(\*VRT) LCLINTNETA(\*SRVLAN)
> RMTINTNETA(10.0.0.2)
> NETIMGDIR(\'/QIBM/UserData/BRMS/cloud/Q1ACQ20571\').

2.  Perform steps specified in the recovery report Step 5 (recover the
    BRMS product and libraries).

3.  Skip Step 6 in the recovery report.

4.  Perform Steps 7 to 14 in the recovery report, skipping Step 8).

5.  Save the QSQL library from NFS/ source LPAR, FTP it to target LPAR
    and restore it to target LPAR.

6.  Configure and assign VTL to the target LPAR and move the backup
    tapes to it.

7.  Perform Steps 16 to 32, skipping Steps 17, 23,24,25 and 28.

8.  After IPL, configure the IP on LPAR as per the available CMN
    resource.

At this stage, the full system restore of the LPAR is completed and the
LPAR can be released for user testing.
