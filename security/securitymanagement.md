---
title: Security
description: Considerations to ensure security.
author: tbd
categories:
  - security 
 subject:
  - security
---

### Security Management

The Management layer sometimes referred to as *Shared Services*,
contains the components necessary to secure and enforce compliance on
the workloads running on Skytap.

![](.//media/image4.png){width="4.053839676290464in"
height="2.189348206474191in"}

*Figure 8 - Management Required Capabilities*

The Skytap cloud service runs IBM Power, with operating systems such as
AIX, IBM i, Linux and x86 workloads, with operating systems such as
Linux and Windows. Although Operating Systems that run on Power are less
numerous than x86, threat actors still target these platforms given they
are more likely to hold valuable data. Also, should these workloads be
connected back to corporate assets in other clouds or on-premises they
then represent a vector for malware to propagate to other systems and
services.

[A]{.smallcaps} **Patching** strategy should encompass all workloads in
Skytap, including Development, Test and Production. Application Vendors
and Operating System Vendors are continually releasing security
hotfixes, and functionality improvements and these should be applied
judiciously to Virtual Machines and LPARs running in the Skytap Cloud.

**Authentication, Authorization and Accounting** should support all
workloads operating in Skytap; a centralized repository of credential
information reduces the operational burden of managing multiple
directories on a per Environment basis but also improves security by
consolidating the logging and administration of users and service
accounts.

A **Backup** strategy should encompass the native capabilities of the
Skytap platform, such as Templates which create a point in time clone of
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
application logs from Virtual Machines or LPARs running in Skytap.
Centralised logging can assist operations or development teams in
understanding application performance, an incident resolution. Many
workloads in the cloud, and in particular Skytap, are short-lived; this
makes discovering and mitigating threats from bad actors particularly
challenging because by the time an incident has been discovered the
suspect Virtual Machine or LPAR may well have been destroyed.
Centralised logging allows for the more comprehensive discovery of
threats as well as their mitigation

#### Example High Level Design

These components translate into an architecture that approximates the
following:

![A close up of a map Description automatically
generated](.//media/image7.png){width="4.625in"
height="5.864583333333333in"}

*Figure 9 - Example Management Architecture implemented in Skytap*

These services are controlled and managed by corporate administrators
accessing via the VPN/Private Connection. The services are then made
available to workloads within Skytap through the use of an Inter
Configuration Network Routing
([[ICNR]{.ul}](https://help.skytap.com/Networking_Between_Environments.html#ICNRoverview)),
this is a low touch automated mechanism to connect logically distinct
environments. Transit through an ICNRs is not possible; therefore,
multiple environments of a different security profile can be connected
via a Shared environment, but traffic cannot flow.

![A close up of a person Description automatically
generated](.//media/image8.png){width="3.756038932633421in"
height="1.8385422134733158in"}

*Figure 10 - Transit ICNR Traffic is Automatically Denied*

#### Antivirus

A cloud-aware antivirus solution is critical for the secure deployment
of any hosted workload.

Policies should be set centrally and pushed out to workloads running on
the platform, these policies should be cognisant of the nature of the
workload, for example, is it transient or long-running, is it a database
server or application middleware. An organisational security policy
should require the use of Antivirus on all Virtual Machines or LPARs.

##### Supported Implementations

  ------------- ------------- --------- ------ ------- ----- ------- ----------------
  Application   Vendor        x86              Power                 Reference
                                                                     Architecture

                              Windows          Linux   AIX   IBM i   Linux

                Trend                                                

                HelpSystems                                          

                Sophos        ✔️        ✔️     ✔️            ✔️      
  ------------- ------------- --------- ------ ------- ----- ------- ----------------

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

##### Supported Implementations

  ------------- ----------- --------- ------ ------- ------ ------ -----------------
  Application   Vendor      x86              Power                 Reference
                                                                   Architecture

                            Windows          Linux   AIX    IBM i  Linux

  BigFix        HCL                                                ✔️

  WSUS          Microsoft                                          

  Ansible       Red Hat                                            
  ------------- ----------- --------- ------ ------- ------ ------ -----------------

##### Architecture

###### Option 1 - Automated Patch Pull Process 

This process uses BigFix in fully automated mode to minimize patching
effort and ensure that all instances are patched every time they are
deployed

A.  Deployment Patch Check: When the Skytap instance is deployed and
    active, the application platforms in the Template check-in with the
    Big-Fix Server.![](.//media/image9.png){width="3.8673611111111112in"
    height="3.7444444444444445in"}

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
![](.//media/image10.png){width="4.340277777777778in" height="3.0625in"}

A.  Gold Template Patch Check: A Skytap Administrator brings up a Gold
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
Environments on the Skytap platform should be delivered at a management
layer.

##### Supported Implementations

  ------------- ----------- --------- ----- ------- ----- ------ -----------------
  Application   Vendor      x86             Power                Reference
                                                                 Architecture

                            Windows         Linux   AIX   IBM i  Linux

  Active        Microsoft                                        
  Directory                                                      

                                                                 
  ------------- ----------- --------- ----- ------- ----- ------ -----------------
Backup

While the Skytap platform has a point in time cloning system, using the
[[Template an Environment
feature]{.ul}](https://help.skytap.com/saving-an-environment-as-a-template.html),
this is not designed to replace a comprehensive backup strategy. It is
not a replacement for live systems such as online Databases or
Application servers.

Most organisations will already have a defined backup strategy and
preferred vendors, assuming these vendors do not require a physical
appliance to be installed into Skytap they should be compatible with the
platform.

##### Supported Implementations

  ------------- ----------- --------- ------ ------- ------ ------ --------------------
  Application   Vendor      x86              Power                 Reference
                                                                   Architecture

                            Windows          Linux   AIX    IBM i  Linux

                Veeam       ✔️               ✔️                    

                Storix                       ✔️                    ✔️

  BRMS          IBM                                  ✔️            ✔️

  Commvault     Commvault   ✔️        ✔️     ✔️      ✔️     ✔      ✔️
  ------------- ----------- --------- ------ ------- ------ ------ --------------------

#### Secrets Management

The Skytap platform utilizes at rest encryption on all storage and
strong in transit controls through our portal or API. However, these
aren't designed to protect application secrets that may become
compromised due to development errors; application or operating system
vulnerabilities; or bad actors. To secure application secrets, license
keys, and sensitive customer information a secrets manager can provide
controlled access and encryption services for these functions.

##### Supported Implementations

  ---------------------------------------------- ----------- --------- ------ ------- ------ ------ -----------------
  Application                                    Vendor      x86              Power                 Reference
                                                                                                    Architecture

                                                             Windows          Linux   AIX    IBM i  Linux

  [[Vault]{.ul}](https://www.vaultproject.io/)   HashiCorp   ✔️        ✔️     ✔️      ✔️     ✔️     

                                                                                                    
  ---------------------------------------------- ----------- --------- ------ ------- ------ ------ -----------------

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

##### Supported Implementations

  --------------------------------------------------------------------------------- -------- --------- ----- ------- ----- ----- -------------------
  Application                                                                       Vendor   x86             Power               Reference
                                                                                                                                 Architecture

                                                                                             Windows         Linux   AIX   IBM i Linux

  [[Splunk Cloud]{.ul}](https://www.splunk.com/en_us/software/splunk-cloud.html)\   Splunk   ✔️        ✔️                        
  [[Splunk                                                                                                                       
  Enterprise]{.ul}](https://www.splunk.com/en_us/software/splunk-enterprise.html)                                                

  syslog                                                                                     ✔️        ✔️    ✔️      ✔️    ✔️    
  --------------------------------------------------------------------------------- -------- --------- ----- ------- ----- ----- -------------------
