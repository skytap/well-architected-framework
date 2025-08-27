---
title: Internal Networking
author: John Bradshaw, Principal Architect
permalink: /security/internal-networking/
---

## Internal Networking

Several of these architectural building blocks are duplicated in the Edge Networking section and this is by design, each environment should be secured independently of each other by creating a defense in depth approach. This means that if one layer is compromised the crown jewels will still be secure.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/security/internalnetworkingmedia/media/image1.png" height="800">

*Figure 15 -- Internal Networking Required Capabilities*

Two additional services present in the environment internal networking, a Service Mesh and DNS/Service Discovery. A Service Mesh allows for secured communication between application components or virtual machines, though typically microservices, but can also fulfil some of the functionality of a firewall. DNS and Service Discovery allows the automated discovery of application services within an environment, for example Domain Services.

#### Example High Level Design

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/security/media/image16.png" height="800">


#### Firewall

Internal network firewalls can be deployed where it is necessary to restrict or control traffic between networks running inside {{site.Brand}}. These firewalls are deployed as virtual machines within your {{site.Brand}} environments.

##### Recommended Implementations

| Application | Vendor |
| ----------- | ------ |
| BigIP | F5 |
| <a href="https://www.netgate.com/solutions/pfsense/" target="_blank">pfSense</a> | Netgate |

-------------------------------------------------------------

#### Service Mesh

A Service Mesh can connect multiple application components or
microservices together in a way that simplifies their use. For example,
a Database running on AIX in {{site.Brand}} could be connected to a frontend
application hosted in a hyperscale cloud provider without having to
manually configure routing at each layer.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/security/internalnetworkingmedia/media/image2.png" alt="A close up of text on a white background Description automatically" width="600">

In addition to network simplification a Service Mesh can also provide
fine-grained access control, mutual TLS authentication and remove
network appliances (such as load balancers).

##### Recommended Implementations

| Application | Vendor | x86 |     | Power |     |     |
| ----------- | ------ | --- | --- | ----- | --- | --- |
|             |        | Windows | Linux | AIX | IBM i | Linux |
| <a href="https://www.hashicorp.com/products/consul/service-mesh/" target="_blank">Consul</a> | HashiCorp | ✔️ | ✔️ |   |   |   |

-------------------------------------------------------------------------

#### DNS/Service Discovery

Within an environment a DNS service can be used to provide information
to applications on where to send traffic without using the IP address.
This is beneficial on the {{site.Brand}} platform even though IP address
portability is Recommended, in case multiple concurrent environments are
running.

Service Discovery helps applications discover other services they
require, but can also validate that the endpoints are healthy before
directing traffic. Service Discovery can also replace some load
balancing functionality.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/security/internalnetworkingmedia/media/image3.png" width="500">

##### Recommended Implementations

| Application | Vendor |
| ----------- | ------ |
| <a href="https://www.hashicorp.com/products/consul/service-mesh/" target="_blank">Consul</a> | HashiCorp |
| Active Directory | Microsoft |
| <a href="https://umbrella.cisco.com/?_ga=2.214455253.393431820.1607963050-217404651.1607963050" target="_blank">Umbrella</a> | Cisco |

----------------------------------------------------------------------

#### Outbound Proxy

Outbound proxies can also be configured directly within your workload networks if needed to provide a layered security stance or if the workload requires it. This type of proxy is commonly used with stand-alone workloads where an application is expected to function in isolation.


#### Inbound Proxy

Inbound proxies deployed within a workload environment are typically used to handle proxying required within a workload or between workload environments.

##### Recommended Implementations

|      Application                                      | Vendor  |
| ----------------------------------------------------- | ------- |
| <a href="https://www.nginx.com/" target="_blank">NGINX</a>                       | F5      |
| <a href="http://www.squid-cache.org/" target="_blank">Squid</a>                  | Squid   |
| <a href="https://www.netgate.com/solutions/pfsense/" target="_blank">pfSense</a> | Netgate |  

-------------------------------------------------------------

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
> * [Edge Networking]({{ site.navigation.Security }}edge-networking)
> * [Virtual Machines]({{ site.navigation.Security }}virtual-machines)
> * [Security as a Service]({{ site.navigation.Security }}security-as-a-service)
