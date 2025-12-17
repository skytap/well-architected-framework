---
title: Edge networking
author: John Bradshaw, Principal Architect
permalink: /security/edge-networking/
---
## {{page.title}}

![](https://skytap.github.io/well-architected-framework/security/edgenetworkingmedia/media/image1.png)

The edge networking capability provides access to resources outside of the immediate environment in {{site.Brand}}, and as such, represents a significant area of exposure. Workloads running in the cloud have legitimate needs to access services or resources either on the Internet or behind the corporate network. To do this safely and efficiently consolidating this ingress and egress into a centralized service can help to enforce good practice and reduce risk.

The outbound proxy and inbound proxy filter is what Virtual Machines can connect to and what in turn can connect to them. For example, an outbound proxy may be configured to allow an application server to request data from a public API; whereas, an inbound proxy (sometimes called a protocol break) terminates external client connections to protect the internal servers from direct Internet exposure. The inbound proxy can then perform validation on the request from the client, such as determining if the client is a known bad actor or if the request is appropriately formed.

The VPN or Private Connection provides secure and restricted connectivity to on-premises or third-party cloud access in a controlled manner. For example, only the Production environment may be allowed to communicate over the Private Connection to the database running on-premises; however, the Development/Test environments can be connected to by the Engineering team via VPN.

Firewalls, Intrusion Detection Systems (IDS) and Intrusion Prevention Systems (IPS) are typically consolidated onto a single device, but they do provide distinctly different functionality. A Firewall can restrict connectivity between devices based on source or destination information sent with the traffic. In contrast, an IDS/IPS attempts to understand the context of the traffic passing through and responds accordingly. The IDS/IPS is designed to detect unauthorized intrusions on your network by matching traffic flows to known signatures.

When providing Internet or VPN access, this stack is typically implemented, as below. With the reverse being true for outbound traffic.

![Typical traffic flow](https://skytap.github.io/well-architected-framework/security/edgenetworkingmedia/media/image2.png)

_Figure 1 - Typical traffic flow_[^1]

#### Example high-level design

![Example Internet and VPN connection scheme](https://skytap.github.io/well-architected-framework/security/edgenetworkingmedia/media/image3.png)

_Figure 2 – Example Internet and VPN connection scheme_

----------------------------------------------------------------------------------

#### outbound proxy

An outbound proxy on the network edge should be configured to restrict access to websites, Internet-connected services and external services to only what is required for typical operation. For example, connection to an application or operating systems update service is appropriate. An outbound proxy can also be used for Data Loss Prevention (DLP) policy enforcement.

##### Recommended implementations

| Application                                                                      | Vendor |
|----------------------------------------------------------------------------------|--------|
| [Squid](http://www.squid-cache.org/)                                             | Squid  |
| [Web Gateway](https://www.mcafee.com/enterprise/en-gb/products/web-gateway.html) | McAfee |

----------------------------------------------------------------------------------

#### Inbound proxy

The inbound proxy sometimes referred to as reverse proxy, sits between the client and the webserver or application server hosted in {{site.Brand}}. A reverse proxy accepts a request from a client, forwards it to a server that can fulfil it, and returns the server's response to the client.

The proxy enhances security by shielding the backend servers from the outside network, and this prevents malicious clients from accessing them directly to exploit any known vulnerabilities. It can also protect these backend servers by rejecting traffic from blacklisted IP addresses or rate limiting the number of connections from clients, reducing the risk of a distributed denial-of-service (DDoS) attack.

##### Recommended implementations

| Application                                                                          | Vendor  |
|--------------------------------------------------------------------------------------|---------|
| [NGINX](https://www.nginx.com/)                                                      | F5      |
| [BIG IP](https://www.f5.com/products/big-ip-services)                                | F5      |
| [Squid](http://www.squid-cache.org/)                                                 | Squid   |
| [pfSense](https://www.netgate.com/solutions/pfsense/)                                | Netgate |
| [MVision Cloud](https://www.mcafee.com/enterprise/en-us/products/mvision-cloud.html) | McAfee  |

----------------------------------------------------------------------------------

#### VPN and private connection

{{site.Brand}} supports site-to-site VPN as well as Private Network Connections (PNCs) using [Azure ExpressRoute](https://azure.microsoft.com/en-gb/services/expressroute/) and [Equinix Cloud Exchange](https://www.equinix.com/solutions/cloud-infrastructure/public-cloud/connectivity/).

##### Site-to-site VPN

The {{site.Brand}} [site-to-site VPN](https://help.skytap.com/vpn-configuration-parameters.html#VPNconfigurationoptions) supports an IPSec VPN using IKEv1 or IKEv2 using Pre-Shared Keys (PSK), AES 256 bit, and Perfect Forward Secrecy (PFS).

The {{site.Brand}} site-to-site VPN secures traffic that traverses the public Internet, but not private connectivity. Multiple VPNs can be created for high availability and to connect multiple corporate data centers to {{site.Brand}}.

![Example VPN connection scheme](https://skytap.github.io/well-architected-framework/security/edgenetworkingmedia/media/image4.png)

_Figure 3 - Example VPN connection scheme_

##### Private Network Connection (PNC)

An Azure ExpressRoute or Equinix Cloud Hub connection are referred to as Private Network Connections (PNCs) in {{site.Brand}}.

In the example below, the PNC connects the on-premises data center with the {{site.Brand}} cloud environments. It should be noted that while this connection is private, using Multiprotocol Label Switching (MPLS), which logically isolates traffic;  it is not encrypted, however.

![Example private connection scheme](https://skytap.github.io/well-architected-framework/security/edgenetworkingmedia/media/image5.png)

_Figure 4 - Example private connection scheme_

When a PNC is used traffic between {{site.Brand}} and the on-premises data center or other cloud providers should be encrypted at the edge of the environment using a firewall to create the site-to-site connection or by using point-to-point encryption from a service mesh network. Service mesh networking is covered in the **Internal networking** section of this document.

----------------------------------------------------------------------------------

#### Firewall

A firewall should be implemented to protect the edge of the {{site.Brand}} platform, both to defend the workloads running in {{site.Brand}}, but also any onward connection to the corporate data center or other clouds.

Internet to environments filtering should take place to only permit acceptable connections, for example, HTTPS connection to the inbound proxy but discard all other forms of traffic attempting to connect to environments directly.

Environments to Internet filtering should restrict egress of traffic except via the outbound proxy; the proxy decides which external sites and services are acceptable.

VPN or PNC to environments, blanket access to environments even from private connections such as VPNs is inadvisable. Outside of machine-to-machine connectivity to support application operations, such as database calls or directory lookups, user access should be brokered via a jump host or Bastion host held in the management environment.

##### Recommended implementations

| Application                                           | Vendor  |
|-------------------------------------------------------|---------|
| [BIG IP](https://www.f5.com/products/big-ip-services) | F5      |
| [pfSense](https://www.netgate.com/solutions/pfsense/) | Netgate |

-------------------------------------------------------------

#### Intrusion detection system and intrusion prevention system

Typically Intrusion Detection Systems or Intrusion Prevention Systems are consolidated on the firewall but shown here as a discrete capability for completeness. The IDS/IPS performs a vital monitoring function to alert administrators and security personnel of unauthorized attempts to access the network. Intrusion is of particular concern with internet-facing applications.

In {{site.Brand}} an IDS/IPS must be placed in line with the traffic as port mirroring is not Recommended, hence the preference to include it as part of the Firewall capability.

##### Recommended implementations

| Application                                                                                                  | Vendor    |
|--------------------------------------------------------------------------------------------------------------|-----------|
| [Network Security Platform](https://www.mcafee.com/enterprise/en-gb/products/network-security-platform.html) | McAfee    |
| [pfSense](https://www.netgate.com/solutions/pfsense/)                                                        | Netgate   |
| [Next Gen Firewall](https://www.paloaltonetworks.com/network-security/next-generation-firewall)              | Palo Alto |

-------------------------------------------------------------

[^1]: A Load Balancer is shown for reference but is not within the scope of this document.
