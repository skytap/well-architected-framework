---
title: Edge Networking
author: John Bradshaw, Principal Architect
permalink: /security/edge-networking/
---
## Edge Networking

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/security/edgenetworkingmedia/media/image1.png" width="800">

The Edge Networking capability provides access to resources outside of the immediate environment in {{site.Brand}}, and as such, represents a significant area of exposure. Workloads running in the cloud have legitimate needs to access services or resources either on the Internet or behind the corporate network. To do this safely and efficiently consolidating this ingress and egress into a centralized service can help to enforce good practice and reduce risk.

The Outbound Proxy and Inbound Proxy filter is what Virtual Machines can connect to and what in turn can connect to them. For example, an Outbound Proxy may be configured to allow an Application Server to request data from a public API; whereas, an Inbound Proxy (sometimes called a protocol break) terminates external client connections to protect the internal servers from direct Internet exposure. The Inbound Proxy can then perform validation on the request from the client, such as determining if the client is a known bad actor or if the request is appropriately formed.

The VPN or Private Connection provides secure and restricted connectivity to on-premises or third-party cloud access in a controlled manner. For example, only the Production environment may be allowed to communicate over the Private Connection to the database running on-premises; however, the Development/Test environments can be connected to by the Engineering team via VPN.

Firewalls, Intrusion Detection Systems (IDS) and Intrusion Prevention Systems (IPS) are typically consolidated onto a single device, but they do provide distinctly different functionality. A Firewall can restrict connectivity between devices based on source or destination information sent with the traffic. In contrast, an IDS/IPS attempts to understand the context of the traffic passing through and responds accordingly. The IDS/IPS is designed to detect unauthorized intrusions on your network by matching traffic flows to known signatures.

When providing Internet or VPN access, this stack is typically implemented, as below. With the reverse being true for outbound traffic.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/security/edgenetworkingmedia/media/image2.png" width="800">

*Figure 1 - Typical Traffic Flow*[^1]

#### Example High Level Design

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/security/edgenetworkingmedia/media/image3.png" width="600">

*Figure 2 - Example Internet and VPN Connection Scheme*

----------------------------------------------------------------------------------

#### Outbound Proxy

An Outbound Proxy on the network edge should be configured to restrict access to websites, Internet-connected services and external services to only what is required for typical operation. For example, connection to an application or operating systems update service is appropriate. An outbound proxy can also be used for Data Loss Prevention (DLP) policy enforcement.

##### Recommended Implementations

| Application | Vendor |
| ----------- | ------ |
| <a href="http://www.squid-cache.org/" target="_blank">Squid</a> | Squid |
| <a href="https://www.mcafee.com/enterprise/en-gb/products/web-gateway.html" target="_blank">Web Gateway</a> | McAfee |

----------------------------------------------------------------------------------


#### Inbound Proxy

The Inbound Proxy sometimes referred to as Reverse Proxy, sits between the client and the webserver or application server hosted in {{site.Brand}}. A reverse proxy accepts a request from a client, forwards it to a server that can fulfil it, and returns the server's response to the client.

The proxy enhances security by shielding the backend servers from the outside network, and this prevents malicious clients from accessing them directly to exploit any known vulnerabilities. It can also protect these backend servers by rejecting traffic from blacklisted IPs or rating limiting the number of connections from clients, reducing the risk of a distributed denial-of-service (DDoS) attack.

##### Recommended Implementations

| Application | Vendor |
| ----------- | ------ |
| <a href="https://www.nginx.com/" target="_blank">NGINX<a> | F5 |
| <a href="https://www.f5.com/products/big-ip-services" target="_blank">BIG IP<a> | F5 |
| <a href="http://www.squid-cache.org/" target="_blank">Squid<a> | Squid |
| <a href="https://www.netgate.com/solutions/pfsense/" target="_blank">pfSense<a> | Netgate |
| <a href="https://www.mcafee.com/enterprise/en-us/products/mvision-cloud.html" target="_blank">MVision Cloud<a> | McAfee |

----------------------------------------------------------------------------------

#### VPN / Private Connection

{{site.Brand}} supports Site-to-Site VPN as well as Private Network Connections (PNCs) using <a href="https://azure.microsoft.com/en-gb/services/expressroute/" target="_blank">Azure ExpressRoute</a> and <a href="https://www.equinix.com/solutions/cloud-infrastructure/public-cloud/connectivity/" target="_blank">Equinix Cloud Exchange</a>.

##### Site-to-Site VPN

The {{site.Brand}} <a href="https://help.skytap.com/vpn-configuration-parameters.html#VPNconfigurationoptions" target="_blank">Site-to-Site VPN</a> supports an IPSec VPN using IKEv1 or IKEv2 using Pre-Shared Keys (PSK), AES 256 bit and Perfect Forward Secrecy (PFS).

The {{site.Brand}} Site-to-Site VPN secures traffic that traverses the public Internet, but not private connectivity. Multiple VPNs can be created for high availability and to connect multiple corporate data centers to {{site.Brand}}.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/security/edgenetworkingmedia/media/image4.png" width="800">

*Figure 3 - Example VPN Connection Scheme*

##### Private Network Connection (PNC)

An Azure ExpressRoute or Equinix Cloud Hub connection are referred to as Private Network Connections (PNCs) in {{site.Brand}}.

In the example below, the PNC connects the on-premises data center with the {{site.Brand}} cloud environments. It should be noted that while this connection is private, using Multiprotocol Label Switching (MPLS), which logically isolates traffic, it is not however encrypted.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/security/edgenetworkingmedia/media/image5.png" width="800">

*Figure 4 - Example Private Connection Scheme*

When a PNC is used traffic between {{site.Brand}} and the On-premises data center or other cloud providers should be encrypted at the edge of the environment using a Firewall to create the site-to-site connection or by using point to point encryption from a service mesh network. Service Mesh networking is covered in the **Internal Networking** section of this document.

----------------------------------------------------------------------------------

#### Firewall

A Firewall should be implemented to protect the edge of the {{site.Brand}} platform, both to defend the workloads running in {{site.Brand}}, but also any onward connection to the corporate data center or other clouds.

Internet to Environments filtering should take place to only permit acceptable connections, for example, HTTPS connection to the Inbound Proxy but discard all other forms of traffic attempting to connect to Environments directly.

Environments to Internet filtering should restrict egress of traffic except via the Outbound Proxy; the proxy decides as to what external sites and services are acceptable.

VPN/PNC to Environments, blanket access to Environments even from private connections such as VPNs is inadvisable. Outside of machine to machine connectivity to support application operations, such as Database calls or Directory Lookups, user access should be brokered via a Jump/Bastion host held in the Management environment.

##### Recommended Implementations

| Application | Vendor |
| ----------- | ------ |
| <a href="https://www.f5.com/products/big-ip-services" target="_blank">BIG IP</a> | F5 |
| <a href="https://www.netgate.com/solutions/pfsense/" target="_blank">pfSense</a> | Netgate |

-------------------------------------------------------------

#### Intrusion Detection System / Intrusion Prevention System

Typically Intrusion Detection Systems or Intrusion Prevention Systems are consolidated on the firewall but shown here as a discrete capability for completeness. The IDS/IPS performs a vital monitoring function to alert administrators and security personnel of unauthorized attempts to access the network. Intrusion is of particular concern with internet-facing applications.

In {{site.Brand}} an IDS/IPS must be placed in line with the traffic as port mirroring is not Recommended, hence the preference to include it as part of the Firewall capability.

##### Recommended Implementations

| Application | Vendor |
| ----------- | ------ |
| <a href="https://www.mcafee.com/enterprise/en-gb/products/network-security-platform.html" target="_blank">Network Security Platform</a> | McAfee |
| <a href="https://www.netgate.com/solutions/pfsense/" target="_blank">pfSense</a> | Netgate |
| <a href="https://www.paloaltonetworks.com/network-security/next-generation-firewall" target="_blank">Next Gen Firewall</a> | Palo Alto |

-------------------------------------------------------------

[^1]: A Load Balancer is shown for reference but is not within the scope of this document.

### Next steps

**Main Overview**
> [{{site.Brand}} Well-Architected Framework]({{ site.navigation.Home }})

**Operational Excellence**
> [{{site.Brand}} Operational Excellence Pillar]({{ site.navigation.Operations }})
> * [Connectivity Overview]({{ site.navigation.Operations }}connectivity/)
> * [Getting Started with Azure Networking]({{ site.navigation.Operations }}connectivity/azure)
> * [Getting Started with IBM Cloud Networking]({{ site.navigation.Operations }}/connectivity/ibm)

**Resiliency**
> [{{site.Brand}} Resiliency Pillar]({{ site.navigation.Resiliency }})

**Security**
> [{{site.Brand}} Security Pillar]({{ site.navigation.Security }})
> * [Key Security Areas]({{ site.navigation.Security }}key-security-areas)
> * [Security Management]({{ site.navigation.Security }}security-management)
> * [Virtual Machines]({{ site.navigation.Security }}virtual-machines)
> * [Internal Networking]({{ site.navigation.Security }}internal-networking)
> * [Security as a Service]({{ site.navigation.Security }}security-as-a-service)
