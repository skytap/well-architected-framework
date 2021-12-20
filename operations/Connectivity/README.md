---
title: Skytap Operational Excellence - Connectivity
author: Colleen E Hamilton - Senior Product Manager, Jason Scott - Cloud Solutions Architect
---

# Connectivity

Building a reliable network in the cloud is different from traditional
on-premises environments. While historically people may have purchased
levels of redundant higher-end hardware to minimize the chance of an
entire network stack failing, in the cloud, we acknowledge up front that
failures will happen. Instead of trying to prevent failures altogether,
the goal is to minimize the effects of a single failing component.

## Key Points

**Skytap-owned versus customer-owned responsibilities**

<img src="media/image1.png" width="400">

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

##### Design Solutions:

-   [Internal
    Networking](../../security/internalnetworking.md)

-   [Edge
    Networking](../../security/edgenetworking.md)

## Considerations on Reliability

The reliability of your Cloud Connectivity Design centers around 3 major
areas: recoverability from failure, the ability to scale as required to
meet increasing or dynamic requirements, and the control over capacity
(in the case of network this is primarily expressed in bandwidth).

Availability of the wide-area network (WAN) infrastructure in place for
your cloud deployments encompasses several factors, including not just
the path between endpoints, but also the reliability and configuration
of the devices along that path. ExpressRoutes are deployed as
fault-tolerant circuit pairs that also provide redundancy in the edge
devices they connect to in an Azure peering location. Once inside the
Azure edge however, vWAN offers automated high-availability for the
Virtual Network Gateways and other components. If you are not using
vWAN, then you need to consider the availability of your gateway
components and third party NVAs as well. A fault tolerant private
circuit connection into Azure may still have multiple single points of
failure if the remaining components connecting to your Skytap subnets
are not also fault tolerant.

A decision to standardize on IPSec VPN tunnels versus private circuit
connections like Equinix Virtual circuits or ExpressRoute often times
focuses primarily on cost. With VPNs in Skytap, there are no specific
charges for the virtual VPN endpoint created, or with ingress and egress
traffic. This is not typically the case with private circuits, as they
do not rely on the internet, they provide dedicated network paths and
more consistent bandwidth. VPNs should not necessarily be equated with
less reliability in many cases however. In use cases where you are
looking to create connections from networks in your Skytap account to
VNets in your Azure subscription(s), traffic does not actually leave the
Azure edge and traverse the internet. Also, while the connection in an
IPSec VPN tunnel does not traverse a private line, it is encrypted by
nature. Private circuits are just that by default, they are private but
the traffic across them is not encrypted.

Once a connectivity pattern into Skytap networks is chosen, either via
VPN or private circuit connections, the WAN implementation is very
similar. In both cases, Skytap exposes networks in an account via a WAN
interface. Local Skytap subnets must fall within an address space
assigned to the WAN, and remote subnets are specified to direct traffic
over the WAN connection. Skytap does not provide automated
fault-tolerance for these virtual WAN devices, so manual failover from a
primary to secondary WAN connection must be architected separately.

## Next steps

**Getting Started with IBM Cloud**

>[Design Considerations for IBM Cloud](../../resiliency/designconsiderationsibm.md)

 >[Skytap on IBM Cloud Connectivity](skytaponibmconnectivity.md)

**Getting Started with Azure**
 >[Design Considerations for Azure](../../resiliency/designconsiderationsazure.md)

>[Skytap on Azure Connectivity](skytaponazureconnectivity.md)
  