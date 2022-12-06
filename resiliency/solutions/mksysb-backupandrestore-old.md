---
Title: Mksysb Backup and Restore in Skytap

Description: Skytap Cold Migration Solution - Backup and Restore to Skytap on Azure using Mksysb.
Authors: Chris Eigbrett - Director, Skytap Service Delivery
---
# Backup and Restore to Skytap on Azure using Mksysb
This guide is provided “as-is”. Information and views expressed in this document, including URL and other Internet website references, may change without notice and usage of the included material assumes this risk.

This document does not provide you with any legal rights to any intellectual property in any product. You may copy and use this document for your internal, reference purposes.

# Table of Contents <a name="toc"></a>

* [Introduction](#intro)
* [Key takeaways](#takeaways)
* [Prerequisites](#begin)
* 
* [Next Steps](#nextsteps)

1)  Backing up on-prem system using mksysb

    a.  Prechecks --

        -   Error logs for any OS level issues

        -   Check Software inconsistencies using \# lppchk -v

        -   Resolve any operating system level issue to take a healthy
            > system Backup

        -   Enough free space to backup all files in a filesystem

    b.  Backup execution

        -   Populate the /tmp/exclude.rootvg file for excluding the
            > filesystems from the backup

        -   Use command \# mksysb -ipX /\<FS>/\<hostname>.mksysb

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image51.png">
-   It may take some time to complete the backup

-   Verify content of mksysb using \# lsmksysb -lf
    > /\<FS>/\<hostname>.mksysb

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image43.png">

2)  Transferring Mksysb to Skytap

    a.  **Option 1 (Direct Connectivity**)

        -   VPN or ExpressRoute

        -   Network planning is required ahead of time

        -   Secured and Fast data transfer

        -   Future Proof

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image52.jpg">

b.  **Option 2 (Published Service)**

    -   Fastest and cheapest to deploy

    -   Native Skytap support

    -   Only Nim server in Skytap is required to started data transfer

    -   Encrypted Data Transfer over public Internet

    -   Only recommended for POC![Graphical user interface, diagram
<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image40.jpg">

c.  **Option 3 (Azure Blob)**

    -   Azure Blob Storage

    -   Secured and Fast data transfer

    -   Additional VM for AZ copy required

    -   Future Proof

    -   Data can be copied over even before Skytap service is Started

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image27.jpg">

3)  Restore In Skytap

    a.  Initiate a NIM server in Skytap

        -   Deploy a new AIX template using public templates (Latest AIX level Recommended)

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image15.png">

-   Rename the LPAR to NIM server

> ![Graphical user interface, application, Teams Description
> automatically
> generated]<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image25.png">"4.914623797025372in"
> height="3.364695975503062in"}

-   Setup the Desired Network and attach the network to the LPAR

> ![Graphical user interface, application Description automatically
> generated]<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image17.png">"3.2491240157480314in"
> height="2.4422583114610674in"}

-   Set the desired hostname in Adapter setting![Graphical user
    > interface, application Description automatically
    > generated]<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image45.png">"4.159675196850394in"
    > height="2.342899168853893in"}

-   

> ![Graphical user interface, application Description automatically
> generated]<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image54.png">"3.3400109361329835in"
> height="2.1555446194225723in"}

-   Power on the LPAR and logon with root access

b.  Configure NIM Server

    -   Check if required file sets are Installed \# lslpp -l \| grep -i
        > nim

    -   Attach Install ISO image to the server ![Graphical user
        > interface, text, application, email Description automatically
        > generated]<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image29.png">"4.721052055993001in"
        > height="2.721162510936133in"} ![Graphical user interface,
        > text, application, email Description automatically
        > generated]<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image53.png">"5.284520997375328in"
        > height="2.2483683289588803in"}

    -   Run #cfgmgr in OS to configure CDROM

    -   List CDROM to confirm \# lsdev -Cc cdrom![Graphical user
        > interface Description automatically
        > generated]<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image18.png">"5.037706692913386in"
        > height="1.3004746281714785in"}

    -   Install file sets from CDROM \# smitty install_latest

        1.  Select CDROM using F4 or esc+4 keys

        2.  Search for NIM in software to install

        3.  Select NIM master using F7 or esc+7 keys![Text Description
            > automatically
            > generated]<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image14.png">"4.342996500437446in"
            > height="2.46826990376203in"}

        4.  Continue installation ![Text Description automatically
            > generated]<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image39.png">"4.570984251968504in"
            > height="2.6448239282589676in"}![Text Description
            > automatically
            > generated]<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image3.png">"4.355553368328959in"
            > height="2.544368985126859in"}

    -   Exit installation menu using F10 or esc+0

    -   Confirm Installation is done \# lslpp -l \| grep -i nim![A
        > picture containing timeline Description automatically
        > generated]<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image23.png">"4.809909230096238in"
        > height="1.059070428696413in"}

    -   Set Hostname and /etc/hosts to exact name as step 3b![Text
        > Description automatically
        > generated]<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image11.png">"4.7754943132108485in"
        > height="1.944245406824147in"}

    -   Run NIM configuration \# smitty nim_config_env![Text Description
        > automatically
        > generated]<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image44.png">"4.9022495625546805in"
        > height="2.820155293088364in"}

    -   Press enter to start- this step will take \~30 mins to complete
        > ![Text Description automatically
        > generated]<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image12.png">"4.8418361767279094in"
        > height="2.9311045494313213in"}

    -   Congratulations NIM Server is Ready !!!!

```{=html}
<!-- -->
```
4)  Setup Client on NIM server to restore Mksysb

    a.  Update the /etc/hosts file with the client hostname![Graphical
        > user interface, text Description automatically
        > generated]<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image10.png">"5.0217639982502185in"
        > height="1.4790955818022746in"}

    b.  Verify hostname is resolved ![Shape Description automatically
        > generated with low
        > confidence]<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image32.png">"5.239807524059493in"
        > height="1.2129188538932634in"}

    c.  Add the Client in NIM config \# smitty nim_mkmac and enter the
        > client hostname![Text Description automatically
        > generated]<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image4.png">"4.548687664041995in"
        > height="2.9612806211723535in"}

    d.  Change the setting as below and press enter![Text Description
        > automatically
        > generated]<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image46.png">"4.9008486439195105in"
        > height="2.890139982502187in"}![Text Description automatically
        > generated]<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image50.png">"4.899166666666667in"
        > height="3.2080468066491687in"}

    e.  NIM Client is defined and can be verified in \# lsnim
        > nimclient![]<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image19.png">"5.694990157480315in"
        > height="0.6522878390201224in"}

    f.  Copy the Source Mksysb in a new filesystem /export/mksysb

        -   Create the filesystem \# crfs -v jfs2 -m /export/mksysb -A
            > yes -a size=10G -r rootvg![Text Description automatically
            > generated]<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image35.png">"5.5290562117235345in"
            > height="0.6895964566929134in"}

        -   List the mksysb file content lsmksysb -lf \<filename>![Text
            > Description automatically
            > generated]<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image34.png">"3.984274934383202in"
            > height="2.2603379265091865in"}

    g.  Create the NIM resources for restoration

        -   We need two NIM resources to restore Mksysb

            1.  Mksysb resource

            2.  Spot resource

        -   Mksysb - \# smitty nim_res > define a resource and select
            > mksysb and press enter![Text Description automatically
            > generated]<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image8.png">"4.695505249343832in"
            > height="2.8290430883639544in"}

        -   Fill in the Name, Server of resource and location of
            > resource (absolute path for location of the mksysb file)
            > ![Text Description automatically
            > generated]<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image42.png">"4.677398293963255in"
            > height="2.7670275590551183in"}

        -   Define Spot from mksysb resource \# smitty nim_res -\>
            > define a resource select spot and press enter (Make sure
            > you have enough space in /export/spot filesystem \~2
            > GB)![Graphical user interface, text Description
            > automatically
            > generated]<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image28.png">"4.875341207349082in"
            > height="2.9094050743657043in"}

        -   Enter Name, Server of resource, Source of Install images and
            > Location (absolute Path for the resource
            > "/export/spot/sapaix_spot") ![Text Description
            > automatically
            > generated]<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image33.png">"5.049488188976378in"
            > height="2.9132731846019246in"}

        -   Creation of spot will take some time \~ 15 to 30 mins ![Text
            > Description automatically
            > generated]<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image26.png">"4.721676509186351in"
            > height="2.874063867016623in"}

        -   Assign resources to nimclient machine defined earlier in the
            > document

> \# smitty nim_mac_res -\> Allocate Network Install resources and
> select nimclient from the list and press enter

-   Use F7 or Esc+7 to select the sapmksysb and sapaix_spot and press
    > enter![Text Description automatically
    > generated]<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image7.png">"4.505018591426071in"
    > height="2.6745122484689414in"}

-   Command output will be as below exit using F10 or esc+0![Text
    > Description automatically
    > generated]<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image36.png">"3.789750656167979in"
    > height="2.675731627296588in"}

-   Enable Bos installation for the client \# smitty nim_mac_op >
    > nimclient ![Text Description automatically
    > generated]<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image22.png">"4.974926727909011in"
    > height="2.906185476815398in"}

-   Select bos_inst and press enter ![Graphical user interface, text
    > Description automatically
    > generated]<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image6.png">"4.951044400699913in"
    > height="2.945412292213473in"}

-   Select the below options for mksysb restore ( Use Arrow keys and F4
    > or Esc+4) ![Text Description automatically generated with medium
    > confidence]<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image13.png">"4.921445756780402in"
    > height="2.915500874890639in"}

-   Ready to ROCK n ROLL ! ![Text Description automatically
    > generated]<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image24.png">"4.787376421697288in"
    > height="3.0284590988626423in"}

-   Verify the client Status \# lsnim -l nimclient ![Text Description
    > automatically
    > generated]<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image16.png">"4.415801618547682in"
    > height="3.359689413823272in"}

h.  Setup New LPAR to restore the mksysb

    -   From the Skytap dashboard Add a new LPAR in your existing
        > Environment ![Graphical user interface, application
        > Description automatically
        > generated]<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image21.png">"4.9295975503062115in"
        > height="3.2215824584426946in"}

    -   Select Templates and choose any AIX template and add VM
        > ![Graphical user interface, text, application, email
        > Description automatically
        > generated]<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image20.png">"5.901140638670166in"
        > height="3.2019149168853893in"}

    -   Edit the setting on VM and Set Hostname and IP ![Graphical user
        > interface, text, application Description automatically
        > generated]<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image30.png">"4.643280839895013in"
        > height="2.819073709536308in"} ![Graphical user interface,
        > text, application Description automatically
        > generated]<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image41.png">"4.40260498687664in"
        > height="3.4976246719160105in"}

    -   From hardware setting assign the disks as per Source LPAR rootvg

        1.  Delete the existing disk and add new disk ![Graphical user
            > interface, application Description automatically
            > generated]<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image5.png">"5.020990813648294in"
            > height="2.1464741907261593in"}

        2.  Add new disk ![Graphical user interface, text, application
            > Description automatically
            > generated]<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image48.png">"5.709429133858268in"
            > height="1.5293875765529308in"}

        3.  Update the CPU/RAM if required and start the LPAR and open
            > the console. System will enter in SMS menu ![Text
            > Description automatically
            > generated]<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image1.png">"4.925753499562554in"
            > height="2.889319772528434in"}

    -   Setup NIC/Ethernet to boot using NIM server using below sequence

        1.  " 2 - Setup Remote IPL"

        2.  " 1 - Interpartition Logical LAN"

        3.  " 1 - IPv4 -- Address Format"

        4.  " 1 - BOOTP"

        5.  " 1 -- IP Parameters"

        6.  Configure the IPS to match the IP as below ![Text
            > Description automatically
            > generated]<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image49.png">"4.949265091863517in"
            > height="2.9003608923884516in"}

        7.  Press esc to return to previous Menu and run "3 - Ping Test"
            > -\> press 1 and enter to execute ![Graphical user
            > interface Description automatically generated with medium
            > confidence]<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image56.png">"5.372890419947507in"
            > height="3.2570680227471565in"}

        8.  On successful ping press enter and return to main menu using
            > "M"

        9.  Press X to exit and server should start boot using the LAN
            > adapter ![Text Description automatically
            > generated]<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image31.png">"4.975820209973754in"
            > height="2.975356517935258in"}

> ![Text Description automatically
> generated]<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image47.png">"4.851860236220473in"
> height="3.4520089676290464in"}

10. It will take 10 -- 20 Mins to restore Mksysb and server will reboot
    > with the Mksysb restored ![A picture containing text Description
    > automatically
    > generated]<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/resiliency/solutions/mksysbmedia-old/media/image9.png">"5.681737751531059in"
    > height="2.4978608923884513in"}

#### Next steps<a name="nextsteps"></a>

**Main Overview**
> [Skytap Well-Architected Framework](../../)

**Operational Excellence**
>[Skytap Operational Excellence Pillar](../../operations/)

**Resiliency**
>[Skytap Resiliency Pillar](../)
>* [Migration](../migrations)
>* [Protection](../backups)
>* [Disaster Recovery](../disaster-recovery)
>* [High Availability](../ibmi-disaster-recovery)
>
>**Migration Solutions**
>* [Cold (Warm) Migrations (Backup and Restore)](./cold-migrations)
>* [Hot Migrations (Replication Sync)](./hot-migrations)
>
>**Design**
>* [Design Considerations for Azure](../design-considerations-azure)
>* [Design Considerations for IBM Cloud](../design-considerations-ibm)

**Security**
> * [Skytap Security Pillar](../../security/)