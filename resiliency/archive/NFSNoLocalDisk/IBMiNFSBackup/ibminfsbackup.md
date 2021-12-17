IBMi:

Backup / Restore using NFS, no local disk

[[https://www.linkedin.com/pulse/ibm-i-backup-your-lpar-nfs-without-using-its-own-diego-kesselman?trk=public\_post-content\_share-article\_title]{.underline}](https://www.linkedin.com/pulse/ibm-i-backup-your-lpar-nfs-without-using-its-own-diego-kesselman?trk=public_post-content_share-article_title)
(Part 1)

[[https://www.linkedin.com/pulse/ibm-i-restore-your-lpar-from-nfs-diego-kesselman]{.underline}](https://www.linkedin.com/pulse/ibm-i-restore-your-lpar-from-nfs-diego-kesselman)
(Part 2)

[[https://www.ibm.com/support/pages/ibm-i-save-and-restore-using-virtual-optical-images-nfs-server]{.underline}](https://www.ibm.com/support/pages/ibm-i-save-and-restore-using-virtual-optical-images-nfs-server)
(original IBM article)

[[https://www.ibm.com/support/pages/node/6220172]{.underline}](https://www.ibm.com/support/pages/node/6220172)\
\
[[https://cloud.ibm.com/docs/power-iaas?topic=power-iaas-configuring-mass-data-migration-mdm-on-ibm-i-vm]{.underline}](https://cloud.ibm.com/docs/power-iaas?topic=power-iaas-configuring-mass-data-migration-mdm-on-ibm-i-vm)
(shows the right alignment of the NFS directories that \"worked\")

Sometimes we need to make backups to disk but don\'t have enough space.
And backups to disk are very useful to migrate to the cloud, new
datacenter, new system with incompatible media or just get a remote
second copy of our backups as a lifesaver.

I wrote a post on LinkedIn a couple of days ago
[[https://www.linkedin.com/posts/diegokesselman\_ibmi-as400-iseries-activity-6810376492615385089\--eVX]{.underline}](https://www.linkedin.com/posts/diegokesselman_ibmi-as400-iseries-activity-6810376492615385089--eVX)
, but unfortunately the link in the reference is dead, so this is my own
how-to with my adjustments.

How it all works out:

![Schema on how to backup to an NFS an to the cloud from
there](./media/image4.jpg){width="6.5in" height="3.6527777777777777in"}

### **STEP by STEP**

You\'ll need a fast network, IBMi (in my case V7R4), Linux (I\'m using
Ubuntu server, but any modern distribution works) and a bucket on IBM
Cloud Object Storage if you want to backup or migrate to the cloud

So these are the steps and requirements:

-   Your IBM i must be on V7R1 or higher release (guess this will work
    > on V5R4 but not tested). I suggest get the latest CUM and Group
    > packages.

-   When migrating to the cloud we need to install the cloudinit program
    > with YUM or using this guide:

[[https://www.ibm.com/support/pages/cloud-init-support-ibm-i]{.underline}](https://www.ibm.com/support/pages/cloud-init-support-ibm-i)

-   Ubuntu 14.X\
    > \<ADD DISK TO SKYTAP VM: example 100GB to cover size of LPAR\>\
    > /media/skytap/my\_data (mount point)

-   Get a Linux system with enough disk so backup can fit. I used Debian
    > with NVMe SSD, you can use HDD with a good RAID adapter, just
    > remember that I/O can impact on your backup performance.

-   Linux and IBM i must see each other on 1GB network or faster between
    > them.

-   Install these tools on your Linux: Python3, NFS Server, awscli (yes,
    > AWS Client works with all S3 servers) and PIGZ .

\# Install tools on Linux

sudo apt install python3 python3-pip nfs-kernel-server awscli pigz
nfs-common\
Python3, python3-pip, nfs-kernel-server, awscli, pigz

NFS Server: 10.0.0.4 ( 192. 168. 50. 22)

IBMi: 10.0.0.3 (192. 168. 50. 244)

-   Setup your NFS Server on Linux system. My network : IBMi04 with IP
    > 10.0.0.3 and Linux 10.0.0.4

\# Create NFS work directory

mkdir /NFS \<\<\<\< should be your scratch space disk dir....where NFS
will point to.....

chmod 777 /NFS (755?)

mkdir /NFS/IBMi04

chmod 777 /NFS/IBMi04

Example: /media/skytap/my\_data/NFS2,\....and ....
/media/skytap/my\_data/NFS2/IBMi04

\# Now edit the /etc/exports file

sudo vi /etc/exports

\# Add the following line and save the file with wq!

/NFS 10.0.0.0/24(rw,sync,no\_subtree\_check)

-   Restart NFS service on Linux:

\# Restart NFS sevice

sudo systemctl restart nfs-kernel-server

\# Export your shares

sudo exportfs -ra

\# Look what are you sharing

showmount \--exports localhost

You have to do this next step where you set up SST with a LAN interface
that allows for access to optical device access over NFS. (Still
researching why this is needed, but it is). Need to do the steps below
and assign an IP address from your IBMi subnet to the SST LAN interface.
After setting the IP address, you must IPL. Then you should be able to
ping that new IP address.\<INSERT SCREEN SHOT HERE\>\
\
WHY: Because later on you create the virtual optical device that talks
over NFS, \"BUT\", it goes thru the \"SERVICE CONNECTION\", not the
in-guest TCP/IP stack. So when you do option 21, END all subsystems,
in-guest TCP/IP goes down, but the GO SAVE still talks to the virtual
device over the service connection.

-   Now go to your IBM i. We need to setup the Service Tools Server from
    > SST or DST:

[[https://www.ibm.com/docs/en/i/7.3?topic=dst-configuring-service-tools-server-using-sst]{.underline}](https://www.ibm.com/docs/en/i/7.3?topic=dst-configuring-service-tools-server-using-sst)

[[https://www.ibm.com/docs/en/i/7.4?topic=pursvosunfs-configure-service-tools-server-dst-virtual-optical-device-use]{.underline}](https://www.ibm.com/docs/en/i/7.4?topic=pursvosunfs-configure-service-tools-server-dst-virtual-optical-device-use)

**NOTES:\
\
**See note on web page \"Use Busy Adapter\" to get access to adapter to
change.\
Set IP to like 10.0.0.99, 98 (some value not used by VMs, or other
service IPs)\
Set GW and mask.\
Store \> F7, Activate F14\
\
F5, then redisplay to make sure changes were accepted. \<F5\> from main
menu

-   We need to add the NFS server entry to the host table on IBM i
    > server:

```{=html}
<!-- -->
```
-   Now we will mount the NFS share and start creating the virtual
    > media - We need to know how much data we need to backup to create
    > enough virtual media. It\'s not possible to create during backup
    > process:

Update Host Table Entries, CFGTCP, (10):\
\# In the IBMi host file, for the NFS server like 10.0.0.4, have entries
for both:\
10.0.0.4 host-4, host-4.example.com (NFS SERVER you are attaching to)

10.0.0.3 host-3, host-3.example.com (name of 'this' IBMi host)

\*\*\* Put "+" sign in single character field to add multiple names.

On IBMi do "CFGTCP" (12), set hostname, domain name, and do "more", set
DNS to skytap gw\
Search Priority: LOCAL\
\
Reboot, ping IP your selected service IP, make sure it responds. Ping
regular host name, ping google.com, etc....FYI: If you change the host
table only, need to logout/login.\
\
\# Logout, log back in

\# Mount the resource

MOUNT TYPE(\*NFS) MFS(\'HOST-2:/media/skytap/my\_data/NFS2/IBMi04\')
MNTOVRDIR(\'/NFS\')

Unmount: (just FYI)\
UNMOUNT TYPE(\*NFS) MNTOVRDIR(\'/NFS\')

\# Create the empty files - 4 x 10GB virtual optical disks

CALL QP2TERM

cd /NFS

dd if=/dev/zero of=IMAGE01.ISO bs=1M count=10240

dd if=/dev/zero of=IMAGE02.ISO bs=1M count=10240

dd if=/dev/zero of=IMAGE03.ISO bs=1M count=10240

dd if=/dev/zero of=IMAGE04.ISO bs=1M count=10240

....to 8 for 80 GB

Wait for \"complete\" message.\"

\# Now we need to create the VOLUME\_LIST file entries to tell our
device how to change media when full.

echo \'IMAGE01.ISO W\' \> VOLUME\_LIST

echo \'IMAGE02.ISO W\' \>\> VOLUME\_LIST

echo \'IMAGE03.ISO W\' \>\> VOLUME\_LIST

echo \'IMAGE04.ISO W\' \>\> VOLUME\_LIST

....to 8 for 80GB, etc.....

-   Now we need to create a device description:

\# Create the device (use IP address and path from NFS mount above)

CRTDEVOPT DEVD(SAV2NFS3) RSRCNAME(\*VRT) LCLINTNETA(\*SRVLAN)
RMTINTNETA(\'10.0.0.4\')
NETIMGDIR(\'/media/skytap/my\_data/NFS2/IBMi04\')

\*\*\* use IP address

\# Vary on the device

VRYCFG CFGOBJ(SAV2NFS3) CFGTYPE(\*DEV) STATUS(\*ON)

-   Initialize the virtual optical media (8 sets X 10 = 80 gig)

-   **Two** commands for each entry

-   See below \"option 7\" if error on LODIMGCLGE

-   If you get \"Media / Device Error\" \>\> check export, re-created
    > optical device

-   Takes a while for INZOPT to complete\
    > \
    > LODIMGCLGE IMGCLG(\*DEV) IMGCLGIDX(8) DEV(SAV2NFS3)\
    > INZOPT NEWVOL(IBMi04\_8) DEV(SAV2NFS3) CHECK(\*NO)\
    > \
    > LODIMGCLGE IMGCLG(\*DEV) IMGCLGIDX(7) DEV(SAV2NFS3)\
    > INZOPT NEWVOL(IBMi04\_7) DEV(SAV2NFS3) CHECK(\*NO)\
    > \
    > LODIMGCLGE IMGCLG(\*DEV) IMGCLGIDX(6) DEV(SAV2NFS3)\
    > INZOPT NEWVOL(IBMi04\_6) DEV(SAV2NFS3) CHECK(\*NO)\
    > \
    > LODIMGCLGE IMGCLG(\*DEV) IMGCLGIDX(5) DEV(SAV2NFS3)\
    > INZOPT NEWVOL(IBMi04\_5) DEV(SAV2NFS3) CHECK(\*NO)\
    > \
    > LODIMGCLGE IMGCLG(\*DEV) IMGCLGIDX(4) DEV(SAV2NFS3)\
    > INZOPT NEWVOL(IBMi04\_4) DEV(SAV2NFS3) CHECK(\*NO)\
    > \
    > LODIMGCLGE IMGCLG(\*DEV) IMGCLGIDX(3) DEV(SAV2NFS3)\
    > INZOPT NEWVOL(IBMi04\_3) DEV(SAV2NFS3) CHECK(\*NO)\
    > \
    > LODIMGCLGE IMGCLG(\*DEV) IMGCLGIDX(2) DEV(SAV2NFS3)\
    > INZOPT NEWVOL(IBMi04\_2) DEV(SAV2NFS3) CHECK(\*NO)\
    > \
    > LODIMGCLGE IMGCLG(\*DEV) IMGCLGIDX(1) DEV(SAV2NFS3)\
    > INZOPT NEWVOL(IBMi04\_1) DEV(SAV2NFS3) CHECK(\*NO)

....to 8 for 80 gig, etc.....

FYI: WRKIMGCLGE IMGCLG(\*DEV) DEV(SAV2NFS3)\
\*\*\* see contents of device assigned image catalog\
/media/skytap/my\_data/NFS2/IBMi04

Errors with Vary On:

WRKCFGSTS CFGTYPE(\*DEV) CFGD(SAV2NFS3) \<\<\< opt 7, make available

-   If we are migrating to the cloud we need to run the cloud-init
    > process :

CALL PGM(QSYS/QAENGCHG) PARM(\*ENABLECI) ??????

-   Now we are going to perform a GO SAVE option 21. You can use your
    > normal CL if you want, but I think this es more complete and
    > complex process, because we are backing up to the network with TCP
    > services down. (Try 21 without ending TCP)

ENDTCP \<\<\< our virtual device runs over the \"service path\", not in
guest TCP/IP

ENDSBS \*ALL \*IMMED

GO SAVE -\> 21

On main screen use SAV2NFS3 device name

-   Once the backup is done, you need to deactivate the device:

VRYCFG CFGOBJ(SAV2NFS3) CFGTYPE(\*DEV) STATUS(\*OFF)

INFO: WRKOPTVOL (work with optical device: display, initialize, volume
size, remaining)\
INFO: DSPOPT (display optical) shows name of \"Volume\" like
\[IBMI04\_5\]\
\
-show contents of backup:

INFO: WRKIMGCLGE IMGCLG(\*DEV) DEV(SAV2NFS3)

\*\* Do this first, and \"mount\" one of the entries, then you can do
dspopt....(next command)

INFO: DSPOPT VOL(IBMI04\_5) DEV(SAV2NFS3) DATA(\*SAVRST) PATH(\*ALL)

\*\* see the actual file that were backed up

=========================================

-   If you want to upload to the cloud, you need to create a bucket and
    > setup the awscli:

[[https://cloud.ibm.com/docs/cloud-object-storage?topic=cloud-object-storage-aws-cli]{.underline}](https://cloud.ibm.com/docs/cloud-object-storage?topic=cloud-object-storage-aws-cli)

***To create your IBM Cloud Object Storage Bucket:***

1.  Go to IBM Cloud portal and create your account:
    > https://cloud.ibm.com .

2.  Go to the catalog and choose \"Cloud Object Storage\" to create a
    > resource. You can create a Lite account with 25GB free.

3.  Create your bucket using the guide or the the IBM Cloud Object
    > Storage portal. You need to get credentials.

-   Now, go back to your Linux system and ***compress*** data you want
    > to ***upload*** to the cloud. You can do all in one command:

cd /NFS/IBMi04

\# pigz compress using gzip algorithm but in parallel, -9=High
compression, -p40=40 threads we use to compress.

\# You can change this values.

cat IMAGE01.ISO \| pigz -9 -p40 -c \| aws
\--endpoint-url=https://s3.us-south.cloud-object-storage.appdomain.cloud
s3 cp - s3://ibmi-backup/IMAGE01.ISO.gz

cat IMAGE02.ISO \| pigz -9 -p40 -c \| aws
\--endpoint-url=https://s3.us-south.cloud-object-storage.appdomain.cloud
s3 cp - s3://ibmi-backup/IMAGE02.ISO.gz

cat IMAGE03.ISO \| pigz -9 -p40 -c \| aws
\--endpoint-url=https://s3.us-south.cloud-object-storage.appdomain.cloud
s3 cp - s3://ibmi-backup/IMAGE03.ISO.gz

cat IMAGE04.ISO \| pigz -9 -p40 -c \| aws
\--endpoint-url=https://s3.us-south.cloud-object-storage.appdomain.cloud
s3 cp - s3://ibmi-backup/IMAGE04.ISO.gz

**NOTE**: The ***\--endpoint-url*** configuration is the one I\'ve
chosen for Dallas location. This can vary depending on the site you
choose to create your bucket.

-   Once this process has finished you can verify if everything is on
    > the cloud just visiting the portal:

![No alt text provided for this
image](./media/image11.png){width="6.5in" height="2.111111111111111in"}

\... or you can use the command line:

aws
\--endpoint-url=https://s3.us-south.cloud-object-storage.appdomain.cloud
s3 ls s3://ibmi-backup/

-   If you DON\'T want to upload to a new system or to the cloud, but
    > compress:

\# PIGZ is parallel GZIP.

pigz -9 -p40 IMAGE01.ISO

pigz -9 -p40 IMAGE02.ISO

pigz -9 -p40 IMAGE03.ISO

pigz -9 -p40 IMAGE04.ISO

Now you can continue restoring on the cloud, on a new system or just
archive for future reference if you want, but the step-by-step will be
part of a different article.

As always, if you need help you can contact me

Good luck!

### **Published By**

![Diego Kesselman](./media/image6.jpg){width="1.0416666666666667in"
height="1.0416666666666667in"}

### [**[Diego Kesselman]{.underline}**](https://mx.linkedin.com/in/diegokesselman?trk=author_mini-profile_title)

#### **ESSELWARE SOLUCIONES, SA de CV**

[[Follow]{.underline}](https://www.linkedin.com/login?session_redirect=https%3A%2F%2Fwww%2Elinkedin%2Ecom%2Fpulse%2Fibm-i-backup-your-lpar-nfs-without-using-its-own-diego-kesselman&trk=ugc-post-bar__follow-button)

IBM i backup to NFS and then to the cloud (with no IBM i disk use). On a
recent post I talked about a hidden gem: The save to NFS capacity on IBM
i. Unfortunately the page is down, so I\'ve created a step-by-step guide
on this article.
[[\#UPDATE]{.underline}](https://www.linkedin.com/signup/cold-join?session_redirect=https%3A%2F%2Fwww%2Elinkedin%2Ecom%2Ffeed%2Fhashtag%2Fupdate&trk=ugc-post-bar__share-text):
The official document from IBM is working again https://lnkd.in/gWJxk2n
[[\#IBMi]{.underline}](https://www.linkedin.com/signup/cold-join?session_redirect=https%3A%2F%2Fwww%2Elinkedin%2Ecom%2Ffeed%2Fhashtag%2Fibmi&trk=ugc-post-bar__share-text)
[[\#ibmiseries]{.underline}](https://www.linkedin.com/signup/cold-join?session_redirect=https%3A%2F%2Fwww%2Elinkedin%2Ecom%2Ffeed%2Fhashtag%2Fibmiseries&trk=ugc-post-bar__share-text)
[[\#ibmcloud]{.underline}](https://www.linkedin.com/signup/cold-join?session_redirect=https%3A%2F%2Fwww%2Elinkedin%2Ecom%2Ffeed%2Fhashtag%2Fibmcloud&trk=ugc-post-bar__share-text)
[[\#iseries]{.underline}](https://www.linkedin.com/signup/cold-join?session_redirect=https%3A%2F%2Fwww%2Elinkedin%2Ecom%2Ffeed%2Fhashtag%2Fiseries&trk=ugc-post-bar__share-text)
[[\#as400]{.underline}](https://www.linkedin.com/signup/cold-join?session_redirect=https%3A%2F%2Fwww%2Elinkedin%2Ecom%2Ffeed%2Fhashtag%2Fas400&trk=ugc-post-bar__share-text)
[[\#cloudobjectstorage]{.underline}](https://www.linkedin.com/signup/cold-join?session_redirect=https%3A%2F%2Fwww%2Elinkedin%2Ecom%2Ffeed%2Fhashtag%2Fcloudobjectstorage&trk=ugc-post-bar__share-text)
[[\#cloud]{.underline}](https://www.linkedin.com/signup/cold-join?session_redirect=https%3A%2F%2Fwww%2Elinkedin%2Ecom%2Ffeed%2Fhashtag%2Fcloud&trk=ugc-post-bar__share-text)
[[\#powervs]{.underline}](https://www.linkedin.com/signup/cold-join?session_redirect=https%3A%2F%2Fwww%2Elinkedin%2Ecom%2Ffeed%2Fhashtag%2Fpowervs&trk=ugc-post-bar__share-text)
[[\#powervirtualserver]{.underline}](https://www.linkedin.com/signup/cold-join?session_redirect=https%3A%2F%2Fwww%2Elinkedin%2Ecom%2Ffeed%2Fhashtag%2Fpowervirtualserver&trk=ugc-post-bar__share-text)
[[\#ibm]{.underline}](https://www.linkedin.com/signup/cold-join?session_redirect=https%3A%2F%2Fwww%2Elinkedin%2Ecom%2Ffeed%2Fhashtag%2Fibm&trk=ugc-post-bar__share-text)

11 comments

![article-comment\_\_guest-image](./media/image1.png){width="0.6944444444444444in"
height="0.6944444444444444in"}

[[Sign
in](https://www.linkedin.com/login?session_redirect=https%3A%2F%2Fwww%2Elinkedin%2Ecom%2Fpulse%2Fibm-i-backup-your-lpar-nfs-without-using-its-own-diego-kesselman&trk=article-reader_leave-comment)]{.underline}
to leave your comment

![Pramod Koti](./media/image3.png){width="0.6944444444444444in"
height="0.6944444444444444in"}

### [**[Pramod Koti]{.underline}**](https://in.linkedin.com/in/pramod-koti-b21bb753?trk=article-comment-author_mini-profile_title)

#### **Lead Consultant Enterprise Infrastructure Transformation for IBMi/iSeries at Infosys**

One other great article 👍

[[Like]{.underline}](https://www.linkedin.com/login?session_redirect=https%3A%2F%2Fwww%2Elinkedin%2Ecom%2Fpulse%2Fibm-i-backup-your-lpar-nfs-without-using-its-own-diego-kesselman&trk=article-reader_like-comment)

[[Reply]{.underline}](https://www.linkedin.com/login?session_redirect=https%3A%2F%2Fwww%2Elinkedin%2Ecom%2Fpulse%2Fibm-i-backup-your-lpar-nfs-without-using-its-own-diego-kesselman&trk=article-reader_reply-comment)

1 Like

1w

![Kevin Tafuro](./media/image2.png){width="0.6944444444444444in"
height="0.6944444444444444in"}

### [**[Kevin Tafuro]{.underline}**](https://it.linkedin.com/in/kevin-tafuro-33673288?trk=article-comment-author_mini-profile_title)

#### **Senior System Engineer presso DiTech**

Gianfranco Zamboni take a look

[[Like]{.underline}](https://www.linkedin.com/login?session_redirect=https%3A%2F%2Fwww%2Elinkedin%2Ecom%2Fpulse%2Fibm-i-backup-your-lpar-nfs-without-using-its-own-diego-kesselman&trk=article-reader_like-comment)

[[Reply]{.underline}](https://www.linkedin.com/login?session_redirect=https%3A%2F%2Fwww%2Elinkedin%2Ecom%2Fpulse%2Fibm-i-backup-your-lpar-nfs-without-using-its-own-diego-kesselman&trk=article-reader_reply-comment)

1 Like

1w

![Rajesh N](./media/image9.jpg){width="1.0416666666666667in"
height="1.0416666666666667in"}

### [**[Rajesh N]{.underline}**](https://in.linkedin.com/in/rajesh-n-69518714?trk=article-comment-author_mini-profile_title)

#### **IBM i Certified \| AS400 \| IBM Power Systems \| Cloud - IBM iCloud**

And additional info his method can be used for PTF to be applied having
it on single system and accessing via NFS

[[Like]{.underline}](https://www.linkedin.com/login?session_redirect=https%3A%2F%2Fwww%2Elinkedin%2Ecom%2Fpulse%2Fibm-i-backup-your-lpar-nfs-without-using-its-own-diego-kesselman&trk=article-reader_like-comment)

[[Reply]{.underline}](https://www.linkedin.com/login?session_redirect=https%3A%2F%2Fwww%2Elinkedin%2Ecom%2Fpulse%2Fibm-i-backup-your-lpar-nfs-without-using-its-own-diego-kesselman&trk=article-reader_reply-comment)

1 Like

1 Reply

![Diego Kesselman](./media/image8.jpg){width="1.0416666666666667in"
height="1.0416666666666667in"}

### [**[Diego Kesselman]{.underline}**](https://mx.linkedin.com/in/diegokesselman?trk=article-comment-author_mini-profile_title)

#### **ESSELWARE SOLUCIONES, SA de CV**

You can use NFS for PTF, Operating System install, OS upgrade and
backup/restore

[[Like]{.underline}](https://www.linkedin.com/login?session_redirect=https%3A%2F%2Fwww%2Elinkedin%2Ecom%2Fpulse%2Fibm-i-backup-your-lpar-nfs-without-using-its-own-diego-kesselman&trk=article-reader_like-comment)

[[Reply]{.underline}](https://www.linkedin.com/login?session_redirect=https%3A%2F%2Fwww%2Elinkedin%2Ecom%2Fpulse%2Fibm-i-backup-your-lpar-nfs-without-using-its-own-diego-kesselman&trk=article-reader_reply-comment)

2w

2w

![Roberto De Pedrini](./media/image12.jpg){width="1.0416666666666667in"
height="1.0416666666666667in"}

### [**[Roberto De Pedrini]{.underline}**](https://it.linkedin.com/in/robertodepedrini?trk=article-comment-author_mini-profile_title)

#### **ERP Consultant - IBM i Developer and Trainer - Founder at Faq400.com and Nextev Srl**

Thank you Diego Kesselman , very useful!

[[Like]{.underline}](https://www.linkedin.com/login?session_redirect=https%3A%2F%2Fwww%2Elinkedin%2Ecom%2Fpulse%2Fibm-i-backup-your-lpar-nfs-without-using-its-own-diego-kesselman&trk=article-reader_like-comment)

[[Reply]{.underline}](https://www.linkedin.com/login?session_redirect=https%3A%2F%2Fwww%2Elinkedin%2Ecom%2Fpulse%2Fibm-i-backup-your-lpar-nfs-without-using-its-own-diego-kesselman&trk=article-reader_reply-comment)

2 Likes

2w

![Matthew Seeberger](./media/image13.jpg){width="1.0416666666666667in"
height="1.0416666666666667in"}

### [**[Matthew Seeberger]{.underline}**](https://www.linkedin.com/in/mkseeberger?trk=article-comment-author_mini-profile_title)

#### **Power i Engineer at CMA Technology Solutions**

Great article! Keep developing and sharing your efforts here. It\'s nice
to have options 😉 I still want to play around with your onfiguration to
backup to OneDrive

[[Like]{.underline}](https://www.linkedin.com/login?session_redirect=https%3A%2F%2Fwww%2Elinkedin%2Ecom%2Fpulse%2Fibm-i-backup-your-lpar-nfs-without-using-its-own-diego-kesselman&trk=article-reader_like-comment)

[[Reply]{.underline}](https://www.linkedin.com/login?session_redirect=https%3A%2F%2Fwww%2Elinkedin%2Ecom%2Fpulse%2Fibm-i-backup-your-lpar-nfs-without-using-its-own-diego-kesselman&trk=article-reader_reply-comment)

1 Like

1 Reply

![Diego Kesselman](./media/image5.jpg){width="1.0416666666666667in"
height="1.0416666666666667in"}

### [**[Diego Kesselman]{.underline}**](https://mx.linkedin.com/in/diegokesselman?trk=article-comment-author_mini-profile_title)

#### **ESSELWARE SOLUCIONES, SA de CV**

You can combine both projects ;-)

[[Like]{.underline}](https://www.linkedin.com/login?session_redirect=https%3A%2F%2Fwww%2Elinkedin%2Ecom%2Fpulse%2Fibm-i-backup-your-lpar-nfs-without-using-its-own-diego-kesselman&trk=article-reader_like-comment)

[[Reply]{.underline}](https://www.linkedin.com/login?session_redirect=https%3A%2F%2Fwww%2Elinkedin%2Ecom%2Fpulse%2Fibm-i-backup-your-lpar-nfs-without-using-its-own-diego-kesselman&trk=article-reader_reply-comment)

2w

2w

![Richard Schoen](./media/image10.jpg){width="1.0416666666666667in"
height="1.0416666666666667in"}

### [**[Richard Schoen]{.underline}**](https://www.linkedin.com/in/richardschoen?trk=article-comment-author_mini-profile_title)

#### **I help people automate their work**

I can\'t imagine why IBM would take that useful article down. Seems odd
to me. Thanks for the info.

[[Like]{.underline}](https://www.linkedin.com/login?session_redirect=https%3A%2F%2Fwww%2Elinkedin%2Ecom%2Fpulse%2Fibm-i-backup-your-lpar-nfs-without-using-its-own-diego-kesselman&trk=article-reader_like-comment)

[[Reply]{.underline}](https://www.linkedin.com/login?session_redirect=https%3A%2F%2Fwww%2Elinkedin%2Ecom%2Fpulse%2Fibm-i-backup-your-lpar-nfs-without-using-its-own-diego-kesselman&trk=article-reader_reply-comment)

1 Like

1 Reply

![Diego Kesselman](./media/image7.jpg){width="1.0416666666666667in"
height="1.0416666666666667in"}

### [**[Diego Kesselman]{.underline}**](https://mx.linkedin.com/in/diegokesselman?trk=article-comment-author_mini-profile_title)

#### **ESSELWARE SOLUCIONES, SA de CV**

Page is back ! (Date was updated)

[[Like]{.underline}](https://www.linkedin.com/login?session_redirect=https%3A%2F%2Fwww%2Elinkedin%2Ecom%2Fpulse%2Fibm-i-backup-your-lpar-nfs-without-using-its-own-diego-kesselman&trk=article-reader_like-comment)

[[Reply]{.underline}](https://www.linkedin.com/login?session_redirect=https%3A%2F%2Fwww%2Elinkedin%2Ecom%2Fpulse%2Fibm-i-backup-your-lpar-nfs-without-using-its-own-diego-kesselman&trk=article-reader_reply-comment)

2w

2w
