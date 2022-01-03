---
title: Utilizing Commvault in Skytap with IBM Power Workloads in Azure
---

Commvault may be used to migrate, protect and recover IBM i LPARs on
Skytap for Azure. This guide will walk you through all the steps
required to get up and running with Commvault in Skytap.

For the overall architecture, please refer to the sample reference
architecture shown below. In the example shown in *Figure 1*, a 1...n
set of IBM i LPARs reside on-premises in a Data Center facility. In the
example architecture, Commvault is utilized to backup the IBM i selected
LPAR data to Proxy Media Agents to be transferred to Azure Blob Storage.
The Commvault CommServer is controlling the orchestration and automation
of the backup within Azure. The data, once transferred, may then be
restored to Skytap for Azure, unchanged.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image1.png">

Figure 1: Sample Azure Reference Architecture

Setting Up Commvault Within a Skytap Environment

How to protect an IBM Power system in the Skytap environment.

**STEP 1: Staging Your Skytap Environment for Commvault**

1)  In your Skytap account, Provision an Environment starting with a
    Windows Server which will host the Commvault CommServer.

    a.  In Skytap, navigate to Environments, and Select *"+ New
        Environment"*

        i.  Search For Windows, and select "Windows Server 2019 Standard
            Sysprepped"

        ii. When selected, click "Create Environment" in upper right

    b.  Once your environment has been created, make the following
        hardware modifications to the Virtual Machine, via the Gear
        Wheel setting.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/protectionmedia/media2/image3.png">

i.  Increase Storage to 200GB Minimum

ii. Increase Memory to 16GB and CPU to 2

iii. Select an Appropriate Hostname, Such as "*commserver"*

```{=html}
<!-- -->
```
2)  Click Run to start the Windows Server.

    a.  Once the Machine has turned to a Green, running, status, go
        through each of the Sysprep options as appropriate.

    b.  When logged in, use the disk and storage settings to increase
        the size of the existing C drive or add new storage HDD layouts
        as needed. For more information on how to do this, click
        [here](https://docs.microsoft.com/en-us/windows-server/storage/disk-management/extend-a-basic-volume).

3)  Within the Skytap Environment, click Add VMs and select a Linux
    Operating System that will serve as the Commvault Media Agent Proxy
    within the environment. For the purpose of this guide, we will make
    reference to Ubuntu 20.04 running on POWER, but you can also select
    the 16.04 on POWER image as well. To support the backup of IBM i, a
    Linux OS for the Commvault Media Agent is required. \[For a list of
    supported operating systems, please refer to [Commvault Media Agent
    Requirements
    Guide](https://documentation.commvault.com/commvault/v11/article?p=2822_1.htm).\]

    a.  Add an additional disk on the LPAR by navigating via the "Gear
        Wheel" on the VM in the Skytap environment. Modifying the
        disk(s) must be done while the LPAR is powered down. [More
        information may be found
        here.](https://help.skytap.com/adding-and-extending-vm-disks.html)
        Once started, the method for adding a new disk and creating a
        partition will vary based on Operating System. For detailed
        steps for performing these actions on the Ubuntu for POWER
        image, you can refer to the following guide:

        i.  [Adding a new disk in
            Ubuntu](https://help.ubuntu.com/community/InstallingANewHardDrive).

    b.  Select an Appropriate Hostname, Such as "*mediaserver"*.

> the method for adding a new disk and creating a partition will vary
> based on Operating System. For detailed steps for performing these
> actions on the Ubuntu for POWER image, you can refer to the following
> guide:

i.  [Setting your Ubuntu
    Hostname](https://www.server-world.info/en/note?os=Ubuntu_21.04&p=hostname).

```{=html}
<!-- -->
```
c.  .

d.  

```{=html}
<!-- -->
```
4)  Within the Skytap Environment, click Add VMs and select an IBM i
    LPAR with any Skytap supported IBM i Operating System. \[Note:
    Modify the LPAR Hardware settings as needed. At a minimum, provide
    8GB of RAM to the IBM i LPAR.\]

5)  Click Run to start all 3 virtual machines / LPARs. Take special note
    of hostnames and IP addresses as they will be used extensively in
    the setup of Commvault.

6)  Fill out and Download the trial for Commvault "Complete Data
    Recovery" from Commvault's Website:

    a.  <https://www.commvault.com/trials>

7)  IF you are unfamiliar with Commvault, start with the Quick Start
    Guide:

    a.  <https://documentation.commvault.com/commvault/v11/article?p=1664.htm>

    b.  On the Windows Machine within the Environment, use the Link from
        the trial to download the Complete Data Protection offering from
        Commvault. Extract and install on the windows machine.

    c.  Once Commvault completes the install, there is both a Web
        Console interface, and a Java Console Interface available to
        perform the configuration required. Many of the steps in this
        guide are specific to the Java Console Interface directly within
        the Windows Server.

![Graphical user interface, text, application, email Description
automatically
generated](I:\Repos\Skytap\WAF\resiliency\protectionmedia\media2/media/image4.png){width="2.342676071741032in"
height="1.7592596237970253in"}

Figure 2: Commvault Java Interface

8)  Within the Java Client, Add the Linux Media Agent and Verify Network
    Connectivity.

    a.  Right-Click on Client Computers, and Select Add New Client.

    b.  Select Unix

    c.  Client Name is How the Machine will Appear in the GUI

    d.  Hostname is the IP Address of the Machine itself

![Graphical user interface, application Description automatically
generated](I:\Repos\Skytap\WAF\resiliency\protectionmedia\media2/media/image5.png){width="3.6203707349081364in"
height="2.7203062117235346in"}

Figure 3: Adding New Client

9)  Inside Commvault, configure a Plan for your data protection

    a.  <https://documentation.commvault.com/11.21/essential/92993_creating_base_plan.html>

    b.  Make Sure LINUX Media Agent Proxy Configured via the CommServ
        interface

    c.  On JAVA GUI (Commvault CommCell) -- For CentOS Proxy Server --
        Right Click -- Install Software -- File System for IBM i

    d.  {NB: There is a template Proxy Media Agent in PacRetail found
        [here](https://cloud.skytap.com/templates/2025247)}

    e.  Manage -- Servers -- Proxy Client -- Manual Association

    f.  **Make sure your work with your IBM Administrator when designing
        your Plan so that you are only protecting the IBM I server
        during authorized data protection windows.**

![](I:\Repos\Skytap\WAF\resiliency\protectionmedia\media2/media/image6.png){width="6.5in"
height="2.8881944444444443in"}

10) Prep the IBM I LPAR for Commvault

    a.  Ensure all networking is running, if not: *STRTCPSVR* -- 'G'

    b.  Ensure accessible for
        [Telnet](https://help.skytap.com/faq-ibmi.html).

    c.  Create a non-QSECOFR user that has \*SECOFR level. \[*CRTUSRPRF*
        -- 'CMV'\]

    d.  Ensure that WRKDIRE has the correct Address for created user
        \[*WRKDIRE*\]

11) ![A screenshot of a computer Description automatically generated
    with medium
    confidence](I:\Repos\Skytap\WAF\resiliency\protectionmedia\media2/media/image7.png){width="6.5in"
    height="3.0708333333333333in"}

    a.  Follow the exact steps found here to install the savf file on
        the IBM I LPAR. \[[Link to SAVF File in
        Assets](https://cloud.skytap.com/assets/462106)\]. Or, SAVF
        found in Software Cache on the Commserv install machine.

    b.  Follow the exact steps found here:
        <https://documentation.commvault.com/commvault/v11_sp20/article?p=16169.htm>

    c.  Once SAVF is installed, COMMVAULT START to kick it off.

12) Create an IBM I Client (Recommend JAVA GUI):

    a.  <https://documentation.commvault.com/11.21/essential/108599_ibm_i.html>

![Graphical user interface, application Description automatically
generated](I:\Repos\Skytap\WAF\resiliency\protectionmedia\media2/media/image8.png){width="6.5in"
height="2.8715277777777777in"}

\*\* When setting up IBM I -- ensure that the CMV user created above is
set on the setup screen. \*\* You also must setup the Proxy Server (Part
of step 4 above) for use with IBM I as part of the IBM I client
creation.

> ![Graphical user interface Description automatically
> generated](I:\Repos\Skytap\WAF\resiliency\protectionmedia\media2/media/image9.png){width="4.933228346456693in"
> height="4.343981846019248in"}

13) Click on the LPAR of your choice.

    a.  Check the status of the Overview screen to verify your settings

    b.  Check the status of the Configuration screen

    c.  Check with your IBM Administrator to see when a appropriate
        backup window exists to test the first backup. **Please note: A
        backup of the critical system components will reset the system
        to Restricted State and prevent software applications from
        operating. Do not run OS-level data protection operations
        outside of backup windows.**

    d.  **Make sure that you associate the Data Storage to each
        subclient for the LPAR. (Through Java GUI, right click
        properties on default FileSet or created Subclient file set).**

![Graphical user interface, application Description automatically
generated](I:\Repos\Skytap\WAF\resiliency\protectionmedia\media2/media/image10.png){width="4.342237532808399in"
height="3.838426290463692in"}

14) Familiarize yourself with the pre-defined user subclients:

![](I:\Repos\Skytap\WAF\resiliency\protectionmedia\media2/media/image11.png){width="6.5in"
height="1.5715277777777779in"}

a.  DRSubclient is not supported at this time. Backing up all subclients
    (but not DR Subclient) will backup the entirety of the LPAR but not
    the IBM I licensing. **PLEASE NOTE: Prior to the full backup, ensure
    PTFs are updated to Match the Target LPAR in Skytap -- OS version
    must also match.**

b.  All other subclients (**\*ALLDLO**, **\*ALLUSR**, **\*IBM**, **\*HST
    log**, and **\*LINK**) will use Save-while-active and not send the
    system into Restricted mode.

```{=html}
<!-- -->
```
15) Run a backup (Right click through Java GUI on FileSystem for LPAR)

    a.  <https://documentation.commvault.com/11.21/essential/108610_performing_ibm_i_file_system_backups.html>

    b.  Click on any of the subclients you wish to protect under the
        Actions tab and select Back up. If this is your first backup
        select 'Full' for your protection level.

    c.  You can monitor the status of the backups from the Jobs tab off
        of the primary Ribbon.

16) Once the backups have finished successfully, you can be confident
    that the automatic schedule as part of the Plan will run as expected
    and you will have regular good data protection operations.

Use case #2 -- Data Recovery using Web Interface (Can also be done
through Java GUI).

Bringing data back to an IBM I system in the cloud is very easy. Once
the data is stored on the Commvault back-end, it can be written back to
any of our systems with an IBMi client. This can be the same system the
data was on when it started, or another system in the same cloud, or a
system in a completely different cloud, or even back on-premises.

Full step-by-step instructions on how to recover data using Commvault
can be found by searching Commvault's documentation website located at:
<https://documentation.commvault.com/commvault/> and do a keyword search
for "IBMi Restore"

Within the Commvault GUI, Now that the data has been protected in the
cloud, you can begin the process of recovering the folders back into the
target guest:

1)  Open the Commvault Command center and browse to the IBMi machine
    that you have protected.

![](I:\Repos\Skytap\WAF\resiliency\protectionmedia\media2/media/image12.png){width="6.5in"
height="3.3194444444444446in"}

2)  Select the first of the subclients whose data you wish to recover.
    Drill inside and select Restore.

> ![](I:\Repos\Skytap\WAF\resiliency\protectionmedia\media2/media/image13.png){width="6.5in"
> height="3.4118055555555555in"}

3)  Select all of the data, and then target an IBMi machine to recover
    the data to. This machine must have a Commvault agent installed on
    it. If this is a disaster recovery or migration, deploy a fresh
    machine in Skytap from a template to be the target, and install the
    CommVault agent so that the machine is visible within the Commvault
    GUI

![](I:\Repos\Skytap\WAF\resiliency\protectionmedia\media2/media/image14.png){width="6.5in"
height="1.4944444444444445in"}

4)  Run the restore job. Target the destination machine.

![](I:\Repos\Skytap\WAF\resiliency\protectionmedia\media2/media/image15.png){width="3.9074070428696412in"
height="5.530170603674541in"}

5)  Repeat steps 2-4 until you have recovered each of the Commvault
    subclients (not including the DR subclient, which is for authoring
    DVDs)

Migration / DR use case: Now that the user data has been restored onto
the server, the customer can decide to keep the existing IP address and
hostname, or to reset the server to the identity of the original
machine.

a.  Manual steps are required to change the IP address and host name to
    match the original server. These steps are detailed in the IBM Power
    series guides. Users should also check the status of any private
    authorities that existed in the environment.
