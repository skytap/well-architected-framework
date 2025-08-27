---
title: Security
description: Considerations to ensure security.
author: John Bradshaw, Principal Architect
permalink: /security/
---
 
# Security on the {{site.Brand}} Platform

**Implementing comprehensive security controls in the cloud**

Securely running workloads in a cloud environment requires a multi-layered approach which covers both how workloads are deployed and maintained within your account and the management of your {{site.Brand}} account itself. This document outlines architectural elements necessary to achieve a robust implementation on the {{site.Brand}} platform and best practices for how your {{site.Brand}} account should be configured.

![control map](https://skytap.github.io/well-architected-framework/security/media/image2.png)

_Figure 1 - Control Map_

Assets you deploy into your account should be defined by implementation standards based on your use cases and requirements while taking into account best practices of how to leverage {{site.Brand}}. Each layer in this model is built on the capabilities delivered in the layer below; you cannot know how to secure virtual machines without understanding how they can be secured and why they need to be.

Accompanying this document is a **{{site.Brand}} - Security Controls Workbook** that should be referred to as you develop your High-Level Designs and Low-Level Designs. It outlines each area discussed herein with specific controls that should be applied. It also contains an example of a Risks and Mitigations register that should be considered as part of any cloud project.

Similar to other cloud services, {{site.Brand}} operates a shared responsibility model with regards to security. {{site.Brand}} is accountable for the platform and customers are accountable for the way they use that platform.

![Sytap Secuurity Control Map](https://skytap.github.io/well-architected-framework/security/media/image3.png)

_Figure 2 -- Shared Responsibility Model_

Additionally, resources deployed into {{site.Brand}} should have a clearly defined operating model:

* Who is responsible for creation/deletion of virtual machines?
* Who is responsible for updating the application/data on your virtual machines?
* Who can provide common services support on the virtual machines?
* Are there services external to {{site.Brand}} that need to be supported for your workloads?
* Which of your existing operating models need to change for new cloud workloads?

The approach to the cloud must be integrated within your organisation's Target Operating Model; this allows for the appropriate security controls to be accounted for, architected and implemented.

![Example Target Operating Model (TOM)](https://skytap.github.io/well-architected-framework/security/media/image3.png)

<!--(https://www.lucidchart.com/documents/edit/f5794a4f-e45d-4a70-9e12-9f0abf4579bb/0?callback=close&name=docs&callback_type=back&v=1701&s=612)-->

_Figure 3 - Example Target Operating Model (TOM)_

Most organizations will have multiple cloud vendors, supported by systems integrators or outsourced management functions. Your various clouds need to be managed holistically but controls applied specifically, for example, all logging across your organization should be consolidated into a single Security Incident Event Monitoring (SIEM) system, but the approach to management and remediation will be custom to each workload and each platform.
