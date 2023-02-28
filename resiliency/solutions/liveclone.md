---
title: Skytap Live Clone
author: Dan Jones, Vice President of Product
permalink: /resiliency/solutions/live-clone/
---

## Skytap Live Clone

<span style="font-weight: 400;">Within Skytap, Environments and Templates are very important concepts. An Environment is an object that comprises one or more VMs, one or more networks, plus configuration settings and metadata. A Template is a read-only copy of an environment that can be used to back up critical environments and acts like a blueprint for new environments. Environments can be copied, and they can be saved as a template. </span>

<span style="font-weight: 400;">Until recently, if an environment contained running VMs it couldn’t be copied or saved as a template until those VMs were shutdown or suspended (x86 only). </span>

<span style="font-weight: 400;">With Skytap's new live clone capability, environments can be copied or saved while VMs remain running. The VMs in the copy or template will be powered off, but the original VMs will continue to run without interruption. </span>

<span style="font-weight: 400;">The new Skytap live clone feature is designed for customers running applications that need to be backed-up or archived, but cannot afford the time it takes to shut down the VMs, create the copy or save as a template, run them, and bring the application back online. With the new Skytap live clone feature, the copy/save happens nearly instantaneously and you eliminate the time it takes to shutdown and start-up the VMs. </span>

[![Skytap Live Clone](https://res.cloudinary.com/marcomontalbano/image/upload/v1639616907/video_to_markdown/images/vimeo--641622590-c05b58ac6eb4c4700831b2b3070cd403.jpg)](https://vimeo.com/641622590 "Skytap Live Clone")


### How it works
There are some considerations to be aware of when using Skytap live clone. The storage for each VM is being cloned by the Skytap storage platform and has no knowledge or concern for what is running within the VM. To ensure the copy of the VMs is consistent and not corrupted, we strongly recommend you quiesce VM activity. The safest level of quiesce is to shutdown the VM, but if you need to leave VMs running we recommend putting the VM into a consistent state. This includes actions like placing databases into read-only or back-up mode and freezing data volumes to ensure all transactions are written to disk before the copy is performed. You can read more about quiescing VM activity in this <a href="https://help.skytap.com/quiescing-vm-activity.html" target="_blank">help topic</a>.

<span style="font-weight: 400;">With a bit of scripting and ingenuity, these operations can be automated. The way to approach this is to designate a VM within the environment as the controller. A script running on this VM remotely connects to each of the other VMs in the environment and quiesces the VM as appropriate. The script then calls the Skytap API to copy or save the environment. Once the copy or save operation is complete, the script again remotely connects to each VM and returns it to normal operations. This entire operation should take just a few minutes. The application will be unavailable during this brief period, but you end up with an exact copy of your entire application and networking configuration, so the brief downtime is well worth it.</span>

<span style="font-weight: 400;">What is particularly exciting about this new feature is you get an instant copy or read-only template of the entire application with one action: all VMs, networks, and other configuration information. You can copy the template to another Skytap region for disaster recovery. You could run the copy of the environment to perform backups, troubleshooting issues, or use it as a target for read-only reporting.</span>

Watch the demo video above to see live clone in action.

### Next steps


**Main Overview**
> [Skytap Well-Architected Framework]({{ site.navigation.Home }})

**Operational Excellence**
> [Skytap Operational Excellence Pillar]({{ site.navigation.Operations }})

**Resiliency**
> [Skytap Resiliency Pillar]({{ site.navigation.Resiliency }})
> * [Migration]({{ site.navigation.Resiliency }}migrations)
> * [Protection]({{ site.navigation.Resiliency }}backups)
> * [Disaster Recovery]({{ site.navigation.Resiliency }}disaster-recovery)
> * [High Availability]({{ site.navigation.Resiliency }}high-availability)
>
> **Migration Solutions**
> * [Hot Migrations (Replication Sync)]({{ site.navigation.Resiliency }}solutions/hot-migrations)
> * [Cold (Warm) Migrations (Backup and Restore)]({{ site.navigation.Resiliency }}solutions/cold-migrations)
>
> **Design**
> * [Design Considerations for Azure]({{ site.navigation.Resiliency }}design-considerations-azure)
> * [Design Considerations for IBM Cloud]({{ site.navigation.Resiliency }}design-considerations-ibm)

**Security**
> [Skytap Security Pillar]({{ site.navigation.Security }})
