---
title: Security Management
author: John Bradshaw, Principal Architect
permalink: /security/security-management/
---

### Security management

The Management layer sometimes referred to as *shared services*, contains the components necessary to secure and enforce compliance on the workloads running on {{site.Brand}}.

![Management required capabilities_](https://raw.githubusercontent.com/skytap/well-architected-framework/master/security/edgenetworkingmedia/media/image1.png)

_Figure 8 – Management required capabilities_

The {{site.Brand}} cloud service runs IBM Power, with operating systems such as AIX, IBM i, Linux, and x86 workloads, with operating systems such as Linux and Windows. Although operating systems that run on Power are less numerous than x86, threat actors still target these platforms given they are more likely to hold valuable data. Also, should these workloads be connected back to corporate assets in other clouds or on-premises they then represent a vector for malware to propagate to other systems and services.

[A]{.smallcaps} **Patching** strategy should encompass all workloads in {{site.Brand}}, including development, test and production. application vendors and operating system vendors are continually releasing security hotfixes, and functionality improvements and these should be applied judiciously to virtual machines and LPARs running in the {{site.Brand}} cloud.

**Authentication, authorization, and accounting** should support all workloads operating in {{site.Brand}}; a centralized repository of credential information reduces the operational burden of managing multiple directories on a per environment basis but also improves security by consolidating the logging and administration of users and service accounts.

A [**Backup**](../resiliency/) strategy should encompass the native capabilities of the {{site.Brand}} platform, such as templates which create a point in time clone of an entire workload from network configuration to data. However, templates are not designed to replace regular backups and are not intended to help recover a single row in a database that was deleted by mistake, for example. templates can save copies of running x86 virtual machines but can only copy shutdown LPARs on the Power platform.

**Secrets management** is used to protect sensitive application data, certificates, keys or credentials. It provides a secure enclave to perform sensitive cryptographic functions such as transaction signing or user/service authentication. For example, an application server may make a call to the Secrets Manager for credentials to access the database server. These keys are then held in RAM, or valid only for a short period, therefore if the VM is compromised the security of the database server is not.

**Log management** should be used to consolidate security and application logs from virtual machines or LPARs running in {{site.Brand}}. Centralized logging can assist operations or development teams in understanding application performance, an incident resolution. Many workloads in the cloud, and in particular {{site.Brand}}, are short-lived; this makes discovering and mitigating threats from bad actors particularly challenging because by the time an incident has been discovered the suspect virtual machine or LPAR may well have been destroyed. Centralized logging allows for the more comprehensive discovery of threats as well as their mitigation

#### Example high-level design

These components translate into an architecture that approximates the following:

![Example management architecture implemented in {{site.Brand}}](https://raw.githubusercontent.com/skytap/well-architected-framework/master/security/media/image7.png)

_Figure 9 – Example management architecture implemented in {{site.Brand}}_

These services are controlled and managed by corporate administrators accessing via the VPN/private connection. The services are then made available to workloads within {{site.Brand}} through the use of an Inter Configuration Network Routing ([ICNR](https://help.skytap.com/Networking_Between_Environments.html#ICNRoverview)), this is a low touch automated mechanism to connect logically distinct environments. Transit through an ICNRs is not possible; therefore, multiple environments of a different security profile can be connected via a Shared environment, but traffic cannot flow.

![Transit ICNR traffic is automatically denied](https://raw.githubusercontent.com/skytap/well-architected-framework/master/security/media/image8.png)

_Figure 10 – Transit ICNR traffic is automatically denied_

#### Antivirus

A cloud-aware antivirus solution is critical for the secure deployment of any hosted workload.

Policies should be set centrally and pushed out to workloads running on the platform, these policies should be cognisant of the nature of the workload, for example, is it transient or long-running, is it a database server or application middleware. An organizational security policy should require the use of Antivirus on all virtual machines or LPARs.

##### Recommended implementations

| Application | Vendor | x86            || Power             |||
|-------------|--------|:-------:|:-----:|:---:|:-----:|:-----:|
|             |        | Windows | Linux | AIX | IBM i | Linux |
| Trend       |        |         |       |     |       |       |
| HelpSystems |        |         |       |     |       |       |
| Sophos      |        |    √    |   √   |  √  |       |   √   |

#### Patching

Patching should be part of any organization's *golden template* strategy, as well as a regular part of operational best practice for longer lived environments.

A *golden template* should be regularly refreshed with the latest security and application patches for developers and users but should align with your general update cadence so as not to cause misalignment issues.

virtual machines and LPARs should be security patched regularly, for both operating systems and vendor application patches at least monthly, and application performance and functionality patching at intervals suitable to the organization.

##### Recommended implementations

| Application | Vendor            | x86            || Power             |||
|-------------|-------------------|:-------:|:-----:|:---:|:-----:|:-----:|
|             |                   | Windows | Linux | AIX | IBM i | Linux |
| BigFix      | HCL               |         |       |     |   √   |       |
| WSUS        | Microsoft         |         |       |     |       |       |
| Ansible     | RedHat Enterprise |         |       |     |       |       |

##### Architecture

###### Option 1 – Automated patch pull process

This process uses BigFix in fully automated mode to minimize patching effort and ensure that all instances are patched every time they are deployed

1. Deployment patch check: When the {{site.Brand}} instance is deployed and active, the application platforms in the template check-in with the Big-Fix server.

![Server instances are verified and patching updates are identified.](https://raw.githubusercontent.com/skytap/well-architected-framework/master/security/media/image9.png)

_Figure 11 – Server instances are verified and patching updates are identified._

1. Pull patches down: For those application instances requiring an update, the patches are deployed and applied to the necessary instances.

    _**Note** application teams must wait for this to occur with each deployment._

1. Save new template: Once the patching effort is completed, the development team saves the environment as a new gold template. Gold templates are used for future deployments and environment copies, thereby ensuring patching effort is minimized.

    _**Note** This also ensures that application teams can roll back to prior versions of the environment._

###### Option 2 – Gold template patch process

A gold template is established for each environment, which is used to create copies for development and test environments.

![gold template patch process](https://raw.githubusercontent.com/skytap/well-architected-framework/master/security/media/image10.png)

1. Gold template patch check: A {{site.Brand}} administrator brings up a gold template of an application environment and has BigFix automatically patch it.

    _**Note** Other patching services can be applied to the gold template at that time. The gold template is then saved and versioned to inform developers a new gold template has been published._

1. Developers pull down gold template: Developers copy the gold template to create development and test environments. Their applications are deployed into gold template copies, thereby creating fully patched and current versions of their development environments.

#### Authentication, authorization, and accounting

To simplify access control, and ultimately improve usability, centralising the authentication, authorization, and accounting for environments on the {{site.Brand}} platform should be delivered at a management layer.

##### Recommended implementations

| Application      | Vendor    | x86            || Power             |||
|------------------|-----------|:-------:|:-----:|:---:|:-----:|:-----:|
|                  |           | Windows | Linux | AIX | IBM i | Linux |
| Active Directory | Microsoft |         |       |     |       |       |

#### Backup

While the {{site.Brand}} platform has a point in time cloning system, using the [template an environment feature](https://help.skytap.com/saving-an-environment-as-a-template.html), this is not designed to replace a comprehensive backup strategy. It is not a replacement for live systems such as online Databases or application servers.

Most organizations will already have a defined backup strategy and preferred vendors, assuming these vendors do not require a physical appliance to be installed into {{site.Brand}} they should be compatible with the platform.

##### Recommended implementations

| Application                 | Vendor    | x86            || Power             |||
|-----------------------------|-----------|:-------:|:-----:|:---:|:-----:|:-----:|
|                             |           | Windows | Linux | AIX | IBM i | Linux |
| Veeam Agent for IBM PowerPC | Veeam     |    √    |   √   |     |       |       |
| AIX System Backup           | Storix    |         |       |  √  |       |       |
| SBAdmin                     | Storix    |         |   √   |     |       |   √   |
| BRMS                        | IBM       |         |   √   |     |   √   |       |
| Commvault                   | Commvault |    √    |   √   |  √  |   √   |   √   |

#### Secrets management

The {{site.Brand}} platform utilizes at rest encryption on all storage and strong in transit controls through our portal or API. However, these aren't designed to protect application secrets that may become compromised due to development errors; application or operating system vulnerabilities; or bad actors. To secure application secrets, license keys, and sensitive customer information a secrets manager can provide controlled access and encryption services for these functions.

##### Recommended implementations

| Application                           | Vendor    | x86            || Power             |||
|---------------------------------------|-----------|:-------:|:-----:|:---:|:-----:|:-----:|
|                                       |           | Windows | Linux | AIX | IBM i | Linux |
| [Vault](https://www.vaultproject.io/) | HashiCorp |    √    |   √   |  √  |   √   |   √   |

#### Log management

Most organizations will have already deployed an on-premises, or in cloud, log management tool to either deliver Security Incident Event Management (SIEM), operations management or both. application and operating systems logs should be funneled into this system to provide both Security and Operations teams a holistic view of their estates.

To simplify management and security, consolidating these logs into a single sender per environment or per customer account is preferred. All virtual machines and LPARs should send logging traffic to this sender for onwards propagation to the log management solution.

##### Recommended implementations

| Application                                                                       | Vendor | x86            || Power             |||
|-----------------------------------------------------------------------------------|--------|:-------:|:-----:|:---:|:-----:|:-----:|
|                                                                                   |        | Windows | Linux | AIX | IBM i | Linux |
| [Splunk Cloud](https://www.splunk.com/en_us/software/splunk-cloud.html)           | Splunk |    √    |   √   |     |       |       |
| [Splunk Enterprise](https://www.splunk.com/en_us/software/splunk-enterprise.html) | Splunk |    √    |   √   |  √  |   √   |   √   |
  