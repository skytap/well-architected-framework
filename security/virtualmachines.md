---
title: virtual machines
author: John Bradshaw, Principal Architect
permalink: /security/virtual-machines/
---

### Virtual machines

The virtual machines capability encompasses VM templates; authentication, authorization, and accounting; patching; antivirus; HIDS/HIPS; and firewall. This area relates to the environments running in {{site.Brand}} and any templates associated with them.

![Virtual Machine Required Capabilities](https://skytap.github.io/well-architected-framework/security/virtualmachinesmedia/media/image1.png)
_Figure 15 – virtual machine required capabilities_

#### VM templates

In {{site.Brand}} the virtual machine template contains a point in time and idempotent copy of an entire environment. An environment or template includes any connectivity it may have, such as Internet access or VPN connections, the state of any x86 or Power virtual machines (LPARs) including their memory state (if x86), disks, MAC addresses and other configuration items.

Pre-configured templates should be made available to users of the {{site.Brand}} platform that conform to organizational security policies. These templates should have pre-installed anti-virus and host-based intrusion detection systems or host-based intrusion prevention systems; installed licenses; up-to-date patching for security vulnerabilities of operating systems and applications; and an enabled firewall.

From these base templates or golden images, users on the {{site.Brand}} platform can build or operate their workloads from a known good configuration. The use of golden images prevents accidental misconfiguration or unauthorized use of operating systems which may not align with organizational standards or software licensing exposure.

These base templates can be made available to users via projects, as described earlier in this document.

#### Authentication, authorization, and accounting

Access to virtual machines and LPARs in {{site.Brand}} environments should be centralized, and the use of a directory service enables that. A local directory server, in the environment, can be made available to virtual machines and LPARs or authentication traffic can be passed to the shared services environment.

Password-based authentication should be avoided in most cases, with public/private key being preferred. Certificate authentication for users is atypical in Windows deployments but is supportable and considerably more secure.

Lightweight Directory Access Protocol (LPAP) is a supported authentication technology on Linux, IBM i, and AIX.

Terminal access should be protected with certificates, SSH can be configured to use signed certificates validated by the secrets manager and should be used to reduce key reuse and automated repudiation of compromised keys or users no longer with authority to connect. With IBM i the TN5250 'Green-Screen' service should be secured using SSL/TLS certificates, some guidance is documented for TLS configuration can be found ![here](https://www.ibm.com/support/pages/configuring-telnet-and-host-servers-server-authentication-tls-first-time), and additional guidance for SSL and Windows machines can be found ![here](https://www.ibm.com/support/pages/configuring-ibm-i-access-windows-ssl-use-client-authentication). Note that the 'green screen' is a telnet based session which has no encryption and is susceptible to interception and exploitation.

##### Supported implementations

| Application      | Vendor    | x86             || Power            |||
|------------------|-----------|:-------:|:-----:|:---:|:-----:|:-----:|
|                  |           | Windows | Linux | AIX | IBM i | Linux |
| Active Directory | Microsoft |    √    |   √   |  √  |       |   √   |
| LDAP             |           |         |       |     |       |       |
| SAML 2.0         |           |         |       |     |       |       |

#### Patching

The regular updating of operating systems and applications with security patches prevents vulnerabilities being exposed and exploited by bad actors. A patching service should be configured to regularly apply new security patches, these patches should also be integrated into any master/gold templates in use on the {{site.Brand}} platform.

##### Supported implementations

| Application    | Vendor             | x86             || Power            |||
|----------------|--------------------|:-------:|:-----:|:---:|:-----:|:-----:|
|                |                    | Windows | Linux | AIX | IBM i | Linux |
| Windows Update | Microsoft          |    √    |       |     |       |       |
| Ansible        | Red Hat Enterprise |    √    |   √   |  √  |       |   √   |

#### Antivirus

All virtual machines and LPARs should have antivirus protecting them, they should be configured to update regularly (hourly), scan frequently and report any events centrally.

##### Supported implementations

| Application | Vendor      | x86            || Power             |||
|-------------|-------------|:-------:|:-----:|:---:|:-----:|:-----:|
|             |             | Windows | Linux | AIX | IBM i | Linux |
|             | Trend       |    √    |   √   |  √  |       |       |
|             | HelpSystems |         |       |     |   √   |       |
|             | Sophos      |    √    |   √   |  √  |       |   √   |

#### HIDS/HIPS

A host-based intrusion detection system or host-based intrusion prevention system can help to secure against unauthorized access.

Typically these applications detect malicious login traffic, changes to privileged files or the installation of software outside the approved process. Logs generated from these systems should be centralized, with the potential to invoke automated defences such as quarantine.

In some production systems, any direct login to a virtual machine or∫ LPAR is considered an attack and would cause the VM to self-destruct.

##### Supported implementations

| Application         | Vendor   | x86            || Power             |||
|---------------------|----------|:-------:|:-----:|:---:|:-----:|:-----:|
|                     |          | Windows | Linux | AIX | IBM i | Linux |
| Fail2Ban            | Trend    |         |   √   |  √  |       |       |
| TripWire Enterprise | TripWire |    √    |   √   |  √  |       |   √   |
|                     | TrapX    |    √    |   √   |     |   √   |       |

#### OS firewall

{{site.Brand}} enables local network communication by default and OS-level firewall protection should be enabled to restrict open ports only to those that are approved. For example, a database server should only allow database traffic from known application servers; any other attempts to connect to a database management port should be ignored and reported.

##### Supported implementations

| Application                                | Vendor    | x86            || Power             |||
|--------------------------------------------|-----------|:-------:|:-----:|:---:|:-----:|:-----:|
|                                            |           | Windows | Linux | AIX | IBM i | Linux |
| Microsoft Defender for Endpoint - firewall | Microsoft |    √    |   *   |     |       |       |
| iptables                                   |           |         |   √   |     |       |   √   |
| ipfilters                                  | IBM       |         |       |  √  |       |       |

#### Example high-level design

![design](https://skytap.github.io/well-architected-framework/security/virtualmachinesmedia/media/image2.png)
