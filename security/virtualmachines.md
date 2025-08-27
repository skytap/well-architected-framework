---
title: Virtual Machines
author: John Bradshaw, Principal Architect
permalink: /security/virtual-machines/
---

### Virtual Machines

The Virtual Machines capability encompasses VM Templates; Authentication, Authorization and Accounting; Patching; Antivirus; HIDS/HIPS; and Firewall. This area relates to the Environments running in {{site.Brand}} and any templates associated with them.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/security/virtualmachinesmedia/media/image1.png" width="800">

*Figure 15 -- Virtual Machine Required Capabilities*

#### VM Templates

In {{site.Brand}} the Virtual Machine template contains a point in time and idempotent copy of an entire Environment. An Environment or Template includes any connectivity it may have, such as Internet access or VPN connections, the state of any x86 or Power Virtual Machines (LPARs) including their memory state (if x86), disks, MAC addresses and other configuration items.

Pre-configured templates should be made available to users of the {{site.Brand}} platform that conform to organizational security policies. These templates should have pre-installed anti-virus and Host-Based Intrusion Detection Systems/Host-Based Intrusion Prevention Systems; installed licenses; up to date patching for security vulnerabilities of operating systems and applications; and an enabled Firewall.

From these base templates or Golden Images, users on the {{site.Brand}} platform can build or operate their workloads from a known good configuration. The use of Golden Images prevents accidental misconfiguration or unauthorized use of operating systems which may not align with organizational standards or software licensing exposure.

These base templates can be made available to users via Projects, as described earlier in this document.

#### Authentication, Authorization and Accounting

Access to Virtual Machines and LPARs in {{site.Brand}} Environments should be centralized, and the use of a directory service enables that. A local directory server, in the Environment, can be made available to Virtual Machines and LPARs or authentication traffic can be passed to the Shared Services environment.

Password-based authentication should be avoided in most cases, with public/private key being preferred. Certificate authentication for users is atypical in Windows deployments but is supportable and considerably more secure.

Lightweight Directory Access Protocol (LPAP) is a supported authentication technology on Linux, IBM i and AIX.

Terminal access should be protected with certificates, SSH can be configured to use signed certificates validated by the Secrets Manager and should be used to reduce key reuse and automated repudiation of compromised keys or users no longer with authority to connect. With IBM i the TN5250 'Green-Screen' service should be secured using SSL/TLS certificates, some guidance is documented for TLS configuration can be found<a href="https://www.ibm.com/support/pages/configuring-telnet-and-host-servers-server-authentication-tls-first-time" target="_blank"> here</a>, and additional guidance for SSL and Windows machines can be found<a href="https://www.ibm.com/support/pages/configuring-ibm-i-access-windows-ssl-use-client-authentication" target="_blank">  here</a>. Its important to note that the 'Green-Screen' is a telnet based session which has no encryption and is susceptible to interception and exploitation.

##### Supported Implementations

|    Application   |      Vendor      |      x86         |                |      Power     |                  |                |
| ---------------- | ---------------- | ---------------- | -------------- | -------------- | ---------------- | -------------- |
|                  |                  |      Windows     |      Linux     |      AIX       |       IBM i      |      Linux     |
| Active Directory |     Microsoft    | ✔️                | ✔️              | ✔️              |                  | ✔️              |
| LDAP             |                  |                  |                |                |                  |                |
| SAML 2.0         |                  |                  |                |                |                  |                |

#### Patching

The regular updating of operating systems and applications with security
patches prevents vulnerabilities being exposed and exploited by bad
actors. A patching service should be configured to regularly apply new
security patches, these patches should also be integrated into any
master/gold templates in use on the {{site.Brand}} platform.

##### Supported Implementations

|      Application     |      Vendor        |      x86         |                |      Power     |                |                |
|----------------------|--------------------|------------------|----------------|----------------|----------------|----------------|
|                      |                    |      Windows     |      Linux     |      AIX       |      IBM i     |      Linux     |
| Windows Update       |     Microsoft      | ✔️                |                |                |                |                |
| Ansible              | Red Hat Enterprise | ✔️                | ✔️              | ✔️              |                | ✔️              |

#### Antivirus

All Virtual Machines and LPARs should have Antivirus protecting them,
they should be configured to update regularly (hourly), scan frequently
and report any events centrally.

##### Supported Implementations

|      Application     |      Vendor     |      x86         |                |      Power     |                |                |
|----------------------|-----------------|------------------|----------------|----------------|----------------|----------------|
|                      |                 |      Windows     |      Linux     |      AIX       |      IBM i     |      Linux     |
|                      | Trend           | ✔️                | ✔️              | ✔️              |                |                |
|                      | HelpSystems     |                  |                |                | ✔️              |                |
|                      | Sophos          | ✔️                | ✔️              | ✔️              |                | ✔️              |

#### HIDS/HIPS

A Host-Based Intrusion Detection System or Host-Based Intrusion Prevention System can help to secure against unauthorized access.

Typically these applications detect malicious login traffic, changes to privileged files or the installation of software outside the approved process. Logs generated from these systems should be centralized, with the potential to invoke automated defences such as quarantine.

In some production systems, any direct login to a Virtual Machine or∫ LPAR is considered an attack and would cause the VM to self-destruct.

##### Supported Implementations

|      Application     |      Vendor     |      x86         |                |      Power     |                |                |
|----------------------|-----------------|------------------|----------------|----------------|----------------|----------------|
|                      |                 |      Windows     |      Linux     |      AIX       |      IBM i     |      Linux     |
| Fail2Ban             | Trend           |                  | ✔️              | ✔️              |                |                |
| TripWire Enterprise  | TripWire        | ✔️                | ✔️              | ✔️              |                | ✔️              |
|                      | TrapX           | ✔️                | ✔️              |                | ✔️              |                |

#### OS Firewall

{{site.Brand}} enables local network communication by default and OS level firewall protection should be enabled to restrict open ports only to those that are approved. For example, a database server should only allow database traffic from known application servers; any other attempts to connect to a database management port should be ignored and reported.

##### Supported Implementations

|      Application                           |      Vendor     |      x86         |                |      Power     |                |                |
|--------------------------------------------|-----------------|------------------|----------------|----------------|----------------|----------------|
|                                            |                 |      Windows     |      Linux     |      AIX       |      IBM i     |      Linux     |
| Microsoft Defender for Endpoint - Firewall | Microsoft       | ✔️                | *              |                |                |                |
| iptables                                   |                 |                  | ✔️              |                |                | ✔️              |
| ipfilters                                  | IBM             |                  |                | ✔️              |                |                |

#### Example High Level Design

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/security/virtualmachinesmedia/media/image2.png" width="600">

### Next steps

**Main Overview**
> [{{site.Brand}} Well-Architected Framework]({{ site.navigation.Home }})

**Operational Excellence**
> [{{site.Brand}} Operational Excellence Pillar]({{ site.navigation.Operations }})
> * [Power Discovery]({{ site.navigation.Operations }}Discovery/)

**Resiliency**
> [{{site.Brand}} Resiliency Pillar]({{ site.navigation.Resiliency }})

**Security**
> [{{site.Brand}} Security Pillar]({{ site.navigation.Security }})
> * [Key Security Areas]({{ site.navigation.Security }}key-security-areas)
> * [Security Management]({{ site.navigation.Security }}security-management)
> * [Edge Networking]({{ site.navigation.Security }}edge-networking)
> * [Internal Networking]({{ site.navigation.Security }}internal-networking)
> * [Security as a Service]({{ site.navigation.Security }}security-as-a-service)
