---
Title: Mksysb Backup and Restore in Skytap
Description: Skytap Cold Migration Solution - Backup and Restore to Skytap on Azure using Mksysb.
Authors: Chris Eigbrett - Director, Skytap Service Delivery, Abhishek Jain – Cloud Solution Architect, Matthew Romero - Technical Product Marketing Manager
permalink: /resiliency/solutions/mksysb-backupandrestore/
---
# Backup and Restore to {{site.SoA}} using Mksysb

This guide is provided “as-is”. Information and views expressed in this document, including URL and other Internet website references, may change without notice and usage of the included material assumes this risk.

This document does not provide you with any legal rights to any intellectual property in any product. You may copy and use this document for your internal, reference purposes.

<a name="toc"></a>

# Table of Contents

* [Prechecks](#prechecks)
* [Backing up On-Prem System using Mksysb Backup Execution](#backing-up-on-prem-system-using-mksysb-backup-execution)
* [Transferring Mksysb to {{site.Brand}}](#transferring-mksysb-to-skytap)
  * [Option 1 - Direct Connectivity](#option-1---direct-connectivity)
  * [Option 2 - Published Service](#option-2---published-service)
  * [Option 3 - Azure Blob](#option-3---azure-blob)
* [Restore In {{site.Brand}}](#restore-in-skytap)
  * [Initiate a NIM server in {{site.Brand}}](#initiate-a-nim-server-in-skytap)
<!---  * [Configure NIM Server](#configure-nim-server)--->
  * [Set up Client on NIM Server to Restore Mksysb](#set-up-client-on-nim-server-to-restore-mksysb)
* [Setup New LPAR and restore the Mksysb](#setup-new-lpar-and-restore-the-mksysb)
* [Savevg Backup and Restore in {{site.Brand}}](#savevg-backup-and-restore-in-skytap)

<a name="prechecks"></a>

#  Prechecks

1. Error logs for any OS level issues.
1. Check Software inconsistencies using \# lppchk -v.
1. Resolve any operating system level issue to take a healthy system backup.
1. Enough free space to backup all files in a filesystem.

<a name="backing-up-on-prem-system-using-mksysb-backup-execution"></a>

# Backing up On-Prem System using Mksysb  Backup Execution

1. Populate the /tmp/exclude.rootvg file for excluding the file systems from the backup.
1. Use command \# mksysb -ipX /\<FS>/\<hostname>.mksysb.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image2.png)){}

Note: It may take some time to complete the backup

1. Verify content of mksysb using \# lsmksysb -lf > /\<FS>/\<hostname>.mksysb.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image3.png)){}

<a name="transferring-mksysb-to-skytap"></a>

## Transferring Mksysb to {{site.Brand}}

<a name="option-1---direct-connectivity"></a>

### Option 1 - Direct Connectivity

**Pros:**

* VPN or ExpressRoute
* Network planning is required ahead of time
* Secured and fast data transfer
* Future proof

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image4.png)){}

<a name="option-2---published-service"></a>

### Option 2 - Published Service

**Pros:**

* Fastest and cheapest to deploy
* Native {{site.Brand}} support
* Only the NIM server in {{site.Brand}} is required to start data transfer
* Encrypted data transfer over public Internet
* Only recommended for proof-of-concept

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image5.png)){}

<a name="option-3---azure-blob"></a>

### Option 3 - Azure Blob

**Pros:**

* Azure Blob Storage
* Secured and fast data transfer
* Additional VM for AZ copy required
* Future proof
* Data can be copied over even before {{site.Brand}} service is started

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image6.png)){}

<a name="restore-in-skytap"></a>

# Restore In {{site.Brand}}

<a name="initiate-a-nim-server-in-skytap"></a>

## Initiate a NIM server in {{site.Brand}}

1. Deploy a new NIM AIX template using public templates (latest AIX level recommended).

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image7a.png)){}

1. Rename the LPAR to NIM server.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image8.png)){}

1. Set up the desired network and attach the network to the LPAR.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image9.png)){}

1. Set the desired hostname in Adapter setting.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image10.png)){}
    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image11.png)){}

1. Power on the LPAR and logon with root access.

<!--- ### Configure NIM Server<a name="configure-nim-server"></a>

1. Check if required file sets are Installed \# lslpp -l \| grep -i nim.

1. Attach Install ISO image to the server.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image12.png">

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image13.png">

1. Run #cfgmgr in OS to configure CDROM.

1. List CDROM to confirm \# lsdev -Cc cdrom.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image14.png">

1. Install file sets from CDROM \# smitty install_latest.

    a. Select CDROM using F4 or esc+4 keys.

    b. Search for NIM in software to install.

    c. Select NIM master using F7 or esc+7 keys.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image15.png">

d. Continue installation.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image16.png">

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image17.png">

1. Exit installation menu using F1. or esc+0.

1. Confirm installation is done \# lslpp -l \| grep -i nim.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image18.png">

1. Set Hostname and /etc/hosts to exact name as the hostname used when configuring the LAPRs Network Adapter settings.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image19.png">

1. Run NIM configuration \# smitty nim_config_env.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image20.png">

10. Press Enter to start. Note: this step will take approximately 1. mins. to complete.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image21.png">

11. Congratulations, your NIM Server is ready!

###### *[Back to the Top](#toc)*
--->
<a name="set-up-client-on-nim-server-to-restore-mksysb"></a>

### Set up Client on NIM Server to Restore Mksysb

1. Set Hostname and /etc/hosts to exact name as the hostname used when configuring the LAPRs Network Adapter settings.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image19.png)){}

1. Update the /etc/hosts file with the client hostname.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image22.png)){}

1. Verify hostname is resolved.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image23.png)){}

1. Add the Client in NIM config \# smitty nim_mkmac and enter the client hostname.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image24.png)){}

1. Change the setting as shown below and press Enter.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image25.png)){}

1. NIM Client is defined and can be verified in \# lsnimn nimclient.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image26.png)){}

1. Copy the Source Mksysb in a new filesystem /export/mksysb.

1. Create the filesystem \# crfs -v jfs2 -m /export/mksysb -A > yes -a size=10G -r rootvg.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image27.png)){}

1. List the mksysb file content lsmksysb -lf.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image28.png)){}

1. Create the two NIM resources required for restoration.

    1. **Mksysb resource**

        1. Mksysb - \# smitty nim_res > define a resource and select mksysb and press enter.

            ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image29.png)){}

        1. Fill in the Name, Server of resource and location of  resource (absolute path for location of the mksysb file).

            ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image30.png)){}

    1. **Spot resource**

        1. Define spot from mksysb resource \# smitty nim_res -\> define a resource select spot and press enter (make sure > you have enough  space in /export/spot filesystem \~2 > GB).

            ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image31.png)){}

        1. Enter **Name**, **Server of Resource**, **Source of Install images** and **Location** (absolute Path for the resource "/export/spot sapaix_spot").

            ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image32.png)){}

        1. Creation of spot will take some time—approximately 15 to 30 minutes.

            ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image33.png)){}

1. Assign resources to nimclient machine: \# smitty nim_mac_res.

1. Allocate Network Install resources and select **nimclient** from the list and press Enter.

1. Use F7 or Esc+7 to select the **sapmksysb** and **sapaix_spot** and press Enter.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image34.png)){}

1. Command output will be as below. Exit using F10 or Esc+0.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image35.png)){}

1. Enable Bos installation for the client \# smitty nim_mac_op nimclient.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image36.png)){}

1. Select bos_inst and press Enter.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image37.png)){}

1. Select the options shown below for mksysb restore (Use arrow keys and F4  or Esc+4).

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image38.png)){}

1. The setup is now complete.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image39.png)){}

1. Verify the client Status \# lsnim -l nimclient.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image40.png)){}

<a name="setup-new-lpar-and-restore-the-mksysb"></a>

## Setup New LPAR and restore the Mksysb

1. From the {{site.Brand}} dashboard, add a new LPAR in your existing environment.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image41.png)){}

1. Select Templates and choose any AIX template and add VM.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image42.png)){}

1. Edit the setting on VM and Set Hostname and IP.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image43.png)){}

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image44.png)){}

1. From hardware setting assign the disks as per Source LPAR rootvg.

    1. Delete the existing disk and add new disk.

        ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image45.png)){}

    1. Add new disk.

        ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image46.png)){}

    1. Update the CPU/RAM if required and start the LPAR and open the console.

        ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image47.png)){}

        Note: System will enter in SMS menu.

1. Set up NIC/Ethernet to boot using NIM server using the below sequence.

    1. **2. Setup Remote IPL**
    1. **1. Interpartition Logical LAN**
    1. **1. IPv4 -- Address Format**
    1. **1. BOOTP**
    1. **1. IP Parameters**

1. Configure the IPS to match the IP as shown below.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image48.png)){}

1. Press esc to return to previous Menu and run "3 - Ping Test" -\> press 1 and enter to execute.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image49.png)){}

1. On successful ping press enter and return to main menu using "M".

1. Press "X" to exit and server should start boot using the LAN adapter.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image50.png)){}

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/image51.png)){}

1. It will take approximately 10 to 20 mins. to restore Mksysb and server will reboot with the Mksysb restored.

## Savevg Backup and Restore in {{site.Brand}}

1. Backup and restore data volumes using savevg.

    1. Prechecks:

        * Minimize  active read/writing to disk to avoid file or database corruption.
        * Verify enough free space to backup volume group to your filesystem.

    1. Backup execution - verify volume group has desired volumes:

        * Run # lsvg and #lsvg - an app to review volumes to be backed up.

          ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/mk_img1.png)){}

        * Run backup to location with sufficient space with the command # savevg -r -f /tmp/backup/app.image app.

          ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/mk_img2.png)){}

    1. Copy backup to {{site.Brand}} LPAR (see copy methods above for mksysb).

    1. Prepare system in {{site.Brand}} to restore volumes. Note: if you want to shrink your volumes when restoring, you should pick smaller sizes that are still sufficient for the data in the volume group.

        * Identify physical volumes associated with the volume and their sizes.

          Run # lspv and # lspv hdisk1 to confirm the size in megabytes of any necessary disks.

          ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/mk_img3.png)){}

        * In {{site.Brand}}, add disks to your restore target of sufficient size.

    1. Shut down the restore LPAR in {{site.Brand}}, go to edit VM and add disks.

        ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/mk_img4.png)){}

    1. Pick how much storage you need and select “Save”.

        ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/mk_img5.png)){}

    1. Repeat for any additional physical disks you need to add.

    1. Start LPAR when disks added.

1. Run restores for savevg

    1. Identify the sizes of your disks to make sure you are restoring to the correct locations with # lspv and # bootinfo -s hdisk1.

        ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/mk_img6.png)){}

    1. Run # ls savevg -f app.image -l to confirm contents of savevg file.

        ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/mk_img7.png)){}

    1. Run restore # restvg -f app.image hdisk1 (add flag -s to restore with minimum size, you can specify multiple disks if desired).

        ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia/media/mk_img8.png)){}

    1. Finished.
