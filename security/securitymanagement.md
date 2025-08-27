---
title: Security Management
author: John Bradshaw, Principal Architect
permalink: /security/security-management/
---

### Security Management

The Management layer sometimes referred to as *Shared Services*,
contains the components necessary to secure and enforce compliance on
the workloads running on {{site.Brand}}.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/security/edgenetworkingmedia/media/image1.png" width="800">

*Figure 8 - Management Required Capabilities*

The {{site.Brand}} cloud service runs IBM Power, with operating systems such as
AIX, IBM i, Linux and x86 workloads, with operating systems such as
Linux and Windows. Although Operating Systems that run on Power are less
numerous than x86, threat actors still target these platforms given they
are more likely to hold valuable data. Also, should these workloads be
connected back to corporate assets in other clouds or on-premises they
then represent a vector for malware to propagate to other systems and
services.

[A]{.smallcaps} **Patching** strategy should encompass all workloads in
{{site.Brand}}, including Development, Test and Production. Application Vendors
and Operating System Vendors are continually releasing security
hotfixes, and functionality improvements and these should be applied
judiciously to Virtual Machines and LPARs running in the {{site.Brand}} Cloud.

**Authentication, Authorization and Accounting** should support all
workloads operating in {{site.Brand}}; a centralized repository of credential
information reduces the operational burden of managing multiple
directories on a per Environment basis but also improves security by
consolidating the logging and administration of users and service
accounts.

A [**Backup**](../resiliency/) strategy should encompass the native capabilities of the
{{site.Brand}} platform, such as Templates which create a point in time clone of
an entire workload from network configuration to data. However,
Templates are not designed to replace regular backups and are not
intended to help recover a single row in a database that was deleted by
mistake, for example. Templates can save copies of running x86 Virtual
Machines but can only copy shutdown LPARs on the Power platform.

**Secrets Management** is used to protect sensitive application data,
certificates, keys or credentials. It provides a secure enclave to
perform sensitive cryptographic functions such as transaction signing or
user/service authentication. For example, an application server may make
a call to the Secrets Manager for credentials to access the database
server. These keys are then held in RAM, or valid only for a short
period, therefore if the VM is compromised the security of the database
server is not.

**Log Management** should be used to consolidate security and
application logs from Virtual Machines or LPARs running in {{site.Brand}}.
Centralised logging can assist operations or development teams in
understanding application performance, an incident resolution. Many
workloads in the cloud, and in particular {{site.Brand}}, are short-lived; this
makes discovering and mitigating threats from bad actors particularly
challenging because by the time an incident has been discovered the
suspect Virtual Machine or LPAR may well have been destroyed.
Centralised logging allows for the more comprehensive discovery of
threats as well as their mitigation

#### Example High Level Design

These components translate into an architecture that approximates the
following:

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/security/media/image7.png" width="500">

*Figure 9 - Example Management Architecture implemented in {{site.Brand}}*

These services are controlled and managed by corporate administrators
accessing via the VPN/Private Connection. The services are then made
available to workloads within {{site.Brand}} through the use of an Inter
Configuration Network Routing
([ICNR](https://help.skytap.com/Networking_Between_Environments.html#ICNRoverview)),
this is a low touch automated mechanism to connect logically distinct
environments. Transit through an ICNRs is not possible; therefore,
multiple environments of a different security profile can be connected
via a Shared environment, but traffic cannot flow.

<img src ="https://raw.githubusercontent.com/skytap/well-architected-framework/master/security/media/image8.png" width="500">

*Figure 10 - Transit ICNR Traffic is Automatically Denied*

#### Antivirus

A cloud-aware antivirus solution is critical for the secure deployment
of any hosted workload.

Policies should be set centrally and pushed out to workloads running on
the platform, these policies should be cognisant of the nature of the
workload, for example, is it transient or long-running, is it a database
server or application middleware. An organisational security policy
should require the use of Antivirus on all Virtual Machines or LPARs.

##### Recommended Implementations

|      Application     |      Vendor     |      x86         |                |      Power     |                |                |
|----------------------|-----------------|------------------|----------------|----------------|----------------|----------------|
|                      |                 |      Windows     |      Linux     |      AIX       |      IBM i     |      Linux     |
| Trend                |                 |                  |                |                |                |                |
| HelpSystems          |                 |                  |                |                |                |                |
| Sophos               |                 | ✔️                | ✔️              | ✔️              |                | ✔️              |

#### Patching

Patching should be part of any organisation\'s *Golden Template*
strategy, as well as a regular part of operational best practice for
longer lived environments.

A *Golden Template* should be regularly refreshed with the latest
security and application patches for developers and users but should
align with your general update cadence so as not to cause misalignment
issues.

Virtual Machines and LPARs should be security patched regularly, for
both operating systems and vendor application patches at least monthly,
and application performance and functionality patching at intervals
suitable to the organisation.

##### Recommended Implementations

|      Application     |      Vendor       |      x86         |                |      Power     |                |                |
|----------------------|-------------------|------------------|----------------|----------------|----------------|----------------|
|                      |                   |      Windows     |      Linux     |      AIX       |      IBM i     |      Linux     |
| BigFix               | HCL               |                  |                |                | ✔️              |                |
| WSUS                 | Microsoft         |                  |                |                |                |                |
| Ansible              | RedHat Enterprise |                  |                |                |                |                |

##### Architecture

###### Option 1 - Automated Patch Pull Process 

This process uses BigFix in fully automated mode to minimize patching
effort and ensure that all instances are patched every time they are
deployed

A.  Deployment Patch Check: When the {{site.Brand}} instance is deployed and
    active, the application platforms in the Template check-in with the
    Big-Fix Server.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/security/media/image9.png" width="500">

> Server instances are verified and patching updates are identified.

B.  Pull Patches Down: For those application instances requiring an
    update, the patches are deployed and applied to the necessary
    instances.

> Application teams will need to wait for this to occur with each
> deployment.

C.  Save New Template: Once the patching effort is completed, the
    development team will save the environment as a new Gold Template.
    Gold Templates are used for future deployments and environment
    copies, thereby ensuring patching effort is minimized.

> This also ensures that application teams can rollback to prior
> versions of the environment.

###### Option 2 - Gold Template Patch Process

A Gold Template is established for each environment, which is used to
create copies for development and test environments.
<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/security/media/image10.png" width="800">

A.  Gold Template Patch Check: A {{site.Brand}} Administrator brings up a Gold
    Template of an Application Environment and has Big Fix automatically
    patch it.

> Other patching services can be applied to the Gold Template at that
> time. The Gold Template is then saved and versioned to inform
> developers a new Gold Template has been published.

B.  Developers Pull Down Gold Template: Developers copy the Gold
    Template to create development and test environments. Their
    applications are deployed into Gold Template copies, thereby
    creating fully patched and current versions of their development
    environments.

#### Authentication, Authorization, Accounting

To simplify access control, and ultimately improve usability,
centralising the Authentication, Authorization, Accounting for
Environments on the {{site.Brand}} platform should be delivered at a management
layer.

##### Recommended Implementations

|      Application     |      Vendor     |      x86         |                |      Power     |                |                |
|----------------------|-----------------|------------------|----------------|----------------|----------------|----------------|
|                      |                 |      Windows     |      Linux     |      AIX       |      IBM i     |      Linux     |
| Active Directory     | Microsoft       |                  |                |                |                |                |

#### Backup

While the {{site.Brand}} platform has a point in time cloning system, using the
<a href="https://help.skytap.com/saving-an-environment-as-a-template.html" target="_blank">Template an Environment feature</a>,
this is not designed to replace a comprehensive backup strategy. It is
not a replacement for live systems such as online Databases or
Application servers.

Most organisations will already have a defined backup strategy and
preferred vendors, assuming these vendors do not require a physical
appliance to be installed into {{site.Brand}} they should be compatible with the
platform.

##### Recommended Implementations

|      Application            |      Vendor     |      x86         |                |      Power     |                |                |
|-----------------------------|-----------------|------------------|----------------|----------------|----------------|----------------|
|                             |                 |      Windows     |      Linux     |      AIX       |      IBM i     |      Linux     |
| Veeam Agent for IBM PowerPC | Veeam           | ✔️                | ✔️              |                |                |                |
| AIX System Backup           | Storix          |                  |                | ✔️              |                |                |
| SBAdmin                     | Storix          |                  | ✔️              |                |                | ✔️              |
| BRMS                        | IBM             |                  | ✔️              |                | ✔️              |                |
| Commvault                   | Commvault       | ✔️                | ✔️              | ✔️              | ✔️              | ✔️              |

#### Secrets Management

The {{site.Brand}} platform utilizes at rest encryption on all storage and
strong in transit controls through our portal or API. However, these
aren't designed to protect application secrets that may become
compromised due to development errors; application or operating system
vulnerabilities; or bad actors. To secure application secrets, license
keys, and sensitive customer information a secrets manager can provide
controlled access and encryption services for these functions.

##### Recommended Implementations

|      Application     |      Vendor     |      x86         |                |      Power     |                |                |
|----------------------|-----------------|------------------|----------------|----------------|----------------|----------------|
|                      |                 |      Windows     |      Linux     |      AIX       |      IBM i     |      Linux     |
| <a href="https://www.vaultproject.io/" target="_blank">Vault</a>                | HashiCorp       | ✔️                | ✔️              | ✔️              | ✔️              | ✔️              |

#### Log Management

Most organisations will have already deployed an on-premises, or in
cloud, log management tool to either deliver Security Incident Event
Management (SIEM), operations management or both. Application and
Operating Systems logs should be funneled into this system to provide
both Security and Operations teams a holistic view of their estates.

To simplify management and security, consolidating these logs into a
single sender per environment or per customer account is preferred. All
Virtual Machines and LPARs should send logging traffic to this sender
for onwards propagation to the log management solution.

##### Recommended Implementations

|      Application         |      Vendor     |      x86         |                |      Power     |                |                |
|--------------------------|-----------------|------------------|----------------|----------------|----------------|----------------|
|                          |                 |      Windows     |      Linux     |      AIX       |      IBM i     |      Linux     |
| <a href="https://www.splunk.com/en_us/software/splunk-cloud.html" target="_blank">Splunk Cloud</a>             | Splunk          | ✔️                | ✔️              |                |                |                |
| <a href="https://www.splunk.com/en_us/software/splunk-enterprise.html" target="_blank">Splunk Enterprise</a> | Splunk          | ✔️                | ✔️              | ✔️              | ✔️              | ✔️              |
  
### Next steps

**Main Overview**
> [{{site.Brand}} Well-Architected Framework]({{ site.navigation.Home }})

**Operational Excellence**
> [{{site.Brand}} Operational Excellence Pillar]({{ site.navigation.Operations }})

**Resiliency**
> [{{site.Brand}} Resiliency Pillar]({{ site.navigation.Resiliency }})

**Security**
> [{{site.Brand}} Security Pillar]({{ site.navigation.Security }})
> * [Key Security Areas]({{ site.navigation.Security }}key-security-areas)
> * [Edge Networking]({{ site.navigation.Security }}edge-networking)
> * [Virtual Machines]({{ site.navigation.Security }}virtual-machines)
> * [Internal Networking]({{ site.navigation.Security }}internal-networking)
> * [Security as a Service]({{ site.navigation.Security }}security-as-a-service)
