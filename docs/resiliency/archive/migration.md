**Skytap on Azure Architecture and Migration Considerations**

The following section provides an overview of Skytap on Azure
Architecture and Migration Considerations include

-   Skytap on Azure General Architecture

-   Supported LPARs

-   Warm Migration

-   Hot Migration

**Skytap on Azure General Architecture**

Here is a high-level look at the Skytap on Azure general architecture.

<img src="migrationmedia/media/image1.png">

**Skytap Service Layers**

Skytap is comprised of three service layer tiers as depicted below:

-   Data Platform Tier -- includes Bare Metal infrastructure, hosting
    service, storage service and network service.

-   Platform Tier -- includes business logic, accounting, quote manager,
    internal API and workflow engine and platform services (containers,
    shared drive, metadata, etc.)

-   Web Tier -- includes HTML UI, RESTful API, Import Export and
    SmartClient Service

<img src="migrationmedia/media/image2.png">

**Skytap on Azure: Azure Region, Skytap Region and connection to Azure
Native Services**

Within a given Azure region, Skytap Standard Power WMs and x86 VMs and
Azure Dedicated S922 and x86 Bare Metal and Storage are connected via
ExpressRoute to Azure Native Services as depicted below.

<img src="migrationmedia/media/image3.png">

**\
**

**Skytap Discovery and Migration -- Support and Limits**

The following section outlines Skytap on Azure support and limits for
IBM i and AIX.

**Skytap on Azure Support and Limits for IBM i**

<img src="./migrationmedia/media/image4.png">

**Skytap on Azure Support and Limits for AIX**

<img src="./migrationmedia/media/image5.png">

High-Level Considerations for Migration to Skytap on Azure

Migration to Skytap on Azure can be migrated via a hot or cold/warm
migration as depicted here. 

<img src="migrationmedia/media/image6.png">

**Skytap on Azure Architecture: Skytap + Commvault**

Skytap leverages best-in-class tools to accommodate both migrations
associated with these operating systems. Skytap has an engineering
partnership with Commvault to facilitate seamless migrations. The result
is a one-touch solution to migrate your workloads into Azure.

Here is an overview of Commvault.

<img src="migrationmedia/media/image7.png">

Here is an overview of how Skytap on Azure and Commvault work together
for AIX and IBM I (as400).

<img src="migrationmedia/media/image8.png">

**Skytap on Azure + Precisely for High Availability Scenarios**

For hot migrations, Skytap partners with Precisely, the market leader
for high availability scenarios. Precisely support IBM i and AIX. How
Precisely supports the Skytap on Azure hot migration for IBM i is
depicted below.

<img src="migrationmedia/media/image9.png">

This is built on IBM's remote journaling which replicates changed data
in real-time and minimizes bandwidth usage. The MIMX change data capture
replicates non-journaled data. This solution is flexible and platform
independent because it supports mixed hardware, storage and IBM I OS
version. It is also optimized for performance because it:

-   delivers near-zero RPO (zero with synchronous Remote Journaling)

-   provides near-zero RTO by maintaining a hot, switch-ready back up

-   includes performance tuning features to optimize performance for
    your environment (i.e parallel applies and database caching)

-   has active-active replication with built-in conflict resolution that
    delivers RTO in seconds (as quickly as moving users to another
    active server)

The following outlines popular Precisely migration scenarios available
for you to consider:

<img src="migrationmedia/media/image10.png">
