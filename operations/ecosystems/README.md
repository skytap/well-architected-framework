---
title: Skytap Operational Excellence - Ecosystems
author:  Matthew Romero, Technical Product Marketing Manager, Ahmed Abdulla PhD, Senior Engagement Manager - McKinsey & Company, Steven Martin, Chief Data Scientist - Microsoft
permalink: /operations/ecosystems/
---

# Ecosystems

**What is a cloud ecosystem?**

A cloud ecosystem is a complex system of interdependent components that all work together to enable cloud services. In nature, an ecosystem is composed of living and nonliving things that are connected and work together. In cloud computing, the ecosystem consists of hardware and software as well as cloud customers, cloud engineers, consultants, integrators and partners.

The need for technologies to work together is particularly clear in cloud computing – where platforms and services are so incredibly connected they must work together to deliver cloud computing benefits when and how customers want it.  {{site.Brand}} partners with these companies (and plan to partner with more) to bring our products & services to as many customers as possible. 

A robust ecosystem provides a cloud provider's customers with an easy way to find and purchase business applications and respond to changing business needs. When the apps are sold through a provider’s app store such as Microsoft Azure Marketplace (for cloud software), the customer essentially has access to a catalog of different vendors' software and services that have already been vetted and reviewed for security, risk and cost.

**The benefits of a cloud ecosystem**

In a cloud ecosystem, it is also easier to aggregate data and analyze how each part of the system affects the other parts. For example, if an ecosystem consists of patient records, smart device logs and healthcare provider records, it becomes possible to analyze patterns across an entire patient population.

## Key points

**{{site.Brand}}-owned versus customer-owned responsibilities**

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/media/image1.png" width="400">

*Figure 2 \-- Shared Responsibility Model*

## Choosing the right design

Once a company selects its data-ecosystem archetype, executives should then focus on setting up the right infrastructure to supports its operation. An ecosystem can’t deliver on its promise to participants without ensuring access to data, and that critical element relies on the design of the data architecture. 

{{site.Brand}} has identified five questions that customers must think about when setting up their data and workload ecosystems.

**How does {{site.Brand}} exchange data among partners in the ecosystem?**

The data exchange typically follows three steps: establishing a secure connection, exchanging data through browsers and clients, and storing results centrally when necessary.

**How does {{site.Brand}} manage identity and access?**

Companies can pursue two strategies to select and implement an identity-management system. The more common approach is to centralize identity management through solutions such as Okta, OpenID, or Ping. An emerging approach is to decentralize and federate identity management, for example, by using blockchain ledger mechanisms.

**How can {{site.Brand}} define data domains and storage?**

Traditionally, an ecosystem orchestrator would centralize data within each domain. More recent trends in data-asset management favor an open data-mesh architecture. Data mesh challenges conventional centralization of data ownership within one party by using existing definitions and domain assets within each party based on each use case or product. Certain use cases may still require centralized domain definitions with central storage. In addition, global data-governance standards must be defined to ensure interoperability of data assets.

**How does {{site.Brand}} manage access to non-local data assets, and how can {{site.Brand}} possibly consolidate?**

Most use cases can be implemented with periodic data loads through application programming interfaces (APIs). This approach results in a majority of use cases having decentralized data storage. Pursuing this environment requires two enablers: a central API catalog that defines all APIs available to ensure consistency of approach, and strong group governance for data sharing.

**How does {{site.Brand}} scale the ecosystem, given its heterogeneous and loosely coupled nature?**

Enabling rapid and decentralized access to data or data outputs is the key to scaling the ecosystem. This objective can be achieved by having robust governance to ensure that all participants of the ecosystem do the following:

Make their data assets discoverable, addressable, versioned, and trustworthy in terms of accuracy
Use self-describing semantics and open standards for data exchange
Support secure exchanges while allowing access at a granular level
The success of a data-ecosystem strategy depends on data availability and digitization, API readiness to enable integration, data privacy and compliance, for example, General Data Protection Regulation (GDPR), and user access in a distributed setup. This range of attributes requires companies to design their data architecture to check all these boxes.

As customers consider establishing new and more diverse data ecosystems, {{site.Brand}} recommends they develop a roadmap that specifically addresses the common challenges. They should then look to define their architecture to ensure that the benefits to participants and themselves come to fruition. The good news is that the data-architecture requirements for ecosystems are not complex. The priority components are identity and access management, a minimum set of tools to manage data and analytics, and central data storage.

##### Design Patterns:

<!-- - [Azure Native Solutions]({{ site.navigation.Operations }}ecosystems/azure-native/) -->
- [SAP Workloads]({{ site.navigation.Operations }}ecosystems/azure-native/sap-workloads)
- [Azure NetApp Files]({{ site.navigation.Operations }}ecosystems/azure-native/skytap+netapp-files)


### Next steps

**Main Overview**
> [{{site.Brand}} Well-Architected Framework]({{ site.navigation.Home }})

**Operational Excellence**
> [{{site.Brand}} Operational Excellence Pillar]({{ site.navigation.Operations }})
> * [Power Discovery]({{ site.navigation.Operations }}Discovery/)
> * [Connectivity]({{ site.navigation.Operations }}connectivity/)
> * [Ecosystems]({{ site.navigation.Operations }}ecosystems/)
> * [Performance]({{ site.navigation.Operations }}performance/)

**Resiliency**
> [{{site.Brand}} Resiliency Pillar]({{ site.navigation.Resiliency }})

**Security**
> [{{site.Brand}} Security Pillar]({{ site.navigation.Security }})
