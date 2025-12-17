---
title: Internal Networking
author: John Bradshaw, Principal Architect
permalink: /security/internal-networking/
---

## Internal networking

Several of these architectural building blocks are duplicated in the Edge Networking section and this is by design, each environment should be secured independently of each other by creating a defense in depth approach. This means that if one layer is compromised the crown jewels will still be secure.

![Internal networking required capabilities](https://skytap.github.io/well-architected-framework/security/internalnetworkingmedia/media/image1.png)
_Figure 1 – Internal networking required capabilities_

Two additional services present in the environment internal networking, a Service Mesh and DNS/Service Discovery. A Service Mesh allows for secured communication between application components or virtual machines, though typically microservices, but can also fulfil some of the functionality of a firewall. DNS and Service Discovery allows the automated discovery of application services within an environment, for example Domain Services.

#### Example high-level design

![Example high-level design](https://skytap.github.io/well-architected-framework/security/media/image16.png)

#### Firewall

Internal network firewalls can be deployed where it is necessary to restrict or control traffic between networks running inside {{site.Brand}}. These firewalls are deployed as virtual machines within your {{site.Brand}} environments.

##### Recommended implementations

| Application                                           | Vendor  |
|-------------------------------------------------------|---------|
| BigIP                                                 | F5      |
| [pfSense](https://www.netgate.com/solutions/pfsense/) | Netgate |

-------------------------------------------------------------

#### Service mesh

A Service Mesh can connect multiple application components or microservices together in a way that simplifies their use. For example, a database running on AIX in {{site.Brand}} could be connected to a frontend application hosted in a hyperscale cloud provider without having to manually configure routing at each layer.

![close up of text on a white background Description automatically](https://skytap.github.io/well-architected-framework/security/internalnetworkingmedia/media/image2.png)

In addition to network simplification a service mesh can also provide fine-grained access control, mutual TLS authentication and remove network appliances (such as load balancers).

##### Recommended implementations

| Application                                                       | Vendor    | x86            || Power             |||
|-------------------------------------------------------------------|-----------|:-------:|:-----:|-----|-------|-------|
|                                                                   |           | Windows | Linux | AIX | IBM i | Linux |
| [Consul](https://www.hashicorp.com/products/consul/service-mesh/) | HashiCorp |    √    |   √   |     |       |       |

-------------------------------------------------------------------------

#### DNS/Service discovery

Within an environment a DNS service can be used to provide information to applications on where to send traffic without using the IP address. This is beneficial on the {{site.Brand}} platform even though IP address portability is Recommended, in case multiple concurrent environments are running.

Service Discovery helps applications discover other services they require, but can also validate that the endpoints are healthy before directing traffic. Service Discovery can also replace some load balancing functionality.

![Service discovery](https://skytap.github.io/well-architected-framework/security/internalnetworkingmedia/media/image3.png)

##### Recommended implementations

| Application                                                                                       | Vendor    |
|---------------------------------------------------------------------------------------------------|-----------|
| [Consul](https://www.hashicorp.com/products/consul/service-mesh/)                                 | HashiCorp |
| Active Directory                                                                                  | Microsoft |
| [Umbrella](https://umbrella.cisco.com/?_ga=2.214455253.393431820.1607963050-217404651.1607963050) | Cisco     |

----------------------------------------------------------------------

#### Outbound proxy

Outbound proxies can also be configured directly within your workload networks if needed to provide a layered security stance or if the workload requires it. This type of proxy is commonly used with stand-alone workloads where an application is expected to function in isolation.

#### Inbound proxy

Inbound proxies deployed within a workload environment are typically used to handle proxying required within a workload or between workload environments.

##### Recommended implementations

| Application                                           | Vendor  |
|-------------------------------------------------------|---------|
| [NGINX](https://www.nginx.com/)                       | F5      |
| [Squid](http://www.squid-cache.org/)                  | Squid   |
| [pfSense](https://www.netgate.com/solutions/pfsense/) | Netgate |  
