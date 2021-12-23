---
title: Overview of the operational excellence pillar
description: Describes the operational excellence pillar.
author: Mike Neil - Vice President, Technical Field Operations 
---

### What is the IBM Power Estate?

All organizations have some type of digital estate which is a collection
of assets that include virtual machines (VMs), physical servers,
applications, and their data. All of these assets power the organization
and its operations. 

For those organizations with an investment in IBM
Power, we look at segmenting the overall digital estate to the Power
estate. While functionally similar, the terminology can be slightly
different. 

An organizations Power estate is comprised of:

* **IBM Power systems** (sometimes called frames): IBM Power Systems
    are the physical servers that Power workloads and their applications
    run on.

* **Power VM**: Power VM is the virtualization engine that runs on IBM
    Power Hardware

* **LPARs (Logical Partitions)**: The LPAR is the analogous to the
    Virtual Machine, or VM on x86.

* **Storage**: While smaller shops will use storage local to the Power
    system, most larger deployments use fiber attached storage to a
    Storage Area Network (SAN)

* **Virtual IO Systems**: This is something most commonly called VIOS
    (Virtual Input Output System). VIOS provides network and storage
    connectivity for the LPARs running on a given Power System.

--------------------------------------------------------------------- ------------- ----------------------------------------

#### Current Cloud Rationalization and IBM Power Workloads

IBM Power Workloads have been in use for over thirty years and have
unique considerations when migrating them to a Cloud Platform. 

The common [Cloud
rationalization](https://docs.microsoft.com/en-us/azure/cloud-adoption-framework/digital-estate/5-rs-of-rationalization)
approach for x86 workloads also apply to Power:

* **Rehost**: This is commonly referred as a lift of shift of existing
    workloads into a Cloud provider with minimal change to the original
    architecture.

* **Refactor**: In application refactoring, organizations will rewrite one
    or more application components to take advantage of native Cloud
    services.

* **Rebuild**: In some cases it isn't cost effective ro refactor or
    rehost an application due to an aging code base, or a lack of
    knowledge as to how the legacy system works. In this case, the
    organization may decide to just start from scratch and create a new
    version of the application completely rearchitected from the ground
    up into a native Cloud-based application that leverages native
    services.

* **Replace**: With the advent of newer Cloud-native SaaS based
    applications, organizations may decide to simply purchase an
    entirely new application and sunset the original legacy application.

* **Retire**: Through application discovery, the organization may
    determine that the application is no longer needed, and/or there is
    overlap with other applications. In this case, the application can
    simply be "turned off".

--------------------------------------------------------------------- ------------- ----------------------------------------

### Updated Cloud Rationalization and IBM Power Workloads 

Before Skytap on Azure, organizations would consider all of the
approaches above with their Power applications, but after years of
moving their applications to the Cloud, there are thousands of customer
applications that are still running in the datacenter that are
increasingly becoming stuck where they are. In this case, organizations
do not want to make the investment to rearchitect, rebuild, replace, or
retire them. The ability to now run IBM Power applications in
hyper-scale Cloud providers such as Microsoft Azure has now changed the
landscape of Cloud rationalization. Let's revisit each of them in more
detail to describe how organizations may want to reconsider this process
for Power applications.

* **Rehost**: Before Skytap on Azure, there was no easy way to rehost
    an application in Azure, or another hyperscale provider. The "next
    best" option is to run Power applications in a colocation provider
    with a direct connection to the Cloud provider of choice. This has
    the disadvantage of having to still maintain physical
    infrastructure. Skytap on Azure allows you move those workloads into
    Azure directly while maintaining the ability for them to run on **Native** IBM Power hardware. At the same time, these workloads are
    in close proximity to their Skytap x86, Azure x86 and Azure native
    Cloud Services which allows for continued high-performance and
    easier manageability. Rehost is essentially a new option for Power
    workloads that didn't exist before in hyper-scale Cloud providers
    that is now a new reality with Skytap on Azure.

* **Refactor**: With the ability to run Power based applications in
    Azure, the amount of refactoring you must do is greatly diminished.
    You can re-host the Power based application components that don't
    lend themselves well to refactoring or would be too expensive to do
    so. Other components that are running on x86 such as a web-server,
    or application server can be refactored to running on Cloud-based
    x86 or Cloud-native solutions.

* **Rearchitect**: Applications that are large where the Power
    component is the "system of record" are great examples moving the
    application to run natively in Skytap on Azure is beneficial.
    Examples are IBM I applications that are used for logistics or
    warehouse management where 90% of the application can be
    Rearchitected to create a next-gen mobile application, or one that
    leverages AI. Instead of taking the time and expense to Rearchitect
    the Power based component it can simply live side-by-side with its
    Cloud-native components.

* **Rebuild**: There are plenty of IBM Power applications that are
    meeting user needs today and tomorrow. If there is a Cloud-first
    strategy, does it make sense to rebuild it to be cloud-native?
    Instead of rebuilding it, it can simply be rehosted into Skytap on
    Azure.


Once you understand your IBM Power estate, then you can start the
process of discovery to determine what and how you will be migrating to
Skytap on Azure.

--------------------------------------------------------------------- ------------- ----------------------------------------



#### IBM Power Discovery for on-premises workloads for migration to Skytap on Azure

| Operational Excellence Power Discovery & Design | Description |
|-------------------|-------------|
| [Power Discovery & Design - Ecosystems](./discoveryecosystems.md) | Provides guidance on how to identify and document your IBM Power estate - Hardware level identification - LPAR size, CPU count, RAM, Storage  |
| [Power Discovery & Design - Workloads](./discoveryworkloads.md) | Provides guidance on how to identify and document your IBM Power estate - softwar level identification - Application requirements |

## Next steps

**Main Overview**
> [Skytap Well-Architected Framework](../../README.md)

**Operational Excellence**
>[Skytap Operational Excellence Pillar](README.md)
>* [Power Discovery & Design - Ecosystems](discoveryecosystems.md)
>* [Power Discovery & Design - Workloads](discoveryworkloads.md)
>* [Connectivity](../connectivity/README.md)

**Resiliency**
> [Skytap Resiliency Pillar](../../resiliency/README.md)

**Security**
> [Skytap Security Pillar](../../security/README.md)
