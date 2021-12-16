## **Connectivity**

**Summary**

Building a reliable network in the cloud is different from
traditional on-premise environments. While historically you may have
purchased levels of redundant higher-end hardware to minimize the chance
of an entire network stack failing, in the cloud, we acknowledge
up front that failures will happen. Instead of trying to prevent
failures altogether, the goal is to minimize the effects of a single
failing component.

## Key Points

**Skytap-owned versus customer-owned responsabilities**

<img src="../../security/media/image3.png" width="500" alt="Shared Responsibility Model">

*Figure 2 -- Shared Responsibility Model*

## Choosing the right design
The design for your cloud-based network routing is based on many factors, both from the application itself as well as the business needs. These factors include: the presence or absence of cloud-native components of the application, as well as any existing network topology and sensitivities; the presence or absence of firewall devices, either in your corporate premises or in your existing cloud deployment; whether there is need to route through, or bypass, existing cloud-native components; the latency, throughput, and reliability needs of your application; the particular HA and/or DR needs of your application; your project’s budget, and its allowance for (or against) specialized components and requirements; your strategy for migrating your existing data to the cloud; and many, many others. And while every application is unique, most apps do have a lot in common with each other.
Skytap’s Cloud Solutions Architects are accustomed to looking for the important inflection points in your application’s design, and making a recommendation based on your application’s current and future needs.  “Lift and Shift” migration options exist for the x86 and Power platforms supported in Skytap, and can in simpler cases mean very little change is required from a hosting and network perspective to your individual VMs and LPARs.  However, cloud migration offers many opportunities to revisit current hosting and network design limitations and moving to a more flexible and resilient cloud-based model.

##### Design Solutions:

- [Internal Networking](../../security/internalnetworking.md)
- [Edge Networking](../../security/edgenetworking.md)


## Getting started with IBM Cloud

- VPN


## Getting Started with Azure
Skytap and Microsoft Azure have partnered in the design of several common network patterns, which customers can put to work in their own applications.
#### Transiting Traffic Between Skytap and On-Prem

It’s worth noting that when connecting from Skytap to Azure VNets via Gateways, there are a number of noteworthy caveats, which you’ll find documented in Azure. In the context of connectivity with Skytap, the most important of these is traffic transit: traffic into a VNet from Skytap does not transit back out to your On-Premises site, even if you have an existing connection between the VNet and OnPrem. This is a fundamental limitation of Azure Gateways. When you need to transit traffic from Skytap to On-Prem, there are a number of alternative solutions that Azure has provided.

### [VPN](./skytapvpn.md)
Skytap’s built-in VPN service gives customers a streamlined option to establish a VPN tunnel between their environment in Skytap, and their Azure or on-premises deployment. The tunnel is encrypted and encapsulated, routed over the public internet. In our Skytap on Azure Data Centers, this means they use Azure’s own internet routing access, and in general, traffic sent from Skytap to an Azure VNet via VPN won’t leave the Azure backbone, making this a relatively simple, low-cost and behaviorally-consistent option, especially for POCs and Dev/Test applications. 



### [Express Route/Global Reach](../ExpressRoute/skytap2azureexpressroute.md)


## vWAN 


## Considerations on Reliability

Next Steps
