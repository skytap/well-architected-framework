---
Title: Azure NetApp Files for Skytap on Azure
Description: Connecting Azure NetApp Files to Skytap AIX machines Skytap on Azure
Authors: Abhishek Jain – Cloud Solution Architect, Jeffry Lane – Sr. Cloud Solutions Architect, Matthew Romero - Technical Product Marketing Manager
permalink: /operations/ecosystems/azure-native/skytap+netapp-files
---
# Azure NetApp Files for {{site.SoA}}
This guide is provided “as-is”. Information and views expressed in this document, including URL and other Internet website references, may change without notice and usage of the included material assumes this risk.

This document does not provide you with any legal rights to any intellectual property in any product. You may copy and use this document for internal reference purposes.

<a name="toc"></a>

# Table of Contents

* [Azure NetApp Files for {{site.Brand}}](#azure-netapp-files-for-skytap)
  * [Reference Architecture](#reference-architecture)
* [Azure NetApp files (ANF)](#azure-netapp-files-anf)
  * [Performance](#performance)
  * [Protocols](#protocols)
  * [Region Availability](#region-availability)
  * [Pricing](#pricing)
  * [Azure Documentation](#azure-documentation)
* [Provisioning Azure NetApp Files](#provisioning-azure-netapp-files)
  * [Create Azure NetApp account](#create-azure-netapp-account)
  * [Create Pools](#create-pools)
  * [Create Volumes](#create-volumes)
* [Mount NFS volumes on Client](#mount-nfs-volumes-on-client)
  * [Linux on Azure](#linux-on-azure)
  * [AIX on {{site.Brand}}](#aix-on-skytap)

<a name="azure-netapp-files-for-skytap"></a>

# Azure NetApp Files for {{site.Brand}}

{{site.Brand}} customers are looking for a cost-effective Azure native shared files solution to share files across Azure and their {{site.Brand}} environments which is secured and able to support both NFS and SMB protocols. Azure NetApp Files (ANF) solves most of these challenges and can therefore be a go-to solution for customers who are looking to have a shared files solution on {{site.Brand}}.

<a name="reference-architecture"></a>

## Reference Architecture

![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/ecosystems/azurenative/solutions/AzureNetAppFiles/media/image2.jpeg)

_Figure 1 – Azure Netapp files for {{site.Brand}}_

<a name="azure-netapp-files-anf"></a>

# Azure NetApp files (ANF)

The Azure NetApp Files (ANF) service is an enterprise-class, high-performance, metered file storage service. It supports any workload type and is highly available by default. You can select service and performance levels and set up snapshots through the service.

![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/ecosystems/azurenative/solutions/AzureNetAppFiles/media/image3.png)

_Figure 2 – Storage Hierarchy for ANF_

<a name="performance"></a>

## Performance

Azure NetApp Files supports three storage service levels: .

* Ultra: provides up to 128 MiB/s of throughput per 1 TiB of volume quota assigned.
* Premium: provides up to 64 MiB/s of throughput per 1 TiB of volume quota assigned.
* Standard: provides up to 16 MiB/s of throughput per 1 TiB of volume quota assigned.

<a name="protocols"></a>

## Protocols

Azure NetApp Files supports SMB 2.1 and SMB 3.1 (which includes support for SMB 3.0)\*.

Azure NetApp Files supports NFSv3 and NFSv4.1.

\* Requires active directory.

<a name="region-availability"></a>

## Region Availability

![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/ecosystems/azurenative/solutions/AzureNetAppFiles/media/image4.png)

![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/ecosystems/azurenative/solutions/AzureNetAppFiles/media/image5.png)

<a name="pricing"></a>

## Pricing

Here is sample pricing for 5TB capacity for each Service Level:

![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/ecosystems/azurenative/solutions/AzureNetAppFiles/media/image9.png)

<a name="azure-documentation"></a>

## Azure Documentation

[https://docs.microsoft.com/en-us/azure/azure-netapp-files](https://docs.microsoft.com/en-us/azure/azure-netapp-files)

<a name="provisioning-azure-netapp-files"></a>

# Provisioning Azure NetApp Files

<a name="create-azure-netapp-account"></a>

## Create Azure NetApp account

(Subscription must be whitelisted to use ANF)

1. [https://docs.microsoft.com/en-us/azure/azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes?tabs=azure-portal](https://docs.microsoft.com/en-us/azure/azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes?tabs=azure-portal)

    Once Subscription is whitelisted, you are ready to create Pools and Volumes.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/ecosystems/azurenative/solutions/AzureNetAppFiles/media/image10.png)

<a name="create-pools"></a>

## Create Pools

1. [https://docs.microsoft.com/en-us/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool](https://docs.microsoft.com/en-us/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool)
1. Minimum 4 TB pool size allowed.
1. Customer charged for complete pool size.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/ecosystems/azurenative/solutions/AzureNetAppFiles/media/image11.png)

<a name="create-volumes"></a>

## Create Volumes

1.[https://docs.microsoft.com/en-us/azure/azure-netapp-files/azure-netapp-files-create-volumes](https://docs.microsoft.com/en-us/azure/azure-netapp-files/azure-netapp-files-create-volumes)

  ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/ecosystems/azurenative/solutions/AzureNetAppFiles/media/image12.png)

<a name="mount-nfs-volumes-on-client"></a>

## Mount NFS volumes on Client

<a name="linux-on-azure"></a>

### Linux on Azure

(Mount Instructions for every volume can be found within Azure portal)

![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/ecosystems/azurenative/solutions/AzureNetAppFiles/media/image13.png)

<a name="aix-on-skytap"></a>

### AIX on {{site.Brand}}

1. Check if AIX LPAR can reach NetApp volume IP - (check if VPN/ExR is configured and ready to use)

    ``` powershell
    showmount -e \<IP>
    ```

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/ecosystems/azurenative/solutions/AzureNetAppFiles/media/image14.png)

1. Create required directory and Mount the filesystem

    ``` powershell
    mkdir -p \<mount point>
    ```

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/ecosystems/azurenative/solutions/AzureNetAppFiles/media/image15.png)

    ``` powershell
    mount \<remote IP>:/\<volumename>\<mount point>
    ```

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/ecosystems/azurenative/solutions/AzureNetAppFiles/media/image16.png)

    ``` powershell
    df -g \<mount point>
    ```

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/ecosystems/azurenative/solutions/AzureNetAppFiles/media/image17.png)

1. List Files in NFS share.

    ``` powershell
    cd \<mount Point>
    ```

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/ecosystems/azurenative/solutions/AzureNetAppFiles/media/image18.png)

    ``` powershell
    ls -l
    ```

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/ecosystems/azurenative/solutions/AzureNetAppFiles/media/image19.png)
