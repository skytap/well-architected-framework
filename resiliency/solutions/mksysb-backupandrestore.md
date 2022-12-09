---
Title: Mksysb Backup and Restore in Skytap

Description: Skytap Cold Migration Solution - Backup and Restore to Skytap on Azure using Mksysb.
Authors: Chris Eigbrett - Director, Skytap Service Delivery, Abhishek Jain – Cloud Solution Architect, Matthew Romero - Technical Product Marketing Manager
---
# Backup and Restore to Skytap on Azure using Mksysb
This guide is provided “as-is”. Information and views expressed in this document, including URL and other Internet website references, may change without notice and usage of the included material assumes this risk.

This document does not provide you with any legal rights to any intellectual property in any product. You may copy and use this document for your internal, reference purposes.

# Table of Contents<a name="toc"></a>

* [Prechecks](#prechecks)
* [Backing up On-Prem System using Mksysb Backup Execution](#backing-up-on-prem-system-using-mksysb-backup-execution)
* [Transferring Mksysb to Skytap](#transferring-mksysb-to-skytap)
  * [Option 1 - Direct Connectivity](#option-1---direct-connectivity)
  * [Option 2 - Published Service](#option-2---published-service)
  * [Option 3 - Azure Blob](#option-3---azure-blob)
* [Restore In Skytap](#restore-in-skytap)
  * [Initiate a NIM server in Skytap](#initiate-a-nim-server-in-skytap)
<!---  * [Configure NIM Server](#configure-nim-server)--->
  * [Set up Client on NIM Server to Restore Mksysb](#set-up-client-on-nim-server-to-restore-mksysb)
* [Setup New LPAR and restore the Mksysb](#setup-new-lpar-and-restore-the-mksysb)

This guide is provided "as-is". The information and views expressed in
this document, including URL and other Internet website references, may
change without notice and usage of the included material assumes this
risk.

This document does not provide you with any legal rights to any
intellectual property in any product. You may copy and use this document
for your internal reference purposes.

#  Prechecks<a name="prechecks"></a>

1.  Error logs for any OS level issues.

2.  Check Software inconsistencies using \# lppchk -v.

3.  Resolve any operating system level issue to take a healthy system
    backup.

4.  Enough free space to backup all files in a filesystem.

###### *[Back to the Top](#toc)*

# Backing up On-Prem System using Mksysb  Backup Execution<a name="backing-up-on-prem-system-using-mksysb-backup-execution"></a>

1.  Populate the /tmp/exclude.rootvg file for excluding the file systems from the backup.

2.  Use command \# mksysb -ipX /\<FS>/\<hostname>.mksysb.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image2.png" width="400">

Note: It may take some time to complete the backup

3.  Verify content of mksysb using \# lsmksysb -lf >
    /\<FS>/\<hostname>.mksysb.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image3.png" width="400">

###### *[Back to the Top](#toc)*
## Transferring Mksysb to Skytap<a name="transferring-mksysb-to-skytap"></a>

### Option 1 - Direct Connectivity<a name="option-1---direct-connectivity"></a>

**Pros:**

-   VPN or ExpressRoute

-   Network planning is required ahead of time

-   Secured and fast data transfer

-   Future proof

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image4.png" width="700">

### Option 2 - Published Service<a name="option-2---published-service"></a>

**Pros:**

-   Fastest and cheapest to deploy

-   Native Skytap support

-   Only the NIM server in Skytap is required to start data transfer

-   Encrypted data transfer over public Internet

-   Only recommended for proof-of-concept

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image5.png" width="700">

### Option 3 - Azure Blob<a name="option-3---azure-blob"></a>

**Pros:**

-   Azure Blob Storage

-   Secured and fast data transfer

-   Additional VM for AZ copy required

-   Future proof

-   Data can be copied over even before Skytap service is started

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image6.png" width="700">

###### *[Back to the Top](#toc)*

# Restore In Skytap<a name="restore-in-skytap"></a>

##  Initiate a NIM server in Skytap<a name="initiate-a-nim-server-in-skytap"></a>

1.  Deploy a new NIM AIX template using public templates (latest AIX level recommended).

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image7a.png" width="700">

2.  Rename the LPAR to NIM server.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image8.png" width="700">

3.  Set up the desired network and attach the network to the LPAR.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image9.png" width="400">

4.  Set the desired hostname in Adapter setting.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image10.png" width="700">

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image11.png" width="700">

5.  Power on the LPAR and logon with root access.

###### *[Back to the Top](#toc)*

<!--- ### Configure NIM Server<a name="configure-nim-server"></a>

1.  Check if required file sets are Installed \# lslpp -l \| grep -i nim.

2.  Attach Install ISO image to the server.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image12.png">

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image13.png">

3.  Run #cfgmgr in OS to configure CDROM.

4.  List CDROM to confirm \# lsdev -Cc cdrom.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image14.png">

5.  Install file sets from CDROM \# smitty install_latest.

    a.  Select CDROM using F4 or esc+4 keys.

    b.  Search for NIM in software to install.

    c.  Select NIM master using F7 or esc+7 keys.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image15.png">

d.  Continue installation.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image16.png">

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image17.png">

6.  Exit installation menu using F10 or esc+0.

7.  Confirm installation is done \# lslpp -l \| grep -i nim.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image18.png">

8.  Set Hostname and /etc/hosts to exact name as the hostname used when configuring the LAPRs Network Adapter settings.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image19.png">

9.  Run NIM configuration \# smitty nim_config_env.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image20.png">

10. Press Enter to start. Note: this step will take approximately 30 mins. to complete.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image21.png">

11. Congratulations, your NIM Server is ready!
--->

###### *[Back to the Top](#toc)*

### Set up Client on NIM Server to Restore Mksysb<a name="set-up-client-on-nim-server-to-restore-mksysb"></a>

1. Set Hostname and /etc/hosts to exact name as the hostname used when configuring the LAPRs Network Adapter settings.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image19.png" width="600">

2.  Update the /etc/hosts file with the client hostname.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image22.png" width="600">

3.  Verify hostname is resolved.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image23.png" width="600">

4.  Add the Client in NIM config \# smitty nim_mkmac and enter the client hostname.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image24.png" width="600">

5.  Change the setting as shown below and press Enter.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image25.png" width="600">

6.  NIM Client is defined and can be verified in \# lsnimn nimclient.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image26.png" width="600">

7.  Copy the Source Mksysb in a new filesystem /export/mksysb.

8.  Create the filesystem \# crfs -v jfs2 -m /export/mksysb -A > yes -a size=10G -r rootvg.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image27.png" width="600">

9.  List the mksysb file content lsmksysb -lf.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image28.png" width="600">

10.  Create the two NIM resources required for restoration.

10a. **Mksysb resource**

10a-1.  Mksysb - \# smitty nim_res > define a resource and select mksysb and press enter.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image29.png" width="600">

10a-2.  Fill in the Name, Server of resource and location of  resource (absolute path for location of the mksysb file).

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image30.png" width="600">

10b.  **Spot resource**

10b-1.  Define spot from mksysb resource \# smitty nim_res -\> define a
        resource select spot and press enter (make sure > you have
        enough space in /export/spot filesystem \~2 > GB).

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image31.png" width="600">

10b-2.  Enter Name, Server of resource, Source of Install images and Location (absolute Path for the resource "/export/spot sapaix_spot").

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image32.png" width="600">

10b-3.  Creation of spot will take some time approximately 15 to 30 minutes.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image33.png" width="600">

11. Assign resources to nimclient machine: \# smitty nim_mac_res.

12. Allocate Network Install resources and select nimclient from the list and press Enter.

13. Use F7 or Esc+7 to select the sapmksysb and sapaix_spot and press Enter.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image34.png" width="600">

14. Command output will be as below exit using F10 or esc+0.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image35.png" width="600">

15. Enable Bos installation for the client \# smitty nim_mac_op nimclient.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image36.png" width="600">

16. Select bos_inst and press Enter.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image37.png" width="600">

17. Select the options shown below for mksysb restore (Use Arrow keys and F4 > or Esc+4).

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image38.png" width="600">

18. The setup is now complete.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image39.png" width="600">

19. Verify the client Status \# lsnim -l nimclient.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image40.png" width="600">


###### *[Back to the Top](#toc)*

## Setup New LPAR and restore the Mksysb<a name="setup-new-lpar-and-restore-the-mksysb"></a>

1.  From the Skytap dashboard, add a new LPAR in your existing
    Environment.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image41.png" width="700">

2.  Select Templates and choose any AIX template and add VM.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image42.png" width="700">

3.  Edit the setting on VM and Set Hostname and IP.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image43.png" width="700">

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image44.png" width="700">

4.  From hardware setting assign the disks as per Source LPAR rootvg.

4a.  Delete the existing disk and add new disk.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image45.png" width="700">

4b.  Add new disk.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image46.png" width="700">

4c.  Update the CPU/RAM if required and start the LPAR and open the console.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image47.png" width="700">

Note: System will enter in SMS menu.

5.  Set up NIC/Ethernet to boot using NIM server using the below
    sequence.

    a.  \" 2 - Setup Remote IPL\"

    b.  \" 1 - Interpartition Logical LAN\"

    c.  \" 1 - IPv4 -- Address Format\"

    d.  \" 1 - BOOTP\"

    e.  \" 1 -- IP Parameters\"

6.  Configure the IPS to match the IP as shown below.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image48.png" width="600">

7.  Press esc to return to previous Menu and run "3 - Ping Test" -\> press 1 and enter to execute.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image49.png" width="600">

8.  On successful ping press enter and return to main menu using "M".

9.  Press "X" to exit and server should start boot using the LAN adapter.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image50.png" width="600">

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image51.png" width="600">

10. It will take approximately 10 to 20 mins. to restore Mksysb and server will reboot with the Mksysb restored.

#### Next steps<a name="nextsteps"></a>

**Main Overview**
> [Skytap Well-Architected Framework](../../README.md)

**Operational Excellence**
>[Skytap Operational Excellence Pillar](../../operations/README.md)

**Resiliency**
>[Skytap Resiliency Pillar](../README.md)
>* [Migration](../migrations.md)
>* [Protection](../backups.md)
>* [Disaster Recovery](../disasterrecovery.md)
>* [High Availability](../ibmihadr.md)
>
>**Migration Solutions**
>* [Cold (Warm) Migrations (Backup and Restore)](./ColdMigrationsOverview.md)
>* [Hot Migrations (Replication Sync)](./HotMigrationOverview.md)
>
>**Design**
>* [Design Considerations for Azure](../designconsiderationsazure.md)
>* [Design Considerations for IBM Cloud](../designconsiderationsibm.md)

**Security**
> * [Skytap Security Pillar](../../security/README.md)
