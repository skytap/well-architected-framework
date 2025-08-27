---
title: Key Security Areas
author: John Bradshaw, Principal Architect
permalink: /security/key-security-areas/
---

## Key Security Areas

Within {{site.Brand}}, there are five areas to build a control framework around:
Platform, Resource Management, Internal Networking, Edge Networking, and
Virtual Machines.

For each of these areas and subsections this document will provide
standardized designs and partner points of contact where appropriate.

### Platform

The {{site.Brand}} platform provides controls for Security Policies;
Notifications; Authentication, Authorization and Accounting; and
Auditing.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/security/media/image4.png" width="500">

*Figure 1 - Platform Required Capabilities*

#### Security Policies

The security policies control access to the platform for authenticated
users. This can restrict connectivity to approved IP addresses (such as
those on the corporate network), password complexity rules (for those
not using SSO) and session timeouts (prompting for re-authentication).
{{site.Brand}} recommends that access is limited to the corporate network
subnets, or alternatively use a virtual machine as a Bastion host to
access the {{site.Brand}} dashboard securely from the {{site.Brand}} infrastructure.
More information on access policies is available
[here]](https://help.skytap.com/setting-access-policies.html)

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/security/media/image5.png" width="800">

*Figure 2 - Access Policies available within the {{site.Brand}} Dashboard*

#### Notifications

Notifications can be used to generate automated email alerts based on
account usage such as capacity thresholds in specific regions or
long-running virtual machines.

#### Authentication, Authorization and Accounting

##### Users, Groups, Projects and Departments

The Users, Groups, Projects and Departments are used to control Cost
and/or Access to services; they should be configured to align with your
organization's best business processes and operational best practices.

  |                    |     Cost Control    |     Access Control    |
|--------------------|---------------------|-----------------------|
|     Users          |     ✔️               |     ✔️                 |
|     Groups         |                     |     ✔️                 |
|     Projects       |                     |     ✔️                 |
|     Departments    |     ✔️               |                       |

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
resources within {{site.Brand}}. The user manager role is best for users who
need to manage and organize users and groups but who don't need full
administrator capabilities. The administrator role is best for trusted
users in your organization who need to manage users, resources, and
account-wide settings.

User accounts should be created as Restricted Users unless those
individuals require elevated permissions within the platform.

*User Permissions*

  |      Restricted     |      Standard     |      User Manager     |      Administrator     |      Can do this                                                                                        |
|---------------------|-------------------|-----------------------|------------------------|---------------------------------------------------------------------------------------------------------|
|     ✔️               |     ✔️             |     ✔️                 |     ✔️                  |     Access shared project resources                                                                     |
|     *               |     ✔️             |     ✔️                 |     ✔️                  |     Create and own projects                                                                             |
|     †               |     ✔️             |     ✔️                 |     ✔️                  |     Create environments, templates, and assets                                                          |
|                     |                   |     ✔️                 |     ✔️                  |     Create and edit users and groups                                                                    |
|                     |                   |     ✔️                 |     ✔️                  |     Delete groups                                                                                       |
|                     |     ‡             |     ‡                 |     ✔️                  |     Create and view reports                                                                             |
|                     |                   |                       |     ✔️                  |     Create and edit departments                                                                         |
|                     |                   |                       |     ✔️                  |     Delete users                                                                                        |
|                     |                   |                       |     ✔️                  |     Create and edit account-wide settings (password policies,   access policies, usage limits, etc.)    |
|                     |                   |                       |     ✔️                  |     Edit and delete environments, templates, and assets owned   by all users in the account.            |

*\* A restricted user can't create a project, but another user can make
a restricted user a project owner.\
† Restricted users have limited permission to create environments.\
‡ An administrator must grant reporting privileges for a user to create
and view reports.*

{{site.Brand}} has additional permissions that can be enabled or disabled for
each user. Some of these permissions are displayed only when specific
features are enabled in the platform. The table below shows the
additional permissions that are **optional (O)** for each type of user.

-   Most permissions are **mandatory (M)** for administrators and can't
    be disabled.

-   Restricted users can't have most permissions enabled.

*Extended User Permissions*

|      Restricted     |      Standard     |      User Manager     |      Administrator     |      Permission                                                                                                                                                                     |
|---------------------|-------------------|-----------------------|------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     O               |     O             |     O                 |     M                  |     This user is able to   access public   templates and public assets.                                                                                                             |
|                     |     O             |     O                 |     M                  |     This user is able to import VMs into   {{site.Brand}}.                                                                                                                                  |
|                     |     O             |     O                 |     M                  |     This user is able to export VMs from {{site.Brand}}.                                                                                                                                    |
|                     |     O             |     O                 |     M                  |     This user is able to generate reports from   {{site.Brand}}.     Reporting can be enabled for   the entire account or just the user's department.                                       |
|                     |     O             |     O                 |     O                  |     This user is able to set promiscuous   mode on VM network adapters for {{site.Brand}}.    This permission is displayed when your customer account is enabled for promiscuous   mode.    |

#### Groups

Groups help you manage large numbers of user permissions across multiple
projects. A user can belong to multiple groups and the groups can be
given permissions within {{site.Brand}}. The platform supports up to 100 groups;
groups are managed by administrators and user managers.

#### Projects

Projects grant users access to other resources within {{site.Brand}}. Users
always use the most permissive access rights granted, so if a named user
is granted Editor rights but is also member of a group with Viewer
permissions, they can use the Editor rights to perform actions on the
project.

Projects support the concept of an Automatic Role; this role is granted
to every new user on {{site.Brand}}. This feature should be used sparingly to
prevent excessive user permissions.

*Project Permissions*

| Owner         | Manager | Editor | Participant | Viewer | Can do this                                                                                               |
|---------------|---------|--------|-------------|--------|-----------------------------------------------------------------------------------------------------------|
| ✔️             | ✔️       | ✔️      | ✔️           | ✔️      | Access and use a running environment                                                                      |
| ✔️             | ✔️       | ✔️      | ✔️           | ✔️      | Download assets                                                                                           |
| ✔️             | ✔️       | ✔️      | ✔️           |        | Control the power state of an environment                                                                 |
| ✔️             | ✔️       | ✔️      | ‡           |        | Create an environment from a project template                                                             |
| ✔️             | ✔️       | ✔️      | ‡           |        | Copy environment                                                                                          |
| ✔️             | ✔️       | ✔️      |             |        | Copy template                                                                                             |
| ✔️             | ✔️       | ✔️      |             |        | Load an ISO                                                                                               |
| ✔️             | ✔️       | ✔️      |             |        | Save an environment as a template                                                                         |
| ✔️             | ✔️       | ✔️      |             |        | Add, edit, and remove resources                                                                           |
| ✔️             | ✔️       | ✔️      |             |        | Share resources with other projects    (as Editor or Manager in both projects)                            |
| ✔️             | ✔️       | ✔️      |             |        | Permanently delete VMs                                                                                    |
| ✔️             | ✔️       |        |             |        | Add and remove project users                                                                              |
| ✔️             |         |        |             |        | Delete the project                                                                                        |
| Administrator |         |        |             |        | Change project permissions                                                                                |
| Administrator |         |        |             |        | Permanently delete environments, templates, and assets   that are in the project and owned by other users |



***‡**  The restricted user must be an editor or manager in at least
one project to perform these actions as a participant. For example, if
the user is a participant in project A and an editor in project B, the
user can create an environment from a template in project A. The new
environment is automatically added to project B (where the user has
permission to add resources). Restricted users cannot own environments,
templates, or assets outside of a project.*

*Project Example*

**Scenario:** The organization wants to control and manage a set of
company-wide golden templates in your customer account. A small set of
users to maintain the templates (template creators), and all other users
in the account will have permission to create environments from those
templates (template users).

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/security/media/image6.png" width="500">

*Figure 3 - Example of Project Implementation*

**Solution:** Create 2 projects:

-   **Golden Templates** -- Use this project to share golden templates
    with all users.

    -   Add all finalized, approved templates to this project.

    -   Add template creators to the project in
        the **Manager** role. **Managers** can:

        -   Add templates to the project or remove templates from the
            project.

        -   Add users to the project or remove users from the project.

    -   Add all other {{site.Brand}} users to the project in
        the **Participant** role. **Participants** can create
        environments from templates in the project.

Enable an **automatic role** on the project. With this setting, every
new {{site.Brand}} user is automatically added to this project as
a **Participant**.

-   **Staging** -- Use this project to share in-progress environments
    and templates between template creators.

    -   Add new, in-progress environments and templates to this project.

    -   Add template creators to the project in the **Manager** role.

    -   Do not add other {{site.Brand}} users to this project. Because they
        don't have project access, they cannot view or use environments
        and templates in this project.

#### Departments

Departments allow you to model company departments, business units, or
project teams within the {{site.Brand}}. With department monitoring and limits,
you can:

-   Create usage limits to cap the amount of storage, RAM hours, or
    concurrent RAM that department users can consume; this can guarantee
    that {{site.Brand}} resources remain available to critical departments.

-   Create usage reports to track and charge-back usage by each
    department.

Each user can belong to one department, and the platform supports up to
100 departments.

#### Single Sign-On

{{site.Brand}} can be configured for SAML based Single Sign-On, thus making it
compatible with Azure Directory Services, Corporate Active Directory and
authentication services such as Ping Identity or Okta. By integrating
with Single Sign-On organizations can enforce additional authentication
controls such as endpoint integrity and verification, location-based
authentication and conditional/just in time access.

----------------------------------------------------------------------------------------------------------------

#### Application <!-- Vendor Reference Architecture -->
            
<a href="https://www.okta.com/workforce-identity/" target="_blank">Workforce Okta Identity</a>

<a href="https://www.pingidentity.com/en/cloud/pingone.html" target="_blank">PingOne</a>

<a href="https://www.pingidentity.com/en/software/pingfederate.html" target="_blank">PingFederate</a>

----------------------------------------------------------------------------------------------------------------

##### Labels

Usage labels help administrators annotate environments, templates, and
assets for more detailed usage reporting. After a label is added to a
resource, the label appears in usage reports whenever that resource is
used. The label data can be used for accounting chargebacks, cost
allocation, and usage trending analysis.

Single Value label categories can be attached only once per environment,
template, asset, or schedule. For example, a Single Value label of *Cost
Center* could be used for chargebacks or cost allocation purposes.

Multi-value label categories are suited for general reporting, for
example, to group environments by applications in use.

#### Auditing

Auditing can be configured to send events to a Log Management tool or
Security Incident Event Monitoring tool. These events are sent using
Webhooks, allowing for near real-time notifications to management
tooling enabling security engineers to respond more rapidly than would
otherwise be available.

Additionally, usage events can also be sent via Webhook to this tooling
or a separate service; this can be used to monitor capacity and spend to
prevent cloud sprawl or an economic denial of service.

--------------------------------------------------------------------- ------------- ----------------------------------------
#### Application <!-- Vendor        Reference Architecture -->

<a href="https://azure.microsoft.com/en-gb/services/monitor/" target="_blank">Microsoft Azure Monitor</a>

--------------------------------------------------------------------- ------------- ----------------------------------------

### Next steps

**Main Overview**
> [{{site.Brand}} Well-Architected Framework]({{ site.navigation.Home }})

**Operational Excellence**
> [{{site.Brand}} Operational Excellence Pillar]({{ site.navigation.Operations }})

**Resiliency**
> [{{site.Brand}} Resiliency Pillar]({{ site.navigation.Resiliency }})

**Security**
> [{{site.Brand}} Security Pillar]({{ site.navigation.Security }})
> * [Security Management]({{ site.navigation.Security }}security-management)
> * [Edge Networking]({{ site.navigation.Security }}edge-networking)
> * [Virtual Machines]({{ site.navigation.Security }}virtual-machines)
> * [Internal Networking]({{ site.navigation.Security }}internal-networking)
> * [Security as a Service]({{ site.navigation.Security }}security-as-a-service)
