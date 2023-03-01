**[DSI Setup]{.ul}**

**[Overview:]{.ul}**

DSI is an iSCSI VTL which provides high performance backup/restore
functionality in a cloud environment. Like other VTLs, DSI is a
disk-based backup solution which emulates physical tape. Since VTL
technology uses disks to back up data, it eliminates the media and
mechanical errors that can occur with physical tapes and drives.

**[Pre-requisites:]{.ul}**

IBM i LPARs should be upgraded to the latest PTFs prior to VTL
installation.

Skytap has prebuilt templates that can be used to configure the DSI VTL
in a Skytap environment.

**[Installation:]{.ul}**

IBM i can be configured using SQL services or advanced analysis macro
from the service tool. SQL services is the preferred method and the
steps are documented below:

1.  From your IBM i Secure Remote Access (SRA) console in Skytap use the
    following commands

\> Strsql

\> Select \* from iscsi_info

Note: This command describes iSCSI initiator on IBM i, it should be
empty as shown in the below screenshot:
<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/VTL/dsiiscsivtlmedia/
<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/VTL/dsiiscsivtlmedia/image1.png">{width="6.268055555555556in"
height="0.8236111111111111in"}

2.  Next, create the initiator on IBM i using the command in the below
    image where Target Name will be the target VTL name. The IP of the
    VTL also needs to be mentioned as shown below:

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/VTL/dsiiscsivtlmedia/image2.png">{width="6.268055555555556in"
height="0.31180555555555556in"}

3.  Then, get the initiator name to add in DSI GUI as shown below:

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/VTL/dsiiscsivtlmedia/image3.png">{width="3.9753444881889766in"
height="0.35003062117235345in"}

4.  Login to the VTL GUI and add the license keys from the configuration
    wizard:

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/VTL/dsiiscsivtlmedia/image4.png">{width="3.283617672790901in"
height="2.1585203412073493in"}

5.  Add IBM i client using the step below:

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/VTL/dsiiscsivtlmedia/image5.png">{width="2.7335706474190724in"
height="2.091848206474191in"}

6.  IPL the storage IOP from IBM i SST. The IOP resource name can be
    found from WRKHDWRSC \*STG with type 298A. Once the IOP is IPLed,
    the IBM i IP will reflect as shown below and the client can be
    added.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/VTL/dsiiscsivtlmedia/image6.png">{width="6.268055555555556in"
height="3.7576388888888888in"}

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/VTL/dsiiscsivtlmedia/image7.png">{width="6.268055555555556in"
height="4.2187314085739285in"}

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/VTL/dsiiscsivtlmedia/image8.png">{width="6.268055555555556in"
height="2.008953412073491in"}

7.  Assign the VTL to the IBM i using the step below:

> <img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/VTL/dsiiscsivtlmedia/image9.png">{width="3.475300743657043in"
> height="1.3167804024496939in"}
>
> <img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/VTL/dsiiscsivtlmedia/image10.png">{width="6.200536964129483in"
> height="4.43371719160105in"}

8.  Now the client will have the VTL assigned and in the IBM i OS, you
    can check in WRKHDWRSC \*STG, that the storage IOP has the VTL
    assigned. If not, IPL the IOP again.

> Note: It may take around 5 minutes for the VTL resources to show up in
> IBM i. The same can be checked using WRKMLBSTS as well.

**[Backup:]{.ul}**

1.  INZBRM \*Device to add the VTL in BRMS

2.  You can create a new media class or edit the existing one with
    command WRKCLSBRM

> <img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/VTL/dsiiscsivtlmedia/image11.png">{width="6.268055555555556in"
> height="2.5215277777777776in"}

3.  Change media policy to use the media class: GO BRMS, Option 11,
    Option 7

4.  Edit the control group and add the media policy, backup device

5.  Add the media (WRKMEDBRM) in BRMS or by using the below command:

> <img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/VTL/dsiiscsivtlmedia/image12.png">{width="6.268055555555556in"
> height="0.3145833333333333in"}

6.  Start backup using STRBKUBRM

7.  Check the logs using DSPLOGBRM once the backup is completed

**[Note]{.ul}**: For automated weekly full system backups use control
groups QNFSSYSFUL and QNFSIPLFUL. These backup tapes can also be used
for bare metal recovery by using an NFS server and BRMS recovery report.
These control groups can be added using INZBRM \*NFSCTLG.

For bare metal recovery, you need to restore the QNFSIPLFUL backup on an
IBM i LPAR capable of hosting the image as NFS mount. Once the LIC, OS
are restored with TCP/IP started, the VTL can be used to restore the
user data by following the BRMS recovery report.
