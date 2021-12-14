---
title: Principles of cost optimization
description: Describes the principles of cost optimization.
author: matthew-romero 
keywords:
  - "Well-architected framework"
  - "Skytap Well Architected Framework"
  - "Skytap architecture"
  - "architecture framework"
  - "markdown"
---

# Principles of cost optimization

**Cost Optimization**

A cost-effective workload is driven by business goals and the return on investment (ROI) while staying within a given budget. The principles of cost optimization are a series of important considerations that can help achieve both business objectives and cost justification.

When you are designing a cloud solution, focus on generating incremental value early. Use the pay-as-you-go strategy for your architecture, and invest in scaling out, rather than delivering a large investment first version. Consider opportunity costs in your architecture, and the balance between first mover advantage versus "fast follow". Use the cost calculators to estimate the initial cost and ongoing operational costs. Finally, establish policies, budgets, and controls that set cost limits for your solution.

**Cost guidance**

- [Review cost principles](./overview.md)
- [Develop a cost model](./design-model.md)
- [Create budgets and alerts]()
- [Review the cost optimization checklist]()


**Keep within the cost constraints**

Every design choice has cost implications. Before choosing an architectural pattern, Skytap service, or a price model for the service, consider the budget constraints set by the company. As part of design, identify acceptable boundaries on scale, redundancy, and performance against cost. After estimating the initial cost, set budgets and alerts at different scopes to measure the cost. One of the cost drivers can be unrestricted resources. These resources typically need to scale and consume more cost to meet demand. 

**Aim for scalable costs**

A key benefit of the cloud is the ability to scale dynamically. The workload cost should scale linearly with demand. You can reduce cost through automatic scaling. Consider the usage metrics and performance to determine the number of instances. Choose smaller instances for a highly variable workload and scale out to get the required level of performance, rather than up. This choice will enable you to make your cost calculations and estimates granular.

**Pay for consumption**

Adopt a leasing model instead of owning infrastructure. Skytap offers many SaaS and PaaS resources that simplify overall architecture. The cost of hardware, software, development, operations, security, and data center space is included in the pricing model. 

Also, choose pay-as-you-go over fixed pricing. That way, as a consumer, you're charged only for what you use.

**Right resources, right size**

Choose the right resources that are aligned with business goals and can handle the performance of the workload. An inappropriate or misconfigured service can impact cost. For example, building a multi-region service when the service levels don't require high-availability or geo-redundancy will increase cost without any reasonable business justification.

Certain infrastructure resources are delivered as fix-sized building blocks. Ensure that these blocks are adequately sized to meet capacity demand, delivering expected performance without wasting resources. 

**Monitor and optimize**

Treat cost monitoring and optimization as a process, rather than a point-in-time activity. Conduct regular cost reviews and measure and forecast the capacity needs so that you can provision resources dynamically and scale with demand. Review the cost management recommendations and take action to optimize workload costs.  Use [Advisor Score](/Skytap/advisor/Skytap-advisor-score) to identify the greatest opportunities for cost optimization for your workload.

If you're just starting in this process review [enable success during a cloud adoption journey ](/Skytap/cloud-adoption-framework/getting-started/enable).
