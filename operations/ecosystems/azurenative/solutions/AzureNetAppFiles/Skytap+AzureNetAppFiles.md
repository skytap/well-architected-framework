---
Title: Azure NetApp Files for Skytap on Azure
Description: Connecting Azure NetApp Files to Skytap AIX machines Skytap on Azure
Authors: Abhishek Jain – Cloud Solution Architect, Jeffry Lane – Sr. Cloud Solutions Architect, Matthew Romero - Technical Product Marketing Manager
---
# Azure NetApp Files for Skytap on Azure
This guide is provided “as-is”. Information and views expressed in this document, including URL and other Internet website references, may change without notice and usage of the included material assumes this risk.

This document does not provide you with any legal rights to any intellectual property in any product. You may copy and use this document for internal reference purposes.

# Table of Contents<a name="toc"></a>

* [Azure NetApp Files for Skytap](#azure-netapp-files-for-skytap)
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
  * [AIX on Skytap](#aix-on-skytap)

# Azure NetApp Files for Skytap<a name="azure-netapp-files-for-skytap"></a>

Skytap customers are looking for a cost-effective Azure native shared
files solution to share files across Azure and their Skytap environments
which is secured and able to support both NFS and SMB protocols. Azure
NetApp Files (ANF) solves most of these challenges and can therefore be
a go-to solution for customers who are looking to have a shared files
solution on Skytap.

###### *[Back to the Top](#toc)*

## Reference Architecture<a name="reference-architecture"></a>

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/ecosystems/azurenative/solutions/AzureNetAppFiles/media/image2.jpeg" width="700">
Figure 1 : Azure Netapp files for Skytap

###### *[Back to the Top](#toc)*

# Azure NetApp files (ANF)<a name="azure-netapp-files-anf"></a>

The Azure NetApp Files (ANF) service is an enterprise-class,
high-performance, metered file storage service. It supports any workload
type and is highly available by default. You can select service and
performance levels and set up snapshots through the service.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/ecosystems/azurenative/solutions/AzureNetAppFiles/media/image3.png" width="700">
Figure 2: Storage Hierarchy for ANF

###### *[Back to the Top](#toc)*

## Performance<a name="performance"></a>

Azure NetApp Files supports three storage service levels: .

-   Ultra: provides up to 128 MiB/s of throughput per 1 TiB of volume quota assigned.

-   Premium: provides up to 64 MiB/s of throughput per 1 TiB of volume quota assigned.

-   Standard: provides up to 16 MiB/s of throughput per 1 TiB of volume quota assigned.

###### *[Back to the Top](#toc)*

## Protocols<a name="protocols"></a>

Azure NetApp Files supports SMB 2.1 and SMB 3.1 (which includes support
for SMB 3.0 ^\*^.

Azure NetApp Files supports NFSv3 and NFSv4.1.

^\*\ Requires\ active\ directory.^

###### *[Back to the Top](#toc)*

## Region Availability<a name="region-availability"></a>

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/ecosystems/azurenative/solutions/AzureNetAppFiles/media/image4.png" width="700">

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/ecosystems/azurenative/solutions/AzureNetAppFiles/media/image5.png" width="700">

###### *[Back to the Top](#toc)*

## Pricing<a name="pricing"></a>

Here is sample pricing for 5TB capacity for each Service Level:

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/ecosystems/azurenative/solutions/AzureNetAppFiles/media/image9.png" width="700">

###### *[Back to the Top](#toc)*

## Azure Documentation<a name="azure-documentation"></a>

<https://docs.microsoft.com/en-us/azure/azure-netapp-files/>

###### *[Back to the Top](#toc)*

# Provisioning Azure NetApp Files<a name="provisioning-azure-netapp-files"></a>

###### *[Back to the Top](#toc)*

## Create Azure NetApp account<a name="create-azure-netapp-account"></a>

(Subscription needs to whitelisted to use ANF)

1)  <https://docs.microsoft.com/en-us/azure/azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes?tabs=azure-portal>

Once Subscription is whitelisted, you are ready to create Pools and Volumes.

###### *[Back to the Top](#toc)*

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/ecosystems/azurenative/solutions/AzureNetAppFiles/media/image10.png" width="700">

## Create Pools<a name="create-pools"></a>

1)  <https://docs.microsoft.com/en-us/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool>

2)  Minimum 4 TB pool size allowed.

3)  Customer charged for complete pool size.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/ecosystems/azurenative/solutions/AzureNetAppFiles/media/image11.png" width="700">

###### *[Back to the Top](#toc)*

## Create Volumes<a name="create-volumes"></a>

1)  <https://docs.microsoft.com/en-us/azure/azure-netapp-files/azure-netapp-files-create-volumes>

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/ecosystems/azurenative/solutions/AzureNetAppFiles/media/image12.png" width="700">

###### *[Back to the Top](#toc)*

## Mount NFS volumes on Client <a name="mount-nfs-volumes-on-client"></a>
### Linux on Azure<a name="linux-on-azure"></a>

(Mount Instructions for every volume can be found within Azure portal)

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/ecosystems/azurenative/solutions/AzureNetAppFiles/media/image13.png" width="700">

###### *[Back to the Top](#toc)*

### AIX on Skytap<a name="aix-on-skytap"></a>

1)  Check if AIX LPAR can reach NetApp volume IP - (check if VPN/ExR is
    configured and ready to use)

``` powershell
showmount -e \<IP>
```

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/ecosystems/azurenative/solutions/AzureNetAppFiles/media/image14.png" width="300">

2)  Create required directory and Mount the filesystem

``` powershell
mkdir -p \<mount point>
```

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/ecosystems/azurenative/solutions/AzureNetAppFiles/media/image15.png" width="150">

``` powershell
mount \<remote IP>:/\<volumename>\<mount point>
```

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/ecosystems/azurenative/solutions/AzureNetAppFiles/media/image16.png" width="400">

``` powershell
df -g \<mount point>
```

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/ecosystems/azurenative/solutions/AzureNetAppFiles/media/image17.png" width="400">

3)  List Files in NFS share.

``` powershell
cd \< mount Point>
```

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/ecosystems/azurenative/solutions/AzureNetAppFiles/media/image18.png" width="100">

``` powershell
ls -l
```

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/ecosystems/azurenative/solutions/AzureNetAppFiles/media/image19.png">

###### *[Back to the Top](#toc)*

## Next steps

**Main Overview**
> [Skytap Well-Architected Framework](../../../../../README.md)

**Operational Excellence**
> [Skytap Operational Excellence Pillar](../../../../README.md)
> * [Power Discovery](../../../../Discovery/README.md)
> * [Connectivity](../../../../connectivity/README.md)
> * [Ecosystems](../../../../ecosystems/README.md)

**Resiliency**
> [Skytap Resiliency Pillar](../../../../../resiliency/README.md)

**Security**
> [Skytap Security Pillar](../../../../../security/README.md)