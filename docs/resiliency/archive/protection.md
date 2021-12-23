**Skytap on Azure Protection, High Availability and Disaster Recovery**

The following section provides an overview of Skytap on Azure
protection, disaster recovery (DR) and high availability (HA).

**Skytap on Azure Protection**

There are two key terms to consider when discussing Skytap on Azure HA
and DR:

**Recovery Time Objective (RTO):** The amount of downtime for services,
apps and/or infrastructure that an organization can tolerate to meet
business continuity needs.

**Recovery Point Objective (RPO):** The frequency of the data backup.
This factors in the amount of time in which data may be lost.

The RTO and RPO combined with contingencies like distance, amount of
data rate change and available bandwidth will determine whether to
employ an HA or DR tool.

High level considerations for availability on Skytap on Azure:

![Graphical user interface Description automatically
generated](I:\Repos\Skytap\WAF\resiliency\protectionmedia/media/image1.png){width="6.25255249343832in"
height="3.0714293525809273in"}

**\
**

**DR Data Protection and Warm/Cold DR for IBM Power using Skytap on
Azure**

Skytap partners with Commvault, an organization specializing in
automated backup and recovery of workloads both on-premises and in the
cloud for DR. Commvault has extensive and rich technical partnerships
with Microsoft and Skytap on Azure. It is deeply integrated in Azure and
has been vetted in Skytap for x86-64 Linux, Windows, AIX and IBM i. Any
workload that Skytap supports is supported by Commvault Backup and
Protection (includes AIX, IBM I, Linux on Power, Oracle, DB2, etc.).
Additional Commvault details include:

![Graphical user interface, text, application, Teams Description
automatically
generated](I:\Repos\Skytap\WAF\resiliency\protectionmedia/media/image2.png){width="6.5in"
height="2.084722222222222in"}

**Skytap on Azure + Commvault (AIX and IBM I as400) Architecture\
**

![Diagram Description automatically
generated](I:\Repos\Skytap\WAF\resiliency\protectionmedia/media/image3.png){width="6.5in"
height="2.941666666666667in"}

The following image details a more comprehensive view of the IBM i
Hybrid DR architecture:

![Graphical user interface Description automatically
generated](I:\Repos\Skytap\WAF\resiliency\protectionmedia/media/image4.png){width="6.5in"
height="3.6in"}

The following image details a more comprehensive view of the AIX Hybrid
DR architecture:

![Graphical user interface, application Description automatically
generated](I:\Repos\Skytap\WAF\resiliency\protectionmedia/media/image5.png){width="6.5in"
height="4.10625in"}

**DR Planning**

You always want to start with a full restore or one-touch migration as
your starting point. From there, you can establish a regular cadence of
backups tuned based on your RPO and how long it takes to restore once
you have those incremental backups taking place. The, in the event of a
disaster, it is a matter of taking the last incremental backup that was
taken and making that your restore point. This helps keep your RTO low
because you are only applying those incrementals. Keep in mind that
Commvault is handling the deduplication, so you are not eating up a full
storage footprint with each backup. This is very efficient from a cost
standpoint.

![Timeline Description automatically generated with medium
confidence](I:\Repos\Skytap\WAF\resiliency\protectionmedia/media/image6.png){width="6.5in"
height="2.426388888888889in"}

**Pure Backup and Recovery with Commvault**

In addition to using Commvault for DR, the same methodology can be used
to orchestrate and recover spot backups. For IBM i, discrete Subclient
libraries may be recovered on an as-needed basis. For AIX, volume
groups, or application specific recoveries may be performed as needed.
In each and every case, the architecture will rely on Media Agent Proxy
and Azure Object Storage in region.

**\
**

**HA for IBM Power with Skytap on Azure**

For HA, Skytap partners with Precisely, an organization specializing in
live replication for IBM i and AIX. Precisely's product offering Assure
MIMIX is the leader in IBM i HA and DR. It offers full-featured,
scalable real-time replication with extensive options for automating
administration, comprehensive monitoring and alerting, customizable
switch automation, and an easy graphical interface. Assure MIMIX works
across any combination of IBM i server, storage and OS versions. The
following details how Assure MIMIX HA works for IBM i:

![Diagram Description automatically generated with low
confidence](I:\Repos\Skytap\WAF\resiliency\protectionmedia/media/image7.png){width="6.5in"
height="2.6819444444444445in"}

The following is an overview of Assure MIMIX for IBM i:

![Diagram Description automatically
generated](I:\Repos\Skytap\WAF\resiliency\protectionmedia/media/image8.png){width="6.5in"
height="2.8722222222222222in"}

The following outlines a live migration utilizing Assure MIMIX for IBM
i:

![Timeline Description automatically
generated](I:\Repos\Skytap\WAF\resiliency\protectionmedia/media/image9.png){width="6.5in"
height="2.9166666666666665in"}

**Precisely Assure MIMIX for AIX\
**

The Precisely Assure MIMIX product offering for AIX focuses on Volume
Group replication and sync. Any replications encapsulated in the Volume
Group are included as part of the replication. The Root Volume Group is
not replicated and needs to be handled separately. Here is an Assure
MIMIX for AIX overview:

![Diagram Description automatically
generated](I:\Repos\Skytap\WAF\resiliency\protectionmedia/media/image10.png){width="6.5in"
height="2.807638888888889in"}

Here is an outline of live migration using Assure MIMIX for AIX:

![Timeline Description automatically
generated](I:\Repos\Skytap\WAF\resiliency\protectionmedia/media/image11.png){width="6.5in"
height="2.923611111111111in"}
