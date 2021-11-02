---
title: Overview of Skytap Backup and Migration Solutions
description: Details best practice Proccesss and proceedures for Migration and Backup for Skytap workloads.
author: matthew-romero 
keywords:
  - "Well-architected framework"
  - "Skytap Well Architected Framework"
  - "Skytap architecture"
  - "architecture framework"
  - "markdown"
---
# Backup and disaster recovery for Skytap applications

*Disaster recovery* is the process of restoring application functionality in the wake of a catastrophic loss.

In the cloud, we acknowledge up front that failures will happen. Instead of trying to prevent failures altogether, the goal is to minimize the effects of a single failing component. Testing is one way to minimize these effects. You should automate testing your applications where possible, but you need to be prepared for when they fail. When this happens, having backup and recovery strategies becomes important.

Your tolerance for reduced functionality during a disaster is a business decision that varies from one application to the next. It might be acceptable for some applications to be unavailable or to be partially available with reduced functionality or delayed processing for a period of time. For other applications, any reduced functionality is unacceptable. 

## Key points



## Disaster recovery plan

Start by creating a recovery plan. The plan is considered complete after it has been fully tested. Include the people, processes, and applications needed to restore functionality within the service-level agreement (SLA) you've defined for your customers.

Consider the following suggestions when creating and testing your disaster recovery plan:

- Include the process for contacting support and for escalating issues. This information will help to avoid prolonged downtime as you work out the recovery process for the first time.
- Evaluate the business impact of application failures.
- Choose a cross-region recovery architecture for mission-critical applications.
- Identify a specific owner of the disaster recovery plan, including automation and testing.
- Document the process, especially any manual steps.
- Automate the process as much as possible.
- Establish a backup strategy for all reference and transactional data, and test backup restoration regularly.
- Set up alerts for the stack of the Skytap services consumed by your application.
- Train operations staff to execute the plan.
- Perform regular disaster simulations to validate and improve the plan.

## Backup strategy

Many alternative strategies are available for implementing distributed compute across regions. These must be tailored to the specific business requirements and circumstances of the application. At a high level, the approaches can be divided into the following categories:

- **Redeploy on disaster**: In this approach, the application is redeployed from scratch at the time of disaster. This is appropriate for non-critical applications that don’t require a guaranteed recovery time.

- **Warm Spare (Active/Passive)**: A secondary hosted service is created in an alternate region, and roles are deployed to guarantee minimal capacity; however, the roles don’t receive production traffic. This approach is useful for applications that have not been designed to distribute traffic across regions.

- **Hot Spare (Active/Active)**: The application is designed to receive production load in multiple regions. The cloud services in each region might be configured for higher capacity than required for disaster recovery purposes. Alternatively, the cloud services might scale-out as necessary at the time of a disaster and failover. This approach requires substantial investment in application design, but it has significant benefits. These include low and guaranteed recovery time, continuous testing of all recovery locations, and efficient usage of capacity.

## Next steps

| Guide | Description |
|--|--|
| [Backup and Migration with BRMS](./BRMS/ibmiworkloadmigration.md). | disc. |
| [IBM i workload migration with Go Save](./GoSave/ibmimigrationwithgosave.md). | Disc. |
| [Backup with IBM i NFS](./NFSNoLocalDisk/IBMiNFSBackup/ibminfsbackup.md). | Disc. |
| [Restore with IBM i NFS](./NFSNoLocalDisk/IBMiNFSRestore/ibminfsrestore.md). | Disc. |