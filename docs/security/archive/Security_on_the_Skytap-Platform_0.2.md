![](.//media/image1.png){width="11.850765529308836in"
height="14.78627734033246in"}

# Table of Contents

[Table of Contents](#table-of-contents) **2**

[Overview](#overview) **6**

> [Key Security Areas](#key-security-areas) 8
>
> [Platform](#platform) 10
>
> [Security Policies](#security-policies) 10
>
> [Notifications](#notifications) 10
>
> [Authentication, Authorization and
> Accounting](#authentication-authorization-and-accounting) 11
>
> [Users, Groups, Projects and
> Departments](#users-groups-projects-and-departments) 11
>
> [Users](#user-roles) 11
>
> [Groups](#groups) 12
>
> [Projects](#projects) 12
>
> [Departments](#departments) 14
>
> [Single Sign-On](#single-sign-on) 15
>
> [Labels](#labels) 15
>
> [Auditing](#auditing) 15
>
> [Management](#management) 16
>
> [Example High Level Design](#example-high-level-design) 17
>
> [Antivirus](#antivirus) 18
>
> [Supported Implementations](#supported-implementations) 18
>
> [Patching](#patching) 18
>
> [Supported Implementations](#supported-implementations-1) 18
>
> [Architecture](#architecture) 18
>
> [Option 1 - Automated Patch Pull
> Process](#option-1---automated-patch-pull-process) 18
>
> [Option 2 - Gold Template Patch
> Process](#option-2---gold-template-patch-process) 19
>
> [Authentication, Authorisation,
> Accounting](#authentication-authorization-accounting) 19
>
> [Supported Implementations](#supported-implementations-2) 19
>
> [Architecture](#architecture-1) 19
>
> [Option 1 -- Centralised Directory](#option-1-centralized-directory)
> 20
>
> [Option 2 -- Independent Directory Synchronisation between
> Environments](#option-2-independent-directory-synchronization-between-environments)
> 20
>
> [Backup](#backup) 20
>
> [Supported Implementations](#supported-implementations-3) 20
>
> [Secrets Management](#secrets-management) 20
>
> [Supported Implementations](#supported-implementations-4) 20
>
> [Log Management](#log-management) 20
>
> [Supported Implementations](#supported-implementations-5) 21
>
> [Edge Networking](#edge-networking) 22
>
> [Example High Level Design](#example-high-level-design-1) 23
>
> [Outbound Proxy](#outbound-proxy) 23
>
> [Supported Implementations](#supported-implementations-6) 23
>
> [Inbound Proxy](#inbound-proxy) 24
>
> [Supported Implementations](#supported-implementations-7) 24
>
> [VPN / Private Connection](#vpn-private-connection) 24
>
> [Site-to-Site VPN](#site-to-site-vpn) 24
>
> [Private Network Connection (PNC)](#private-network-connection-pnc) 25
>
> [Firewall](#firewall) 26
>
> [Supported Implementations](#supported-implementations-8) 26
>
> [Intrusion Detection System / Intrusion Prevention
> System](#intrusion-detection-system-intrusion-prevention-system) 26
>
> [Supported Implementations](#supported-implementations-9) 26
>
> [Virtual Machines](#virtual-machines) 27
>
> [VM Templates](#vm-templates) 27
>
> [Authentication, Authorisation and
> Accounting](#authentication-authorisation-and-accounting) 27
>
> [Supported Implementations](#supported-implementations-10) 28
>
> [Patching](#patching-1) 28
>
> [Supported Implementations](#supported-implementations-11) 28
>
> [Antivirus](#antivirus-1) 28
>
> [Supported Implementations](#supported-implementations-12) 28
>
> [HIDS/HIPS](#hidships) 28
>
> [Supported Implementations](#supported-implementations-13) 29
>
> [OS Firewall](#os-firewall) 29
>
> [Example High Level Design](#example-high-level-design-2) 30
>
> [Internal Networking](#internal-networking) 31
>
> [Example High Level Design](#example-high-level-design-3) 31
>
> [Firewall](#firewall-1) 31
>
> [Supported Implementations](#supported-implementations-14) 31
>
> [Service Mesh](#service-mesh) 31
>
> [Supported Implementations](#supported-implementations-15) 32
>
> [DNS/Service Discovery](#dnsservice-discovery) 32
>
> [Supported Implementations](#supported-implementations-16) 33
>
> [Outbound Proxy](#outbound-proxy-1) 33
>
> [Supported Implementations](#supported-implementations-17) 33
>
> [Inbound Proxy](#inbound-proxy-1) 33
>
> [Supported Implementations](#supported-implementations-18) 33
>
> [Security as a Service](#security-as-a-service) 34
>
> [Legend](#legend) 34

[References](#references) **34**

# Overview

Securely running workloads in a cloud environment requires a
multi-layered approach which covers both how workloads are deployed and
maintained within your account and the management of your Skytap account
itself. This document outlines architectural elements necessary to
achieve a robust implementation on the Skytap platform and best
practices for how your Skytap account should be configured.

![](.//media/image2.png){width="6.375in" height="3.013888888888889in"}

*Figure 1 - Control Map*

Assets you deploy into your account should be defined by implementation
standards based on your use cases and requirements while taking into
account best practices of how to leverage Skytap. Each layer in this
model is built on the capabilities delivered in the layer below; you
cannot know how to secure a virtual machine without understanding how
they can be secured and why they need to be.

Accompanying this document is a **Skytap - Security Controls Workbook**
that should be referred to as you develop your High-Level Designs and
Low-Level Designs. It outlines each area discussed herein with specific
controls that should be applied. It also contains an example of a Risks
and Mitigations register that should be considered as part of any cloud
project.

Similar to other cloud services, Skytap operates a shared responsibility
model with regards to security. Skytap is accountable for the platform
and customers are accountable for the way they use that platform.

![](.//media/image3.png){width="5.2962390638670165in"
height="5.098958880139983in"}

*Figure 2 -- Shared Responsibility Model*

Additionally, resources deployed into Skytap should have clearly a
clearly defined operating model:

-   Who is responsible for creation/deletion of virtual machines?

-   Who is responsible for updating the application/data on your virtual
    machines?

-   Who can provide common services support on the virtual machines?

-   Are there services external to Skytap that need to be supported for
    your workloads?

-   Which of your existing operating models need to change for new cloud
    workloads?

Most organizations will have multiple cloud vendors, supported by
systems integrators or outsourced management functions. Cloud platforms
need to be managed holistically but controls applied specifically, for
example, all logging across your organisation should be consolidated
into a single Security Incident Event Monitoring (SIEM) system and the
approach to management and remediation will be defined by the workload
and platform.

# 

## Key Security Areas

Within Skytap, there are five areas to build a control framework around:
Platform, Resource Management, Internal Networking, Edge Networking, and
Virtual Machines.

For each of these areas and subsections this document will provide
standardized designs and partner points of contact where appropriate.

![](.//media/image4.png){width="5.875in" height="8.305555555555555in"}

*Figure 4 - Cloud Security Capabilities*

###  

### Platform

The Skytap platform provides controls for Security Policies;
Notifications; Authentication, Authorisation and Accounting; and
Auditing.

![](.//media/image4.png){width="5.875in" height="1.2727274715660541in"}

*Figure 5 - Platform Required Capabilities*

#### Security Policies

The security policies control access to the platform for authenticated
users. This can restrict connectivity to approved IP addresses (such as
those on the corporate network), password complexity rules (for those
not using SSO) and session timeouts (prompting for reauthentication).
Skytap recommends that access is limited to the corporate network
subnets, or alternatively use a virtual machine as a Bastion host to
access the Skytap dashboard securely from the Skytap infrastructure.
More information on access policies is available
[[here]{.ul}](https://help.skytap.com/setting-access-policies.html)

![](.//media/image5.png){width="7.267716535433071in" height="3.875in"}

*Figure 6 - Access Policies available within the Skytap Dashboard*

#### Notifications

Notifications can be used to generate automated email alerts based on
account usage such as capacity thresholds in specific regions or
long-running virtual machines.

#### Authentication, Authorization and Accounting

##### Users, Groups, Projects and Departments

The Users, Groups, Projects and Departments are used to control Cost
and/or Access to services; they should be configured to align with your
organisation's best business processes and operational best practices.

  -----------------------------------------------------------------------
                          Cost Control            Access Control
  ----------------------- ----------------------- -----------------------
  Users                   ✔️                      ✔️

  Groups                                          ✔️

  Projects                                        ✔️

  Departments             ✔️                      
  -----------------------------------------------------------------------

A User is a named individual or service account that can authenticate to
the platform. Once authenticated, they can then assume either a
Restricted User, Standard User, User Manager or Administrator platform
role.

Departments are used to consolidate consumption allowances and
reporting. Departments can be created with hard limits on the resources
they can consume. A user may be a member of a single Department.

###### User Roles

The restricted role is best for users who need tightly controlled access
to a limited number of resources. The standard role is for trusted
individuals who need the ability to deploy virtual machines and
resources within Skytap. The user manager role is best for users who
need to manage and organize users and groups but who don't need full
administrator capabilities. The administrator role is best for trusted
users in your organization who need to manage users, resources, and
account-wide settings.

User accounts should be created as Restricted Users unless those
individuals require elevated permissions within the platform.

*User Permissions*

  ------------------------------------------------------------------------------------
  Restricted   Standard   User      Administrator   Can do this
                          Manager                   
  ------------ ---------- --------- --------------- ----------------------------------
  ✔️           ✔️         ✔️        ✔️              Access shared project resources

  \*           ✔️         ✔️        ✔️              Create and own projects

  †            ✔️         ✔️        ✔️              Create environments, templates,
                                                    and assets

                          ✔️        ✔️              Create and edit users and groups

                          ✔️        ✔️              Delete groups

               ‡          ‡         ✔️              Create and view reports

                                    ✔️              Create and edit departments

                                    ✔️              Delete users

                                    ✔️              Create and edit account-wide
                                                    settings (password policies,
                                                    access policies, usage limits,
                                                    etc.)

                                    ✔️              Edit and delete environments,
                                                    templates, and assets owned by all
                                                    users in the account.
  ------------------------------------------------------------------------------------

*\* A restricted user can't create a project, but another user can make
a restricted user a project owner.\
† Restricted users have limited permission to create environments.\
‡ An administrator must grant reporting privileges for a user to create
and view reports.*

Skytap has additional permissions that can be enabled or disabled for
each user. Some of these permissions are displayed only when specific
features are enabled in the platform. The table below shows the
additional permissions that are **optional (O)** for each type of user.

-   Most permissions are **mandatory (M)** for administrators and can't
    be disabled.

-   Restricted users can't have most permissions enabled.

*Extended User Permissions*

+-------+------+--------+---------+----------------------------------+
| Restr | Stan | User   | Admini  | Permission                       |
| icted | dard | M      | strator |                                  |
|       |      | anager |         |                                  |
+=======+======+========+=========+==================================+
| O     | O    | O      | M       | This user is able to             |
|       |      |        |         | access [[public                  |
|       |      |        |         | templates]{.                     |
|       |      |        |         | ul}](https://help.skytap.com/Pub |
|       |      |        |         | lic_Templates.html) and [[public |
|       |      |        |         | assets]{.ul}](https://help.skyta |
|       |      |        |         | p.com/Using_Public_Assets.html). |
+-------+------+--------+---------+----------------------------------+
|       | O    | O      | M       | This user is able to [[import    |
|       |      |        |         | VMs]                             |
|       |      |        |         | {.ul}](https://help.skytap.com/i |
|       |      |        |         | mporting-vms-overview.html) into |
|       |      |        |         | Skytap.                          |
+-------+------+--------+---------+----------------------------------+
|       | O    | O      | M       | This user is able to [[export    |
|       |      |        |         | VMs]{.ul}](https://he            |
|       |      |        |         | lp.skytap.com/Exports.html) from |
|       |      |        |         | Skytap.                          |
+-------+------+--------+---------+----------------------------------+
|       | O    | O      | M       | This user is able to [[generate  |
|       |      |        |         | reports]{.ul}](https://he        |
|       |      |        |         | lp.skytap.com/Reports.html) from |
|       |      |        |         | Skytap.                          |
|       |      |        |         |                                  |
|       |      |        |         | Reporting can be enabled for the |
|       |      |        |         | entire account or just the       |
|       |      |        |         | user\'s department.              |
+-------+------+--------+---------+----------------------------------+
|       | O    | O      | O       | This user is able to             |
|       |      |        |         | set [[promiscuous                |
|       |      |        |         | mode]{                           |
|       |      |        |         | .ul}](https://help.skytap.com/En |
|       |      |        |         | abling_Promiscuous_Mode.html) on |
|       |      |        |         | VM network adapters for Skytap.\ |
|       |      |        |         | This permission is displayed     |
|       |      |        |         | when your customer account is    |
|       |      |        |         | enabled for promiscuous mode.    |
+-------+------+--------+---------+----------------------------------+

###### Groups

Groups help you manage large numbers of user permissions across multiple
projects. A user can belong to multiple groups and the groups can be
given permissions within Skytap. The platform supports up to 100 groups;
groups are managed by administrators and user managers.

###### Projects

Projects grant users access to other resources within Skytap. Users
always use the most permissive access rights granted, so if a named user
is granted Editor rights but is also member of a group with Viewer
permissions, they can use the Editor rights to perform actions on the
project.

Projects support the concept of an Automatic Role; this role is granted
to every new user on Skytap. This feature should be used sparingly to
prevent excessive user permissions.

*Project Permissions*

  -----------------------------------------------------------------------------------------------
  Owner           Manager   Editor   Participant   Viewer   Can do this
  --------------- --------- -------- ------------- -------- -------------------------------------
  ✔️              ✔️        ✔️       ✔️            ✔️       Access and use a running environment

  ✔️              ✔️        ✔️       ✔️            ✔️       Download assets

  ✔️              ✔️        ✔️       ✔️                     Control the power state of an
                                                            environment

  ✔️              ✔️        ✔️       **‡**                  Create an environment from a project
                                                            template

  ✔️              ✔️        ✔️       **‡**                  Copy environment

  ✔️              ✔️        ✔️                              Copy template

  ✔️              ✔️        ✔️                              Load an ISO

  ✔️              ✔️        ✔️                              Save an environment as a template

  ✔️              ✔️        ✔️                              Add, edit, and remove resources

  ✔️              ✔️        ✔️                              Share resources with other projects\
                                                            (as Editor or Manager in both
                                                            projects)

  ✔️              ✔️        ✔️                              Permanently delete VMs

  ✔️              ✔️                                        Add and remove project users

  ✔️                                                        Delete the project

  Administrator                                             Change project permissions

                                                            Permanently delete environments,
                                                            templates, and assets that are in the
                                                            project and owned by other users
  -----------------------------------------------------------------------------------------------

***‡**  The restricted user must be an editor or manager in at least
one project to perform these actions as a participant. For example, if
the user is a participant in project A and an editor in project B, the
user can create an environment from a template in project A. The new
environment is automatically added to project B (where the user has
permission to add resources). Restricted users cannot own environments,
templates, or assets outside of a project.*

*Project Example*

**Scenario:** The organisation wants to control and manage a set of
company-wide golden templates in your customer account. A small set of
users to maintain the templates (template creators), and all other users
in the account will have permission to create environments from those
templates (template users).

![projects](.//media/image6.png){width="5.6361100174978125in"
height="4.986805555555556in"}

*Figure 7 - Example of Project Implementation*

**Solution:** Create 2 projects:

-   **Golden Templates** -- Use this project to share golden templates
    with all users.

    -   Add all finalised, approved templates to this project.

    -   Add template creators to the project in
        the **Manager** role. **Managers** can:

        -   Add templates to the project or remove templates from the
            project.

        -   Add users to the project or remove users from the project.

    -   Add all other Skytap users to the project in
        the **Participant** role. **Participants** can create
        environments from templates in the project.

Enable an **automatic role** on the project. With this setting, every
new Skytap user is automatically added to this project as
a **Participant**.

-   **Staging** -- Use this project to share in-progress environments
    and templates between template creators.

    -   Add new, in-progress environments and templates to this project.

    -   Add template creators to the project in the **Manager** role.

    -   Do not add other Skytap users to this project. Because they
        don't have project access, they cannot view or use environments
        and templates in this project.

###### Departments

Departments allow you to model company departments, business units, or
project teams within the Skytap. With department monitoring and limits,
you can:

-   Create usage limits to cap the amount of storage, RAM hours, or
    concurrent RAM that department users can consume; this can guarantee
    that Skytap resources remain available to critical departments.

-   Create usage reports to track and charge-back usage by each
    department.

Each user can belong to one department, and the platform supports up to
100 departments.

###### Single Sign-On

Skytap can be configured for SAML based Single Sign-On, thus making it
compatible with Azure Directory Services, Corporate Active Directory and
authentication services such as Ping Identity or Okta. By integrating
with Single Sign-On organisations can enforce additional authentication
controls such as endpoint integrity and verification, location-based
authentication and conditional/just in time access.

  ---------------------------------------------------------------------------------------------------------------- ---------- -----------------------------------
  Application                                                                                                      Vendor     Reference Architecture

                                                                                                                              

  [[Workforce                                                                                                      Okta       
  Identity](https://www.okta.com/workforce-identity/)<https://azure.microsoft.com/en-gb/services/monitor/>]{.ul}              

  [[PingOne]{.ul}](https://www.pingidentity.com/en/cloud/pingone.html)\                                            Ping       
  [[PingFederate]{.ul}](https://www.pingidentity.com/en/software/pingfederate.html)                                Identity   
  ---------------------------------------------------------------------------------------------------------------- ---------- -----------------------------------

##### Labels

Usage labels help administrators annotate environments, templates, and
assets for more detailed usage reporting. After a label is added to a
resource, the label appears in usage reports whenever that resource is
used. The label data can be used for accounting chargebacks, cost
allocation, and usage trending analysis.

Single Value label categories can be attached only once per environment,
template, asset, or schedule. For example, a Single Value label of *Cost
Centre* could be used for chargebacks or cost allocation purposes.

Multi-value label categories are suited for general reporting, for
example, to group environments by applications in use.

#### Auditing

Auditing can be configured to send events to a Log Management tool or
Security Incident Event Monitoring tool. These events are sent using
Webhooks[^1], allowing for near real-time notifications to management
tooling enabling security engineers to respond more rapidly than would
otherwise be available.

Additionally, usage events can also be sent via Webhook to this tooling
or a separate service; this can be used to monitor capacity and spend to
prevent cloud sprawl or an economic denial of service.

  --------------------------------------------------------------------- ------------- ----------------------------------------
  Application                                                           Vendor        Reference Architecture

                                                                                      

  [[Azure                                                               Microsoft     ✔️[^2]
  Monitor]{.ul}](https://azure.microsoft.com/en-gb/services/monitor/)                 

  Splunk Enterprise                                                     Splunk        
  --------------------------------------------------------------------- ------------- ----------------------------------------

###  

### 

### 

### Management

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

##### Architecture

The Skytap platform's ability to deploy environments from templates
supports rapid application development and testing, but it can be
problematic for organisations that use operating systems that rely on
globally unique identifiers as part of their authentication chain. In
particular the Microsoft Windows operating systems use Security IDs
(SIDs) which must be entirely unique across all connected machines. If
two machines present with the same credentials across different networks
there will be a conflict and unexpected behaviour could occur.

###### Option 1 -- Centralized Directory

A centralized directory

###### Option 2 -- Independent Directory Synchronization between Environments

In this case the master directory held in the Management Environment
seeds directories below it, which are then independent.

#### Backup

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

### Edge Networking![](.//media/image11.png){width="1.5833333333333333in" height="5.645833333333333in"}

The Edge Networking capability provides access to resources outside of
the immediate environment in Skytap, and as such, represents a
significant area of exposure. Workloads running in the cloud have
legitimate needs to access services or resources either on the Internet
or behind the corporate network. To do this safely and efficiently
consolidating this ingress and egress into a centralised service can
help to enforce good practice and reduce risk.

The Outbound Proxy and Inbound Proxy filter is what Virtual Machines can
connect to and what in turn can connect to them. For example, an
Outbound Proxy may be configured to allow an Application Server to
request data from a public API; whereas, an Inbound Proxy (sometimes
called a protocol break) terminates external client connections to
protect the internal servers from direct internet exposure. The Inbound
Proxy can then perform validation on the request from the client, such
as determining if the client is a known bad actor or if the request is
appropriately formed.

The VPN or Private Connection provides secure and restricted
connectivity to on-premises or third-party cloud access in a controlled
manner. For example, only the Production environment may be allowed to
communicate over the Private Connection to the database running
on-premises; however, the Development/Test environments can be connected
to by the Engineering team via VPN.

Firewalls, Intrusion Detection Systems (IDS) and Intrusion Prevention
Systems (IPS) are typically consolidated onto a single device, but they
do provide distinctly different functionality. A Firewall can restrict
connectivity between devices based on source or destination information
sent with the traffic. In contrast, an IDS/IPS attempts to understand
the context of the traffic passing through and responds accordingly. The
IDS/IPS is designed to detect unauthorised intrusions on your network by
matching traffic flows to known signatures

When providing Internet or VPN access, this stack is typically
implemented, as below. With the reverse being true for outbound traffic.

![](.//media/image12.png){width="6.375in" height="1.125in"}

*Figure 11 - Typical Traffic Flow*[^3]

#### Example High Level Design

![A screenshot of a cell phone Description automatically
generated](.//media/image13.png){width="3.9348009623797027in"
height="6.621166885389326in"}

*Figure 12 - Example Internet and VPN Connection Scheme*

#### Outbound Proxy

An Outbound Proxy on the network edge should be configured to restrict
access to websites, Internet-connected services and external services to
only what is required for typical operation. For example, connection to
an application or operating systems update service is appropriate. An
outbound proxy can also be used for Data Loss Prevention (DLP) policy
enforcement.

##### Supported Implementations

  ----------------------------------------------------------------------------------- ------------- -------------------------------------
  Application                                                                         Vendor        Reference Architecture

                                                                                                    

  [[Squid]{.ul}](http://www.squid-cache.org/)                                         Squid         

  [[Web                                                                               McAfee        
  Gateway]{.ul}](https://www.mcafee.com/enterprise/en-gb/products/web-gateway.html)                 
  ----------------------------------------------------------------------------------- ------------- -------------------------------------

#### Inbound Proxy

The Inbound Proxy sometimes referred to as Reverse Proxy, sits between
the client and the webserver or application server hosted in Skytap. A
reverse proxy accepts a request from a client, forwards it to a server
that can fulfil it, and returns the server's response to the client.

The proxy enhances security by shielding the backend servers from the
outside network, and this prevents malicious clients from accessing them
directly to exploit any known vulnerabilities. It can also protect these
backend servers by rejecting traffic from blacklisted IPs or rating
limiting the number of connections from clients, reducing the risk of a
distributed denial-of-service (DDoS) attack.

##### Supported Implementations

  ----------------------------------------------------------------------------------- ------------- ----------------------------------------
  Application                                                                         Vendor        Reference Architecture

                                                                                                    

  [[NGINX]{.ul}](https://www.nginx.com/)                                              F5            

  [[BIG IP]{.ul}](https://www.f5.com/products/big-ip-services)                        F5            

  [[Squid]{.ul}](http://www.squid-cache.org/)                                         Squid         

  [[pfSense]{.ul}](https://www.netgate.com/solutions/pfsense/)                        Netgate       

  [[MVision                                                                           McAfee        
  Cloud]{.ul}](https://www.mcafee.com/enterprise/en-us/products/mvision-cloud.html)                 
  ----------------------------------------------------------------------------------- ------------- ----------------------------------------

#### VPN / Private Connection

Skytap supports Site-to-Site VPN as well as Private Network Connections
(PNCs) using [[Azure
ExpressRoute]{.ul}](https://azure.microsoft.com/en-gb/services/expressroute/)
and [[Equinix Cloud
Exchange]{.ul}](https://www.equinix.com/solutions/cloud-infrastructure/public-cloud/connectivity/).

##### Site-to-Site VPN

The Skytap [[Site-to-Site
VPN]{.ul}](https://help.skytap.com/vpn-configuration-parameters.html#VPNconfigurationoptions)
supports an IPSec VPN using IKEv1 or IKEv2 using Pre-Shared Keys (PSK),
AES 256 bit and Perfect Forward Secrecy (PFS).

The Skytap Site-to-Site VPN secures traffic that traverses the public
Internet, but not private connectivity. Multiple VPNs can be created for
high availability and to connect multiple corporate data centres to the
Skytap cloud.

![](.//media/image14.png){width="7.5in" height="4.291666666666667in"}

*Figure 13 - Example VPN Connection Scheme*

##### Private Network Connection (PNC)

An Azure ExpressRoute or Equinix Cloud Hub connection are referred to as
Private Network Connections (PNCs) in Skytap.

In the example below, the PNC connects the on-premises data centre with
the Skytap cloud environments. It should be noted that while this
connection is private, using Multiprotocol Label Switching (MPLS), which
logically isolates traffic, it is not however encrypted.

![](.//media/image15.png){width="7.5in" height="2.6527777777777777in"}

*Figure 14 - Example Private Connection Scheme*

When a PNC is used traffic between Skytap and the On-premises datacentre
or other cloud providers should be encrypted at the edge of the
environment using a Firewall to create the site-to-site connection or by
using point to point encryption from a service mesh network. Service
Mesh networking is covered in the **Internal Networking** section of
this document.

#### Firewall

A Firewall should be implemented to protect the edge of the Skytap
platform, both to defend the workloads running in Skytap but also any
onward connection to the corporate datacentre or other clouds.

Internet to Environments filtering should take place to only permit
acceptable connections, for example, HTTPS connection to the Inbound
Proxy but discard all other forms of traffic attempting to connect to
Environments directly.

Environments to Internet filtering should restrict egress of traffic
except via the Outbound Proxy; the proxy decides as to what external
sites and services are acceptable.

VPN/PNC to Environments, blanket access to Environments even from
private connections such as VPNs is inadvisable. Outside of machine to
machine connectivity to support application operations, such as Database
calls or Directory Lookups, user access should be brokered via a
Jump/Bastion host held in the Management environment.

##### Supported Implementations

  -------------------------------------------------------------- ------------- ----------------------------------------
  Application                                                    Vendor        Reference Architecture

                                                                               

  [[pfSense]{.ul}](https://www.netgate.com/solutions/pfsense/)   Netgate       

  [[BIG-IP]{.ul}](https://www.f5.com/products/big-ip-services)   F5            
  -------------------------------------------------------------- ------------- ----------------------------------------

#### Intrusion Detection System / Intrusion Prevention System

Typically Intrusion Detection Systems or Intrusion Prevention Systems
are consolidated on the firewall but shown here as a discrete capability
for completeness. The IDS/IPS performs a vital monitoring function to
alert administrators and security personnel of unauthorised attempts to
access the network. Intrusion is of particular concern with
internet-facing applications.

In Skytap an IDS/IPS must be placed in line with the traffic as port
mirroring is not supported, hence the preference to include it as part
of the Firewall capability.

##### Supported Implementations

  -------------------------------------------------------------------------------------------------- ------------- ----------------------------------------
  Application                                                                                        Vendor        Reference Architecture

                                                                                                                   

  [[pfSense]{.ul}](https://www.netgate.com/solutions/pfsense/)                                       Netgate       

  [[Network Security                                                                                 McAfee        
  Platform]{.ul}](https://www.mcafee.com/enterprise/en-gb/products/network-security-platform.html)                 

  [[Next Gen                                                                                         Palo Alto     
  Firewall]{.ul}](https://www.paloaltonetworks.com/network-security/next-generation-firewall)                      
  -------------------------------------------------------------------------------------------------- ------------- ----------------------------------------

### Virtual Machines

The Virtual Machines capability encompasses VM Templates;
Authentication, Authorisation and Accounting; Patching; Antivirus;
HIDS/HIPS; and Firewall. This area relates to the Environments running
in Skytap and any templates associated with them.

![A screenshot of a cell phone Description automatically
generated](.//media/image4.png){width="4.054116360454943in"
height="2.248853893263342in"}

*Figure 15 -- Virtual Machine Required Capabilities*

#### VM Templates

In Skytap the Virtual Machine template contains a point in time and
idempotent copy of an entire Environment. An Environment or Template
includes any connectivity it may have, such as Internet access or VPN
connections, the state of any x86 or Power Virtual Machines (LPARs)
including their memory state (if x86), disks, MAC addresses and other
configuration items.

Pre-configured templates should be made available to users of the Skytap
platform that conform to organisational security policies. These
templates should have pre-installed anti-virus and Host-Based Intrusion
Detection Systems/Host-Based Intrusion Prevention Systems; installed
licenses; up to date patching for security vulnerabilities of operating
systems and applications; and an enabled Firewall.

From these base templates or Golden Images, users on the Skytap platform
can build or operate their workloads from a known good configuration.
The use of Golden Images prevents accidental misconfiguration or
unauthorised use of operating systems which may not align with
organisational standards or software licensing exposure.

These base templates can be made available to users via Projects, as
described earlier in this document.

#### Authentication, Authorisation and Accounting

Access to Virtual Machines and LPARs in Skytap Environments should be
centralised, and the use of a directory service enables that. A local
directory server, in the Environment, can be made available to Virtual
Machines and LPARs or authentication traffic can be passed to the Shared
Services environment.

Password-based authentication should be avoided in most cases, with
public/private key being preferred. Certificate authentication for users
is atypical in Windows deployments but is supportable and considerably
more secure.

Lightweight Directory Access Protocol (LPAP) is a supported
authentication technology on Linux, IBM i and AIX.

Terminal access should be protected with certificates, SSH can be
configured to use signed certificates validated by the Secrets Manager
and should be used to reduce key reuse and automated repudiation of
compromised keys or users no longer with authority to connect. With IBM
i the TN5250 'Green-Screen' service should be secured using SSL/TLS
certificates, some guidance is documented
[[here]{.ul}](https://www.ibm.com/support/pages/configuring-ssl-telnet-and-host-servers-server-authentication-first-time),
as the Green-Screen is a telnet based session which has no encryption
and is susceptible to interception and exploitation.

##### Supported Implementations

  ------------- ----------- --------- ----- ------- ----- ----- -------------------
  Application   Vendor      x86             Power               Reference
                                                                Architecture

                            Windows         Linux   AIX   IBM i Linux

  Active        Microsoft   ✔️        ✔️    ✔️            ✔️    
  Directory                                                     

  LDAP                                                          

  SAML 2.0                                                      
  ------------- ----------- --------- ----- ------- ----- ----- -------------------

#### Patching

The regular updating of operating systems and applications with security
patches prevents vulnerabilities being exposed and exploited by bad
actors. A patching service should be configured to regularly apply new
security patches, these patches should also be integrated into any
master/gold templates in use on the Skytap platform.

##### Supported Implementations

  -------------- ----------- --------- ----- ------- ----- ----- ------------------
  Application    Vendor      x86             Power               Reference
                                                                 Architecture

                             Windows         Linux   AIX   IBM i Linux

  Ansible        Red Hat     ✔️        ✔️    ✔️            ✔️    

  Windows Update Microsoft   ✔️                                  

                                                                 
  -------------- ----------- --------- ----- ------- ----- ----- ------------------

#### Antivirus

All Virtual Machines and LPARs should have Antivirus protecting them,
they should be configured to update regularly (hourly), scan frequently
and report any events centrally.

##### Supported Implementations

  ------------- ------------- --------- ------ ------- ----- ----- ------------------
  Application   Vendor        x86              Power               Reference
                                                                   Architecture

                              Windows          Linux   AIX   IBM i Linux

                Trend         ✔️        ✔️     ✔️                  

                HelpSystems                            ✔️          

                Sophos        ✔️        ✔️     ✔️            ✔️    
  ------------- ------------- --------- ------ ------- ----- ----- ------------------

#### HIDS/HIPS

A Host-Based Intrusion Detection System or Host-Based Intrusion
Prevention System can help to secure against unauthorised access.

Typically these applications detect malicious login traffic, changes to
privileged files or the installation of software outside the approved
process. Logs generated from these systems should be centralised, with
the potential to invoke automated defences such as quarantine.

In some production systems, any direct login to a Virtual Machine or
LPAR is considered an attack and would cause the VM to self-destruct.

##### Supported Implementations

  ------------- ---------- --------- ------ ------- ----- ------- ------------------
  Application   Vendor     x86              Power                 Reference
                                                                  Architecture

                           Windows          Linux   AIX   IBM i   Linux

  Fail2Ban                           ✔️                   ✔️      

  TripWire      Tripwire   ✔️        ✔️     ✔️            ✔️      
  Enterprise                                                      

                TrapX      ✔️        ✔️                   ✔️      
  ------------- ---------- --------- ------ ------- ----- ------- ------------------

#### OS Firewall

Skytap enables local network communication by default and OS level
firewall protection should be enabled to restrict open ports only to
those that are approved. For example, a database server should only
allow database traffic from known application servers; any other
attempts to connect to a database management port should be ignored and
reported.

Supported Implementations

  -------------- ----------- --------- ----- ------- ----- ----- ------------------
  Application    Vendor      x86             Power               Reference
                                                                 Architecture

                             Windows         Linux   AIX   IBM i Linux

  Windows        Microsoft   ✔️                                  
  Firewall                                                       

  iptables       N/A                   ✔️                  ✔️    

  ipfilters      IBM                         ✔️                  
  -------------- ----------- --------- ----- ------- ----- ----- ------------------

#### Example High Level Design

![](.//media/image16.png){width="5.60416447944007in"
height="8.240279965004374in"}

### Internal Networking

Several of these architectural building blocks are duplicated in the
Edge Networking section and this is by design, each environment should
be secured independently of each other by creating a defense in depth
approach. This means that if one layer is compromised the crown jewels
will still be secure.

![](.//media/image4.png){width="4.053839676290464in"
height="2.1838888888888888in"}

*Figure 15 -- Internal Networking Required Capabilities*

Two additional services present in the environment internal networking,
a Service Mesh and DNS/Service Discovery. A Service Mesh allows for
secured communication between application components or virtual
machines, though typically microservices, but can also fulfil some of
the functionality of a firewall. DNS and Service Discovery allows the
automated discovery of application services within an environment, for
example Domain Services.

#### Example High Level Design

Drawing

##### 

#### Firewall

Internal network firewalls can be deployed where it is necessary to
restrict or control traffic between networks running inside Skytap.
These firewalls are deployed as virtual machines within your Skytap
environments.

##### Supported Implementations

  -------------------------------------------------------------- ------------- ----------------------------------------
  Application                                                    Vendor        Reference Architecture

                                                                               

  [[pfSense]{.ul}](https://www.netgate.com/solutions/pfsense/)   Netgate       

                                                                 F5            
  -------------------------------------------------------------- ------------- ----------------------------------------

#### Service Mesh

A Service Mesh can connect multiple application components or
microservices together in a way that simplifies their use. For example,
a Database running on AIX in Skytap could be connected to a frontend
application hosted in a Hyperscale cloud provider without having to
manually configure routing at each layer.

![A close up of text on a white background Description automatically
generated](.//media/image17.png){width="2.932292213473316in"
height="2.837594050743657in"}

In addition to network simplification a Service Mesh can also provide
fine-grained access control, mutual TLS authentication and remove
network appliances (such as load balancers).

##### Supported Implementations

  -------------------------------------------------------------------------- ----------- --------- ----- ------- ----- ----- ------------------
  Application                                                                Vendor      x86             Power               Reference
                                                                                                                             Architecture

                                                                                         Windows         Linux   AIX   IBM i Linux

  [[Consul]{.ul}](https://www.hashicorp.com/products/consul/service-mesh/)   HashiCorp   ✔️                                  

                                                                                                                             

                                                                                                                             
  -------------------------------------------------------------------------- ----------- --------- ----- ------- ----- ----- ------------------

#### DNS/Service Discovery

Within an environment a DNS service can be used to provide information
to applications on where to send traffic without using the IP address.
This is beneficial on the Skytap platform even though IP address
portability is supported, in case multiple concurrent environments are
running.

Service Discovery helps applications discover other services they
require, but can also validate that the endpoints are healthy before
directing traffic. Service Discovery can also replace some load
balancing functionality.

![A picture containing room Description automatically
generated](.//media/image18.png){width="2.625in"
height="2.710275590551181in"}

##### Supported Implementations

  ---------------------------------------------------------------------------------------------------------- ----------- --------- ----- ---------------------------------
  Application                                                                                                Vendor      x86             Reference Architecture

                                                                                                                         Windows         Linux

  [[Consul]{.ul}](https://www.hashicorp.com/products/consul/service-discovery/)                              HashiCorp   ✔️              

  Active Directory                                                                                           Microsoft                   

  [[Umbrella]{.ul}](https://umbrella.cisco.com/?_ga=2.214455253.393431820.1607963050-217404651.1607963050)   Cisco                       
  ---------------------------------------------------------------------------------------------------------- ----------- --------- ----- ---------------------------------

#### Outbound Proxy

Outbound proxies can also be configured directly within your workload
networks if needed to provide a layered security stance or if the
workload requires it. This kind of proxy type of proxy is commonly used
with stand-alone workloads where an application is expected to function
in isolation.

##### Supported Implementations

  -------------------- ------------- -------------------------------------
  Application          Vendor        Reference Architecture

                                     

                                     

                                     
  -------------------- ------------- -------------------------------------

#### Inbound Proxy

Inbound proxies deployed within a workload environment are typically
used to handle proxying required within a workload or between workload
environments.

##### Supported Implementations

  -------------------------------------------------------------- ------------- ----------------------------------------
  Application                                                    Vendor        Reference Architecture

                                                                               

  [[NGINX]{.ul}](https://www.nginx.com/)                         F5            

  [[Squid]{.ul}](http://www.squid-cache.org/)                    Squid         

  [[pfSense]{.ul}](https://www.netgate.com/solutions/pfsense/)   Netgate       
  -------------------------------------------------------------- ------------- ----------------------------------------

## Security as a Service

Given the breadth of security controls available across multiple cloud
vendors, the changing threat landscape, and the need to support modern
and legacy workloads in the enterprise space, it can be difficult for
organisations to provide those services internally.

Skytap works with a variety of Security as a Service vendors who can
manage your cloud workloads and who have expert knowledge in securing
cloud workloads. These partners can supplement an organisation's
internal security support team and should be considered as part of a
wider cloud strategy.

  ----------------------------------------------------------------------------------------
  Vendor        Capabilities                                                         
  ------------- -------------- ----- ----- ----- ----- ----- ----- ----- ----- ----- -----
                A              B     C     D     E     F     G     H     I     J     K

  Meridian IT   ✔️                                                                   

  IBM                                                                                ✔️

  HelpSystems                                                                        

                                                                                     
  ----------------------------------------------------------------------------------------

### Legend

  -----------------------------------------------------------------------
  Key       Capability[^4]
  --------- -------------------------------------------------------------
  A         Business continuity and disaster recovery (BCDR or BC/DR)

  B         Continuous monitoring

  C         Data loss prevention (DLP)

  D         Email security

  E         Encryption

  F         Identity and access management (IAM)

  G         Network security

  H         Security assessment

  I         Security information and event management (SIEM)

  J         Vulnerability scanning

  K         Web security
  -----------------------------------------------------------------------

# References

Wikipedia. (2020, April 4). *Webhook*. Retrieved 4 5, 2020, from
Wikipedia: The Free Encyclopedia: http://en.wikipedia.org/wiki/Webhook

[^1]: A webhook in web development is a method of augmenting or altering
    the behaviour of a web page, or web application, with custom
    callbacks. These callbacks may be maintained, modified, and managed
    by third-party users and developers who may not necessarily be
    affiliated with the originating website or application. (Wikipedia,
    2020)

[^2]: *Skytap: "*[[Sending Skytap Platform logs to Azure
    Monitor]{.ul}](https://medium.com/skytap-cloud/sending-skytap-platform-logs-to-azure-monitor-b0642221941e)*".
    Authored 6 Jan 2020.*

[^3]: A Load Balancer is shown for reference but is not within the scope
    of this document.

[^4]: *Cloud Security Alliance. [[\"Defined Categories of Security as a
    Service\"]{.ul}](https://downloads.cloudsecurityalliance.org/assets/research/security-as-a-service/csa-categories-securities-prep.pdf)
    (PDF). Cloud Security Alliance. Retrieved 5 June 2017.*
