This is the second part to my previous article:\
[[https://www.linkedin.com/pulse/ibm-i-restore-your-lpar-from-nfs-diego-kesselman/]{.underline}](https://www.linkedin.com/pulse/ibm-i-restore-your-lpar-from-nfs-diego-kesselman/)

[**[IBM i - Backup your LPAR to NFS without using its own storage]{.underline}**](https://bit.ly/3d01iY4)
---------------------------------------------------------------------------------------------------------

When performing backup I was assuming you\'ll use a Linux as your NFS
server. Why? It\'s fast, cheap, works perfect with (IBM) cloud, you can
build your own even on a Raspberry Pi, mini-PC as Intel NUC and use
inexpensive SSDs to capture save your data.

You have different options when restoring:

-   Restore data from your running OS

-   Full system restore from BRMS or Option 21

Trying to continue with my \"IBM i - Backup your LPAR to NFS without
using its own storage\" article I\'ll choose the Full System Restore
option.

NOTE: Restoring specific data can be performed using this method or
using the same server from backup. Restoring the full system, including
\"License Machine Code\" (LIC) requires more steps and resources.

### **BACKUP & RESTORE from NFS diagram**

![No alt text provided for this image](./media/image3.jpg){width="6.5in"
height="3.6527777777777777in"}

### **REQUIREMENT**

We have some requirements to perform a full system restore, because we
need to start our system from \"A\" or \"B\" side (not \"D\" IPL) and
point to an NFS server to load our LIC :

-   One TEMPORARY system (or LPAR) to mount the images running IBM i
    > V7R2, V7R3 or V7R4 (think this can run on previous releases, but
    > never test that options and I\'m sure you\'ll need some PTFs).

-   Latest PTFs on TEMPORARY system

-   Enough disk space on TEMPORARY system

-   Target IBM i system (or LPAR) on the same network you have your
    > TEMPORARY system with enough resources to restore the backup

-   Connection from TEMPORARY system to your IBM Cloud Object Storage or
    > the place you have saved your data.

-   HMC or an already installed system with a basic OS

### **HOW THIS WORKS**

***When using IBM Cloud Object Storage***

-   Install YUM (you can use ACS for easy setup)
    > [[https://www.ibm.com/support/pages/node/706903]{.underline}](https://www.ibm.com/support/pages/node/706903)

-   Now install some tools using ACS or command line : pigz gzip gunzip
    > python3 python3-pip

-   If you don\'t have ICC in your TEMPORARY NFS instance install your
    > s3cmd or awscli

CALL QP2TERM

PATH=\$PATH:/QOpenSys/pkgs/bin

export PATH

yum -y install pigz gzip gunzip python3 python3-pip

pip3 install s3cmd

pip3 install awscli

\# Then you can configure

aws configure

\# Paste your Secret Key, Access Key and select the region (In my case
I\'m using DALLAS: us-south)

\# If using S3CMD

s3cmd \--configure

-   On the TEMPORARY NFS instance you need to download images.(Start
    > Here)

CALL QP2TERM\
\
\# the mount point to the LINUX NFS Server that has the backup images

mkdir /NFS

mkdir /NFS/RESTORE21 (not needed)

chmod 755 /NFS \<\<\< 777???

chmod 777 /NFS/RESTORE21\
\
\# the local IBMi dir that will be NFS exported to the restore client

Mkdir /IBMINFS

Mkdir /IBMINFS/RESTORE21 (not needed)

\# TP in my example mount the Linux NFS server holding the backups:

MOUNT TYPE(\*NFS) MFS(\'HOST-2:/media/skytap/my\_data/NFS2/IBMi04\')
MNTOVRDIR(\'/NFS\')

Copy all the files from /NFS to /IBMINFS (from the linux machine to our
temporary IBMi NFS server)

Cp /NFS to /IBMINFS (not sure best way to do this, was having trouble
using \"cp /NFS/\*\", maybe need quotes .....Ended up doing each file as
a separate command:\
cp /NFS/IMAGE01.ISO /IBMINFS/IMAGE01.ISO

2 \> 2, etc.

*\# Using S3CMD*

*s3cmd get s3://ibmi-backup/IMAGE01.ISO.gz /NFS/RESTORE21*

*s3cmd get s3://ibmi-backup/IMAGE02.ISO.gz /NFS/RESTORE21*

*s3cmd get s3://ibmi-backup/IMAGE03.ISO.gz /NFS/RESTORE21*

*s3cmd get s3://ibmi-backup/IMAGE04.ISO.gz /NFS/RESTORE21*

*\# \... or using AWS CLI (\"us-south\" bucket)*

*aws
\--endpoint-url=https://s3.us-south.cloud-object-storage.appdomain.cloud
s3 cp s3://ibmi-backup/IMAGE01.ISO.gz /NFS/RESTORE21*

*aws
\--endpoint-url=https://s3.us-south.cloud-object-storage.appdomain.cloud
s3 cp s3://ibmi-backup/IMAGE02.ISO.gz /NFS/RESTORE21*

*aws
\--endpoint-url=https://s3.us-south.cloud-object-storage.appdomain.cloud
s3 cp s3://ibmi-backup/IMAGE03.ISO.gz /NFS/RESTORE21*

*aws
\--endpoint-url=https://s3.us-south.cloud-object-storage.appdomain.cloud
s3 cp s3://ibmi-backup/IMAGE04.ISO.gz /NFS/RESTORE21*

-   *We have downloaded our backups on /NFS/RESTORE21 and now we need to
    > decompress our images.*

*/QOpenSys/pkgs/bin/gunzip /NFS/RESTORE21/IMAGE01.ISO.gz*

*/QOpenSys/pkgs/bin/gunzip /NFS/RESTORE21/IMAGE02.ISO.gz*

*/QOpenSys/pkgs/bin/gunzip /NFS/RESTORE21/IMAGE03.ISO.gz*

*/QOpenSys/pkgs/bin/gunzip /NFS/RESTORE21/IMAGE04.ISO.gz*

-   Now we need to go back to green-screen on TEMPORARY system and run
    > the following commands

\# Create an Image Catalog

CRTIMGCLG IMGCLG(RESTORE21) DIR(\'/IBMINFS\') CRTDIR(\*NO)
ADDVRTVOL(\*DIR) IMGTYPE(\*ISO)\
.... Reference the local /IBMINFS directory that contains a copy of the
files from the NFS linux server

\# CREATE VIRTUAL OPTICAL DEVICE on SOURCE

CRTDEVOPT DEVD(NFSOPT) RSRCNAME(\*VRT) LCLINTNETA(\*N)\
and

VRYCFG CFGOBJ(NFSOPT) CFGTYPE(\*DEV) STATUS(\*ON)

\# Load Image Catalog with Device

LODIMGCLG IMGCLG(RESTORE21) DEV(NFSOPT)

\# Verify IMAGE Catalog - This will extract some files for remote IPL
process

\# Takes like 5-10 minutes to complete....

VFYIMGCLG IMGCLG(RESTORE21) TYPE(\*LIC) SORT(\*NO) NFSSHR(\*YES)

\*\*\* Fails when sort = \"Yes\" \>\> need to research why

\# Change Authorities

CHGAUT OBJ(\'/IBMINFS\') USER(\*PUBLIC) DTAAUT(\*RWX) SUBTREE(\*ALL)

CHGAUT OBJ(\'/IBMINFS\') USER(QTFTP) DTAAUT(\*RX) SUBTREE(\*ALL)

\# Export NFS Share

CHGNFSEXP OPTIONS(\'-i -o ro\') DIR(\'/IBMINFS\')

\# Start NFS Servers

STRNFSSVR \*ALL

\# Change TFTP Server Attributes

CHGTFTPA AUTOSTART(\*YES) ALTSRCDIR(\'/IBMINFS\')

\# Restart TFTP server

ENDTCPSVR SERVER(\*TFTP)

STRTCPSVR SERVER(\*TFTP)

-   On the ***TARGET*** system, if ***we have an HMC***, we can start
    > our \"***Network Boot***\" process from menu:

![No alt text provided for this image](./media/image2.png){width="6.5in"
height="4.902777777777778in"}

![No alt text provided for this image](./media/image1.png){width="6.5in"
height="4.875in"}

1.  Boot server IP address is our NFS (TEMPORARY) server

2.  Server directory: The one we have chosen : /IBMINFS

3.  Network Adapter: The adapter connected to the same network as NFS
    > server on our TARGET system

4.  IP Address: The one we choose for our TARGET system

5.  Subnet and Gateway: The ones for our TARGET system

START HERE

-   If we DON\'T have an HMC or we can\'t use it (Cloud environments) we
    > can use the STRNETINS, but we need to setup the Service Tools
    > Server from SST or DST:

[[https://www.ibm.com/docs/en/i/7.3?topic=dst-configuring-service-tools-server-using-sst]{.underline}](https://www.ibm.com/docs/en/i/7.3?topic=dst-configuring-service-tools-server-using-sst)

[<https://www.ibm.com/docs/en/i/7.4?topic=pursvosunfs-configure-service-tools-server-dst-virtual-optical-device-use>\
]{.underline}

See note on web page \"Use Busy Adapter\" to get access to adapter to
change.\
Set IP to like 10.0.0.99, 98 (some value not used by VMs, or other
service IPs)\
Set GW and mask.\
Store \> F7, Activate F14\
\
F5, then redisplay to make sure changes were accepted. \<F5\> from main
menu\
\
\
\# On IBMi do "CFGTCP" (12), set hostname, domain name, and do "more",
set DNS to skytap gw\
Search Priority: LOCAL\
\
Update Host Table Entries, CFGTCP, (10):\
\# In the IBMi host file, for the NFS server like 10.0.0.4, have entries
for both:\
10.0.0.6 host-4, host-4.example.com (TEMP IBMi NFS SERVER you are
attaching to)

10.0.0.5 host-5, host-5.example.com (name of 'this' IBMi host)

\*\*\* Put "+" sign in single character field to add multiple names.\
\
Reboot, ping IP your selected service IP, make sure it responds. Ping
regular host name, ping google.com, etc....

\...And then create the Virtual Optical device: (using \*SRVLAN)\
\
CRTDEVOPT DEVD(NFSRESTORE) RSRCNAME(\*VRT) LCLINTNETA(\*SRVLAN)
RMTINTNETA(\'10.0.0.4\')
NETIMGDIR(\'/media/skytap/my\_data/NFS2/IBMi04\')

\# one line

CRTDEVOPT DEVD(NFSRESTR2) RSRCNAME(\*VRT) LCLINTNETA(\*SRVLAN)
RMTINTNETA(\'10.0.0.6\') NETIMGDIR(\'/IBMINFS\')

VRYCFG CFGOBJ(NFSRESTR2) CFGTYPE(\*DEV) STATUS(\*ON)

(\*\* SAVE TEMPLATE HERE, in case of a problem)\
(Need to \"vary on\" NFSRESTORE again if we reset from Template)

No we can IPL from NFS server

STRNETINS DEV(NFSRESTR2) OPTION(\*LIC) KEYLCKMOD(\*MANUAL)

-   When we press FINISH or confirm the command our TARGET system/LPAR
    > will start to IPL and we need to connect our console for manual
    > operation.

REMEMBER: This process can take some minutes to show DST screen.

-   From here is a similar process to a normal restore 21, but you\'ll
    > need to confirm network information and start installing

1.  License Machine Code

2.  Add additional disks to ASP or backup won\'t fit

3.  Install Operating System

-   Once you get the IBM i signon screen you\'ll need to setup the
    > Virtual Optical device \"again\", because you have reinstalled the
    > OS

CRTDEVOPT DEVD(NFSRESTORE) RSRCNAME(\*VRT) LCLINTNETA(\*SRVLAN)
RMTINTNETA(\'192.168.50.229\') NETIMGDIR(\'/NFS/RESTORE21\')

VRYCFG CFGOBJ(NFSRESTORE) CFGTYPE(\*DEV) STATUS(\*ON)

-   Make your changes to your OS before FULL SYSTEM RESTORE and start
    > the process

GO RESTORE

21

\# Choose NFSRESTORE as your device

-   Now your system will start to restore your data from the NFS server.

Hope this can help you make your restores from your network and/or
migrations to the cloud using NFS.

**FINAL NOTES**

-   NFS does not encrypt data.

-   Performance depends on network speed and I/O. If NFS server is slow,
    > restore is slow too

-   When downloading data from IBM Cloud Object Storage charges may
    > apply, depending on your plan.

Good luck and remember, you can drop me a line if something goes wrong
or have any question
