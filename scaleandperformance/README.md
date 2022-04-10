
Scalability is the ability of a system to handle increased load and Performance efficiency is the ability of your workload to scale to meet the demands placed on it by users in an efficient manner. 

Before the cloud became popular, when it came to planning how a system would handle increases in load, many organizations intentionally provisioned oversized workloads to meet business requirements. This decision made sense in on-premises environments because it ensured capacity during peak usage. Capacity reflects resource availability (CPU and memory). Capacity was a major consideration for processes that would be in place for many years.

Just as you need to anticipate increases in load in on-premises environments, you need to expect increases in cloud environments to meet business requirements. One difference is that you may no longer need to make long-term predictions for expected changes to ensure you'll have enough capacity in the future. Another difference is in the approach used to manage performance.

To achieve performance efficiency, consider how your workload design scales and implement PaaS offerings that have built-in scaling operations.

These principles are intended to guide you in your overall strategy for improving performance efficiency.

Understand the challenges of distributed architectures

Most cloud deployments are based on distributed architectures where components are distributed across various services. Troubleshooting monolithic applications often requires only one or two lenses—the application and the database. With distributed architectures, troubleshooting is complex and challenging because of various factors. For example, capturing telemetry throughout the application—across all services—as possible. Also, the team should be skilled with the necessary expertise to troubleshoot all services in your architecture.

Run performance testing in the scope of development

Any development effort must go through continuous performance testing. The tests make sure that any change to the codebase doesn't negatively affect the application's performance. Establish a regular cadence for running the tests. Run the test as part of a scheduled event or part of a continuous integration (CI) build pipeline.

Establish performance baselines—Determine the current efficiency of the application and its supporting infrastructure. Measuring performance against baselines can provide strategies for improvements and determine if the application is meeting the business goals.

Run load and stress tests—Load testing measures your application's performance under predetermined amounts of load. Stress testing measures the maximum load your application and its infrastructure can support before it buckles.

Identify bottlenecks—Bottlenecks is an area within your application that can hinder performance. These spots can be the result of shortages in code or misconfiguration of a service. Typically, a bottleneck worsens as load increases.

Continuously monitor the application and the supporting infrastructure

Have a data-driven approach—Base your decisions on the data captured from repeatable processes. Archive data to monitor performance changes over time, not just compared to the last measurement taken.
Monitor the health of current workloads—In monitoring strategy, consider scalability and resiliency of the infrastructure, application, and dependent services. For scalability, look at the metrics that would allow you to provision resources dynamically and scale with demand. For reliability, look for early warning signs that might require proactive intervention.
Troubleshoot performance issues—Issues in performance can arise from database queries, connectivity between services, under-provisioned resources, or memory leaks in code. Application telemetry and profiling can be useful tools for troubleshooting your application.
Identify improvement opportunities with resolution planning

Understand the scope of your planned resolution and communicate the changes to all necessary stakeholders. Make code enhancements through a new build. Enhancements to infrastructure may involve many teams. This involved effort may require updated configurations and deprecations in favor of more-appropriate solutions.

Invest in capacity planning

Plan for fluctuation in expected load that can occur because of world events. Test variations of load before the events, including unexpected ones, to ensure that your application can scale. Make sure all regions can adequately scale to support total load, if a region fails. Take into consideration:

Technology and the SKU service limits.
SLAs when determining the services to use in the design. Also, the SLAs of those services.
Cost analysis to determine how much improvement will be realized in the application if costs are increased. Evaluate if the cost is worth the investment.





Two main ways an application can scale include vertical scaling and horizontal scaling. Vertical scaling (scaling up) increases the capacity of a resource, for example, by using a larger virtual machine (VM) size. Horizontal scaling (scaling out) adds new instances of a resource, such as VMs or database replicas.

Horizontal scaling has significant advantages over vertical scaling, such as:

True cloud scale: workloads that are designed to run on multiple LPARs, reaching scales that aren't possible on a single node.
Horizontal scale is elastic: You can add more instances if load increases, or remove instances during quieter periods.
Scaling out can be triggered automatically, either on a schedule or in response to changes in load.
Scaling out may be cheaper than scaling up. Running several small VMs can cost less than a single large VM.
Horizontal scaling can also improve resiliency, by adding redundancy. If an instance goes down, the application keeps running.
An advantage of vertical scaling is that you can do it without making any changes to the application. But at some point, you'll hit a limit, where you can't scale up anymore. At that point, any further scaling must be horizontal.

Horizontal scale must be designed into the system. For example, you can scale out VMs by placing them behind a load balancer. But each VM in the pool must handle any client request, so the application must be stateless or store state externally (say, in a distributed cache). Managed PaaS services often have horizontal scaling and autoscaling built in. The ease of scaling these services is a major advantage of using PaaS services.

Just adding more instances doesn't mean an application will scale, however. It might push the bottleneck somewhere else. For example, if you scale a web front end to handle more client requests, that might trigger lock contentions in the database. You'd want to consider other measures, such as optimistic concurrency or data partitioning, to enable more throughput to the database.

Always conduct performance and load testing to find these potential bottlenecks. The stateful parts of a system, such as databases, are the most common cause of bottlenecks, and require careful design to scale horizontally. Resolving one bottleneck may reveal other bottlenecks elsewhere.

Plan for growth
Planning for growth starts with understanding your current workloads, which can help you anticipate scale needs based on predictive usage scenarios. An example of a predictive usage scenario is an e-commerce site that recognizes that its infrastructure should scale appropriately for an expected high volume of holiday traffic.

