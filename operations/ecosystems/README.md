---
title: Skytap Operational Excellence - Ecosystems
author: TBD
permalink: /operations/ecosystems/
---

# Ecosystems

Building a reliable network in the cloud is different from traditional
on-premises environments. While historically people may have purchased
levels of redundant higher-end hardware to minimize the chance of an
entire network stack failing, in the cloud, we acknowledge up front that
failures will happen. Instead of trying to prevent failures altogether,
the goal is to minimize the effects of a single failing component.

## Key Points

**Skytap-owned versus customer-owned responsibilities**

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/media/image1.png" width="400">

*Figure 2 \-- Shared Responsibility Model*

## Choosing the right design

The design for your cloud-based network routing is based on many
factors, both from the application itself as well as the business needs.
These factors include: the presence or absence of cloud-native
components of the application, as well as any existing network topology
and sensitivities; the presence or absence of firewall devices, either
in your corporate premises or existing cloud deployment; whether there
is need to route through, or bypass, existing cloud-native components;
the latency, throughput, and reliability needs of your application; the
particular high availability (HA) and/or disaster recovery (DR) needs of
your application; your project's budget, and its allowance for (or
prohibitions against) specialized components and requirements; your
strategy for migrating your existing data to the cloud; and many, many
others.

While every application is unique, most apps do have a lot in common
with each other. Skytap's Cloud Solutions Architects are accustomed to
looking for the important inflection points in your application's
design, and making a recommendation based on your application's current
and future needs. "Lift and Shift" migration options exist for the x86
and Power platforms supported in Skytap, and in simpler cases can mean
very little change is required from a hosting and network perspective to
your individual VMs and LPARs. However, cloud migration does offer many
opportunities to revisit current hosting- and network-based design
limitations and move to a more flexible and resilient cloud-based model.

##### Design Patterns:

-   [Azure Native Solutions](azurenative/README.md)
-   [SAP Workloads](SAP/README.md)


<hr>

#### Next steps

**Main Overview**
> [Skytap Well-Architected Framework](../../README.md)

**Operational Excellence**
>[Skytap Operational Excellence Pillar](../README.md)
>* [Power Discovery](../Discovery/README.md)
>* [Connectivity](../connectivity/README.md)
>* [Ecosystems](README.md)

**Resiliency**
> [Skytap Resiliency Pillar](../../resiliency/README.md)

>**Design**
>* [Design Considerations for Azure](../../resiliency/designconsiderationsazure.md)
>* [Design Considerations for IBM Cloud](../../resiliency/designconsiderationsibm.md)

**Security**
> [Skytap Security Pillar](../../security/README.md)
