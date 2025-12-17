---
Title: Utilizing Commvault in Skytap with IBM Power Workloads in Azure
Author: Matthew Romero, Technical Product Marketing Manager 
permalink: /resiliency/solutions/commvault
---

# {{page.title}}

This guide is provided “as-is”. Information and views expressed in this document, including URL and other Internet website references, may change without notice and usage of the included material assumes this risk.

This document does not provide you with any legal rights to any intellectual property in any product. You may copy and use this document for your internal, reference purposes.

<!-- 
# Table of Contents <a name="toc"></a>

* [Key Takeaways](#takeaways)
* [Setting Up Commvault Within Skytap](#setup)
  * [Create a new Skytap Environment](#createnewenv)
  * [Update the environment details](#updateenvdetails)
* [Deploying the Commvault software into the CommServer VM](#deploycommvault)
  * [Configuring the VM to run Commvault](#configurecommvaultvm)
  * [Installing Commvault Complete Data Recovery–Context Library Download]()
* .
-->

## Key Takeaways<a name="takeaways"></a>

Commvault may be used to migrate, protect, and recover IBM i LPARs on Skytap for Azure. This guide will walk you through all the steps required to get up and running with Commvault in Skytap.

Here is an overview of how {{site.SoA}} and Commvault work together
for AIX and IBM i (as400).

![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/migrationmedia/media/image8.png)

For the overall architecture, please refer to the sample reference architecture shown below. In the example shown in *Figure 1*, a 1--*n* set of IBM i LPARs resides on-premises in a data center facility. In the example architecture, Commvault is used to back up the IBM i selected LPAR data to Commvault proxy MediaAgents to be transferred to Azure Blob Storage. The Commvault CommServer controls the orchestration and automation of the backup within Azure. The data, once transferred, may then be restored to Skytap for Azure, unchanged.
<hr>

![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image1.png)

*Figure 1: Sample Azure Reference Architecture*

<hr>

# Setting Up Commvault Within Skytap to protect an IBM Power system in the Skytap environments.<a name="setup"></a>

### Staging Your Skytap Environment for Commvault<a name="step1"></a>

#### Create a new Skytap Environment<a name="createnewenv"></a>

1. Navigate to [cloud.skytap.com](https://cloud.skytap.com). The Skytap Dashboard page will then display.
1. Once you have signed into the portal with your **Skytap account**, click the **Environments** tab at the top of the window. The **Manage Environments and VMs** page will then display.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image3.png)

1. Provision an **Environment** by clicking the **(+) New Environment** button ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image4.png) on the upper right-hand side of the window. The **Create a new environment** dialogue will display.
1. Search for and select "**Windows Server 2019 Standard Sysprepped**" from the search field.

    <p style="background-position: 5px 5px;border-radius: 4px;padding: 6px 6px 6px 34px!important;margin: 10px!important;line-height: 1.4em;border: 1px solid #BBD7E7!important;background: #F9FDFF url('/_images/help-icn-info.png') no-repeat;">This virtual machine will host the Commvault CommServer and is the first step in our infrastructure.</p>

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image5.png)

1. In the upper right of the **Create a new Environment** window, click the **Create** Environment button ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image6.png).

#### Update the environment details<a name="updateenvdetails"></a>

1. Click ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image7.png) **(Edit environment information)** that is next to the environment title **Windows Server 2019 Standard Sysprepped** at the top of the environment details window.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image8.png)

    The **Edit environment** dialogue displays.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image9.png)

1. From within the **Edit environment** dialogue, type a new **Environment Name**, such as **Skytap & Commvault Solution Infrastructure**, and then click the **Save** button ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image10.png)

    _**Note** After the environment is created, make the following hardware modifications to the VM to ensure stability and performance._

1. From the Machine pool window, inside the **Windows Server 2019 Standard** VM tile, click the **Edit VM Settings icon** ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image11.png).

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image12.png)

    The **VM Settings** screen displays.

1. From the **VM Settings**, click the **Hardware** tab. The **Hardware** settings page displays.
1. In the **Compute** section, apply the following settings:

    1. Under **RAM**, click ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image7.png)(**Edit**), and choose **16 GB** from the drop-down menu.
    1. Click the **vCPUs** drop-down menu and choose **4**.
    1. Click the adjacent core configuration drop-down menu and choose **2 sockets x 2 cores per socket**.

        ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image13.png)

1. In the **Storage** section, perform the following steps:

    1. Next to the existing disk, click the **Edit** (**Pencil icon**) ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image14.png). The storage section expands.
    1. In the **Disk size** field, type **200 GB**, and then click the **Save** button.

        ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image10.png)

        ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image15.png)

1. At the top of the **VM Settings** window, click the **\< Back** link to return to the environment details page.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image16.png)

1. From the environment details, from the VM pool, click  ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image7.png) next to the **Windows Server 2019 Standard** VM tile.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image17.png) 

    The **Rename VM** dialogue displays.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image18.png)

1. Type a new **VM name**, such as **Windows Server 2019 Standard  - Commvault Server**, and then click the **Save** button ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image10.png).

![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image19.png)

## Deploying the Commvault software into the CommServer VM.<a name="deploycommvault"></a>

### Configuring the VM to run Commvault<a name="configurecommvaultvm"></a>

1. On the **Windows Server 2019 Standard - Commvault Server** VM tile in the Environment details page, click **Run** ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image20.png) to start the Windows Server.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image21.png)

1. After the VM has turned to a **Green** and **Running** status, click the VM tile to open a Secure Remote Access (SRA) client view.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image22.png)

1. Complete the Windows Sysprep setup steps. After the VM reboots, sign in.
1. Rename the VM.

    1. Click **Start** > **Settings** > **System** > **About**. The **About** window displays.
    1. Click **Rename this PC**. The **Rename your PC** dialogue displays.
    1. Type a new name for the PC, such as **CommvaultServer.**
    1. Click **Next**, and then click **Restart now**.

1. Resize the Windows boot volume to use the extra space you allocated earlier.

    _**Note** For detailed instructions, see [Extend a basic volume](https://docs.microsoft.com/en-us/windows-server/storage/disk-management/extend-a-basic-volume)._

### Installing Commvault Complete Data Recovery–Context Library Download

1. From the SRA client view of the Windows Server VM, open a web browser to: [Commvault free trial](https://www.commvault.com/trials).
1. Click **Commvault Complete™ Data Protection 30-Day Trial Offer**.
1. Complete Fill out and download the trial for **Commvault Complete™ Data Protection** from Commvault's Website:

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image23.png)

1. Start the Windows Commvault Server installation.
1. From the Windows Commvault Server installation, on the **Choose the installation type** screen, select the **Create a custom package to install on a different computer** option to download **Context Library** and click the **\>** arrow to proceed to the next step.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image24.png)

1. On the **Select operating system Type** screen, check **ALL** the checkboxes and click the **\>** arrow to proceed to the next step.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image25.png)

1. On the **Custom Package Install Type** screen, select the **New Installations** option and click the **\>** arrow to proceed to the next step.
1. On the **Select Package Option** screen, select the **All Packages** option, as well as checking the **Include MS SQL Server** option and then click the **\>** arrow to proceed to the next step.
1. Use the **Default settings** and click the **\>** arrow to proceed to the next step.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image26.png)

1. **On the TAR file destination** screen, use the **default settings** to **not** include a TAR file for Unix Packages, and click the **\>** arrow to proceed to the next step.
1. Create a **Windows Share** from the **C:\\DownloadPackageLocation** folder where the installer is downloading the installer files for the Context Library.

_**Note** For detailed steps, see the following guide: [How to Create Shared Folders In Windows Server 2019](https://www.c-sharpcorner.com/article/create-shared-folders-in-windows-server-2016-computer-management/#:~:text=Introduction%201%20First%20step%20in%20this%20process%20would,Wizard%20%E2%80%94%3E%20Click%20Next.%20...%20More%20items...%20)._

#### Installing the CommServe Tools

1. Once the installer finishes creating the Context Library, deploy CommServer Software on the Windows 2019 Server by re-launching the installer file.
1. From within the Commvault installer, on the **Choose the installation type** screen, select the **Install packages on this computer** option then click the **\>** arrow to proceed to the next step.
1. On the **Installation Path** screen, select the **default settings**, then click the **\>** arrow to proceed to the installation.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image27.png)

1. Once the installation completes, open the CommServe Admin Console (should open automatically).
1. Create a new account using the same credentials that were used for signing up for the trial.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image28.png)

1. Log in to the Admin Console with the credentials that you created.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image29.png)

1. **Skip** the initial guided setup, as we will proceed with those steps after you have added the MediaAgent Server.

#### Configuring the Update Context Library Location

Library Update Context Library Location (as tool default location does not match where the tool actually downloads it).

1. Open the **Commvault CommCell Console**.
1. Login with the credentials you created when you opened the Admin Console.
1. Click the **Tools** tab at the top of the console.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image30.png)

1. Click the **Add/Remove Software** icon to open the software menu.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image31.png)

1. Select the **Software Cache Configuration** option.
1. Within the Software Cache Configuration pop-up, update the **Cache Directory** to the directory where all the software was downloaded earlier. (The default is **c:\\DownloadPackageLocation**).

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image32.png)

1. Click **Commit Cache**, then click **OK** on the pop-up when the operation completes, then click **OK** again to close the **Software Cache Configuration** pop-up.
1. Reboot the Server.

### Deploying the Commvault MediaAgent Proxy on an Ubuntu x86 VM as a Decoupled install and connecting it to the CommServer

Our next step is from within the Skytap environment, where we will add a Linux operating system VM that will serve as the Commvault MediaAgent Proxy within the environment.

For the purpose of this guide, we will make occasional reference to Ubuntu 20.04 running on POWER, but it is recommended that you use any of the other Ubuntu x86 VM images, such as **Ubuntu 18.04 LTS Server 64-bit–Firstboot**. These x86 VM images are all fully supported–including deduplication, which is not supported under POWER.

_**Note** To support the backup of IBM i, a Linux OS for the Commvault MediaAgent is required. For a list of supported operating systems, please refer to [Commvault MediaAgent Requirements Guide](https://documentation.commvault.com/commvault/v11/article?p=2822_1.htm)._

#### *Creating a VM to host the* Commvault MediaAgent Proxy

1. Starting from the Environment details window, within the Machine Pool, click the **(+) Add VMs** button.
1. From the **Add VMs** pop-up, at the top left click the **Templates** tab to open the available public templates.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image33.png)

1. Search for and select **Ubuntu 18.04 LTS Server 64-bit – Firstboot** from the **search field**.
1. Click the **Add VM(s)** button.
1. From the Machine pool window, inside the **Ubuntu 18.04 LTS Server 64-bit–Firstboot** machine object, click the **Gear Wheel** settings icon.
1. From the **VM Settings**, click the **Hardware** tab.
1. From the **Compute** section, under **RAM**, select the **Pencil icon** to open the **RAM dropdown**.
1. Select **16 GB** from the **RAM dropdown** menu, and click the **Save** button.
1. To add a disk on the LPAR, from the **Storage** section, click the **+ Add disk** link.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image34.png)

    _**Note** Modifying a disk must be done while the LPAR is powered down. See [Adding and extending virtual disks for a VM](https://help.skytap.com/adding-and-extending-vm-disks.html)._

1. Set the Disk Size to **200 GB** and click the **Save** button.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image35.png)

1. At the top of the **VM Settings** window, click the **\< Back** link to return to the Environment Details window.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image36.png)

1. From the Environment details, from the VM pool, click the **Pencil icon** next to the Ubuntu **Ubuntu 18.04 LTS Server 64-bit – Firstboot** machine name**.**
1. Under the **Rename VM** pop-up, update the VM name to something clear such as **Ubuntu 18.04 LTS Server - MediaAgent**, and click the **Save** button.
1. Inside the Environments details, from within the Machine pool window, inside the **Ubuntu 18.04 LTS Server - MediaAgent** machine object, click the **Run icon** to start the machine.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image37.png)

1. Once the Machine has turned to a **Green** and **Running** status, click the **VM** to open a remote console session with the machine.
1. When prompted proceed through each of the Sysprep options as appropriate.
1. Add the new 200GB disk to the machine.

_**Note** The method for adding a new disk and creating a partition will vary based on operating system.<br>For detailed steps for performing these actions on the Ubuntu image, you can refer to the following guide: [Adding a new disk in Ubuntu](https://help.ubuntu.com/community/InstallingANewHardDrive)._

1. Update the **Hostname** for the MediaAgent Server Hostname, Such as **UbuntuMediaServer**.

_**Note** The method for updating the hostname will vary based on operating system. For steps about setting the hostname for an Ubuntu image, see [Set Hostname](https://www.server-world.info/en/note?os=Ubuntu_21.04&p=hostname)._

1. Reboot the Ubuntu Server and reconnect.
1. Mount the **Windows share** to install Commvault MediaAgent Proxy software in disconnected mode.

_**Note** The method for mounting a Windows share will vary based on operating system.<br>For detailed steps for performing these actions on the Ubuntu image, you can refer to the following guide: [Mounting a Windows share in Ubuntu](https://www.wikihow.com/Mount-a-Windows-Share-on-an-Ubuntu-Server#:~:text=Mount%20Windows%20Share%20on%20Ubuntu%201%20Install%20samba,and%20exit.%206%20...%20%28more%20items%29%20See%20More)._

1. Install the Commvault MediaAgent Proxy software by navigating to the mounted Windows share location.
1. Use the **ls** command to list the contents of the installer location.

![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image38.png)

1. Use the following command to enter the Unix/Linux installer file location:

> **cd Unix**

1. Start the Unix installer by entering the following command:

> **./cvpkgadd**

1. From the Commvault installer, on the **OEM Selection** screen select **Commvault**, and then select **Next**.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image39.png)

1. On the **Install Task** screen, select the **Install packages on this machine** option, and then select **Next**.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image40.png)

1. On the Package Selection screen, select **File System Core**, **File System**, **MediaAgent**, and **File System for IBM I**, then select **Next**.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image41.png)

1. On the **Install Directory** screen, update the **Installation Directory** to match your new disk, and select **Next** to continue.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image42.png)

1. On the **Log Directory** screen, update the **Log Directory** to the same location you used for your **Installation Directory**, and select **Next** to continue.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image43.png)

1. On the **Laptop & Desktop Backup** screen, use the **default settings** to **not** configure for laptop and desktop backups. (you can change this setting later).
1. On the **Unix Group Assignment** screen, use the **default settings** to **not** assign a dedicated Unix Group. (you can change this setting later).
1. On the **Permission Details** screen, use the **default settings**. (you can change this setting later).
1. On the **Client Host Information** screen, update the **Client/Physical machine host name** to match the machine\'s IP. (i.e. **10.0.0.2**) and select **Next** to continue.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image44.png)

1. On the **Client Information** screen, update the **Client name** to match the machine\'s name. (ie **UbuntuMediaAgent**) and select **Next** to continue.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image45.png)

1. On the **Install Agents for Restore Only** screen, use the **default    settings** and select **Next** to continue.
1. On the Summary screen, select **Next** to continue.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image46.png)

    Once the packages are installed, the configuration utility will start.

1. From the **Configuration utility**, on the **Server Information** screen, leave the field **blank** and then select **Next** to start the configuration of the agent as a **Decoupled** install.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image47.png)

1. On the **Decoupled Install** screen, select **Yes** and then select **Next** to proceed with the decoupled install.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image48.png)

1. On the Installation Status screen, select Finish to exit the installation and configuration utility.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image49.png)

1. Returning to the Windows Server VM, open the CommCell Console.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image50.png)

1. Right-Click on **Client Computers**, and select **New Client**.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image51.png)

1. From the **Add New Client** pop-up, select **Unix**.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image52.png)

1. In the **New Unix Client** pop-up, under the **Configure Client** screen, Update the **Client Name** with the name of the MediaAgent Server (ex: **UbuntuMediaAgent**)
1. Update the **Host Name** with the **IP Address** of the MediaAgent. (ex **10.0.0.2**)
1. Check the checkbox for **Fetch the configuration information from the client that is already installed on decoupled mode** and click **Next**.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image53.png)

1. In the **New Unix Client** pop-up, under the **Optional Data** screen, Use the default settings and click **Next**.
1. In the **New Unix Client** pop-up, under the **Summary** screen, click **Finish** to add the MediaAgent.
1. Close the **Registration Status** once the MediaAgent has been connected.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image54.png)

1. Close the **Add New Client** pop-up.

### Adding the Ubuntu MediaAgent Proxy Storage to the CommServer

1. From the **Commcell Console**, click **Getting Started** icon at the top.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image55.png)

1. From the **Getting Started** window, under **(1) Initial Setup**, click **Configure Storage**.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image56.png)

1. From the **Initial Setup: Configure Storage** window, under **(2) Configure Storage Devices**, click **Add Disk Storage**.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image57.png)

1. In the **Add Disk Library** pop-up, set the **Name** (choose something unambiguous, such as **Backups**), ensure that the selected **MediaAgent** is the **UbuntuMediaServer**, then set the **Local Path** to the path to the added 200GB Ubuntu disk is located (ex **/media/backupdata**), and then click **OK**.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image58.png)

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image59.png)

### Configure the backup plan on the Commvault Server

1. Inside Commvault, configure a plan for your data protection

> **Make sure your work with your IBM Administrator when designing your Plan so that you are only protecting the IBM I server during authorized data protection windows.**

![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image60.png)

#### Create a CommServe Disaster Recovery Policy/Plan

1. From the **CommCell Console**, click the **Getting Started** icon at the top.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image55.png)

1. From the **Getting Started** window, under **(1) Initial Setup**, select the **Configure Policies** link.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image61.png)

1. From the **Initial Setup :: Configure Policies** window, under **(1) Configure Storage Policies**, select the **Add Storage Policy** link.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image62.png)

1. In the **Create Storage Policy Wizard** pop-up, select the **Storage Policy Type** to **CommServe Disaster Recovery Backup**, then click **Next**.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image63.png)

1. In the **Create Storage Policy Wizard** pop-up, set the **Storage Policy Name** to something clear such as **CommServe DR Backup**, then click **Next**.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image64.png)

1. In the **Create Storage Policy Wizard** pop-up, select the **Library for Primary Copy** to the Library you created when the MediaAgents storage was added (ex: **Backups**), then click **Next**.
1. Click **Yes** to the warning about DR policies to disk.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image65.png)

1. In the **Create Storage Policy Wizard** pop-up, set the **MediaAgent** to the Ubuntu MediaAgent (ex: **UbuntuMediaAgent**), then click **Next**.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image66.png)

1. In the **Create Storage Policy Wizard** pop-up, use the **default settings** on the **Enter the streams and retention criteria** screen, then click **Next**.
1. In the **Create Storage Policy Wizard** pop-up, you can choose to encrypt your data on the **Advanced settings for the primary copy** screen, then click **Next**.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image67.png)

1. In the **Create Storage Policy Wizard** pop-up, on the **Review your selections** screen, click **Finish**.

#### Create a Data Protection and Archiving Policy/Plan

1. From the **Initial Setup :: Configure Policies** window, under **(1) Configure Storage Policies**, select the **Add Storage Policy** link.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image62.png)

1. In the **Create Storage Policy Wizard** pop-up, select the **Storage Policy Type** to **Data Protection and Archiving**, then click **Next**.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image68.png)

1. In the **Create Storage Policy Wizard** pop-up, set the **Storage Policy Name** to something clear such as **DataProtection**, then click **Next**.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image69.png)

1. In the **Create Storage Policy Wizard** pop-up, select the **Library for Primary Copy** to the Library you created when the MediaAgents storage was added (ex: **Backups**), then click **Next**.
1. In the **Create Storage Policy Wizard** pop-up, set the **MediaAgent** to the Ubuntu MediaAgent (ex: **UbuntuMediaAgent**), then click **Next**.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image66.png)

1. In the **Create Storage Policy Wizard** pop-up, use the **default settings** on the **Enter the streams and retention criteria** screen, then click **Next**.
1. In the **Create Storage Policy Wizard** pop-up, on the **Enable deduplication on Storage policy** screen, select **Enable Deduplication** checkbox, and set the **Number of Partitions** to **1**, then set the **MediaAgent and Partition Path** to the mount point of the 200GB disk you added to the Ubuntu MediaAgent machine –(ex: **UbuntuMediaServer /media/backups**), then click **Next**.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image70.png)

1. In the **Create Storage Policy Wizard** pop-up, you can choose to encrypt your data on the **Advanced settings for the primary copy** screen, then click **Next**.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image71.png)

1. In the **Create Storage Policy Wizard** pop-up, on the **Review your selections** screen, click **Finish**.

### Deploying the IBM I workload machine, and connecting it to the Commvault Server

Our next step is from within the Skytap environment, where we will add an IBM i LPAR that will serve as the Workload within the environment.

1. Starting from the Environment details window, within the Machine Pool, click the **(+) Add VMs** button.
1. From the **Add VMs** pop-up, at the top left click the **Templates** tab to open the available public templates.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image72.png)

1. Search for **IBM i** with the **search field**.
1. Select an IBM i LPAR image. (ex: **IBM i 7.4**)
1. Click the **Add VM(s)** button.
1. From the Machine pool window, inside the **IBM i 7.4** machine object, click the **Gear Wheel** settings icon.
1. From the **VM Settings**, click the **Hardware** tab.
1. From the **Compute** section, under **RAM**, select the **Pencil icon** to open the **RAM dropdown**.
1. Select **8 GB** from the **RAM dropdown** menu, and click the **Save** button.

    _**Note** Modify the LPAR Hardware settings as needed. At a minimum, provide 8GB of RAM to the IBM i LPAR._

10. At the top of the **VM Settings** window, click the **\< Back** link to return to the Environment Details window.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image36.png)

1. From the Environment details, from the VM pool, click the **Pencil icon** next to the **IBM i 7.4** machine name**.**
1. Under the **Rename VM** pop-up, update the VM name to something clear such as **IBM i 7.4–Workload 01**, and click the **Save** button.
1. Inside the Environments details, from within the Machine pool window, inside the **IBM i 7.4–Workload 01** machine object, click the **Run icon** to start the machine.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image73.png)

1. Once the Machine has turned to a green running status, click the VM to open a remote console session with the machine.
1. When prompted proceed through each of the Sysprep options as appropriate.
1. Prep the IBM I LPAR for Commvault.

    1. Ensure all networking is running, if not: **STRTCPSVR**–'G'
    1. Ensure accessible for [IBM i FAQ, Networking](https://help.skytap.com/faq-ibmi.html).
    1. Create a non-QSECOFR user that has \*SECOFR level. **CRTUSRPRF –'CMV'**
    1. Ensure that WRKDIRE has the correct Address for created user **WRKDIRE**

        ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image74.png)

    1. Follow the exact steps found here to install the savf file on the IBM I LPAR. \[[Link to SAVF File in Assets](https://cloud.skytap.com/assets/462106). Or, SAVF found in Software Cache on the Commserv install machine.
    1. Follow the exact steps found here: [https://documentation.commvault.com/commvault/v11_sp20/article?p=16169.htm](https://documentation.commvault.com/commvault/v11_sp20/article?p=16169.htm).
    1. Once SAVF is installed, COMMVAULT START to kick it off.

1. Returning to the Windows Server VM, open the CommCell Console.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image75.png)

1. Right-Click on **Client Computers**, and select **New Client**.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image76.png)

1. From the **Add New Client** pop-up, select **IBM i**.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image52.png)

    _**Note** When setting up IBM i–ensure that the CMV user created above is set on the setup screen,<br>You also must set up the proxy server (part of step 4 above) for use with IBM i as part of the IBM i client creation._

1. In the **New iSeries Client Configuration Dialog** pop-up, under the **General** screen, Update the **Client Name** with the name of the IBM i Server (ex: **IBMiWorkload01**)
1. Update the **Host Name** with the **IP Address** of the MediaAgent. (ex **10.0.0.3**)
1. Update the **IBMi User Credential** to your IBM i non-QSECOFR environment user and click **OK**.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image77.png)

1. In the **New iSeries Client Configuration Dialog** pop-up, under the **Optional Data** screen, use the default settings and click **Next**.
1. In the **New iSeries Client Configuration Dialog** pop-up, under the **Summary** screen, click Finish to add the IBM i Workload machine.
1. Close the **Registration Status** once the IBM i machine has been connected.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image54.png)

1. Close the **Add New Client** pop-up.
1. Click on the LPAR of your choice.

    1. Check the status of the Overview screen to verify your settings.
    1. Check the status of the Configuration screen.
    1. Check with your IBM Administrator to see when an appropriate backup window exists to test the first backup.

        **Please note: A backup of the critical system components will reset the system to Restricted State and prevent software applications from operating.**

        **Do not run OS-level data protection operations outside of backup windows.**
    1. **Make sure that you associate the Data Storage to each subclient for the LPAR. (Through Java GUI, right-click properties on default FileSet or created Subclient file set).**

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image78.png)

Familiarize yourself with the pre-defined user sub-clients:

![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image79.png)

DRSubclient is not supported at this time. Backing up all sub-clients (but not DR Subclient) will backup the entirety of the LPAR but not the IBM I licensing. **PLEASE NOTE: Before the full backup, ensure PTFs are updated to Match the Target LPAR in Skytap–OS version must also match.**

All other sub-clients (**\*ALLDLO**, **\*ALLUSR**, **\*IBM**, **\*HST log**, and **\*LINK**) will use Save-while-active and not send the system into Restricted mode.

### Run a backup (Right click through Java GUI on FileSystem for LPAR)

1. [Performing IBM i File System Backups](https://documentation.commvault.com/11.21/essential/108610_performing_ibm_i_file_system_backups.html).
1. Click on any of the sub-clients you wish to protect under the Actions tab and select Back up. If this is your first backup select 'Full' for your protection level.
1. You can monitor the status of the backups from the Jobs tab off of the primary Ribbon.

1. Once the backups have finished successfully, you can be confident that the automatic schedule as part of the Plan will run as expected and you will have regular good data protection operations.

Use case #2–Data Recovery using Web Interface (Can also be done through Java GUI).

Bringing data back to an IBM I system in the cloud is very easy. Once the data is stored on the Commvault back-end, it can be written back to any of our systems with an IBMi client. This can be the same system the data was on when it started, or another system in the same cloud, or a system in a completely different cloud, or even back on-premises.

Full step-by-step instructions on how to recover data using Commvault can be found by searching Commvault's documentation website located at: [Commvault Documentation](https://documentation.commvault.com/commvault/) and search for "IBMi Restore"

Within the Commvault GUI, Now that the data has been protected in the cloud, you can begin the process of recovering the folders back into the target guest:

1. Open the Commvault Command center and browse to the IBM i machine that you have protected.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image80.png)

1. Select the first of the sub-clients whose data you wish to recover. Drill inside and select Restore.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image81.png)

1. Select all of the data, and then target an IBM i machine to recover the data to. This machine must have a Commvault agent installed on it. If this is disaster recovery or migration, deploy a fresh machine in Skytap from a template to be the target, and install the CommVault agent so that the machine is visible within the Commvault GUI.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image82.png)

1. Run the restore job. Target the destination machine.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image83.png)

1. Repeat steps 2-4 until you have recovered each of the Commvault subclients (not including the DR subclient, which is for authoring DVDs)

##### Migration/DR use case: 
Now that the user data has been restored onto the server, the customer can decide to keep the existing IP address and hostname, or to reset the server to the identity of the original machine.

_**Note** Manual steps are required to change the IP address and hostname to match the original server. These steps are detailed in the IBM Power series guides. Users should also check the status of any private authorities that existed in the environment._
