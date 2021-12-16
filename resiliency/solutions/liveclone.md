---
title: Skytap Live Clone
author: Dan Jones, Vice President of Product
---

**Skytap Live Clone** 

<span style="font-weight: 400;">Within Skytap, Environments and Templates are very important concepts. An Environment is an object that comprises one or more VMs, one or more networks, plus configuration settings and metadata. A Template is a read-only copy of an environment that can be used to back up critical environments and acts like a blueprint for new environments. Environments can be copied, and they can be saved as a template. </span>

<span style="font-weight: 400;">Until recently, if an environment contained running VMs it couldn’t be copied or saved as a template until those VMs were shutdown or suspended (x86 only). </span>

<span style="font-weight: 400;">With Skytap's new live clone capability, environments can be copied or saved while VMs remain running. The VMs in the copy or template will be powered off, but the original VMs will continue to run without interruption. </span>

<span style="font-weight: 400;">The new Skytap live clone feature is designed for customers running applications that need to be backed-up or archived, but cannot afford the time it takes to shut down the VMs, create the copy or save as a template, run them, and bring the application back online. With the new Skytap live clone feature, the copy/save happens nearly instantaneously and you eliminate the time it takes to shutdown and start-up the VMs. </span>

<div style="padding:56.25% 0 0 0;position:relative;"><iframe src="https://player.vimeo.com/video/641622590?h=64242ce57a&amp;badge=0&amp;autopause=0&amp;player_id=0&amp;app_id=58479" frameborder="0" allow="autoplay; fullscreen; picture-in-picture" allowfullscreen style="position:absolute;top:0;left:0;width:100%;height:100%;" title="Introducing Skytap Live Clone: Copy and save environments while VMs are running"></iframe></div><script src="https://player.vimeo.com/api/player.js"></script>


<h2><b>How it works</b></h2>
<span style="font-weight: 400;">There are some considerations to be aware of when using Skytap live clone. The storage for each VM is being cloned by the Skytap storage platform and has no knowledge or concern for what is running within the VM. To ensure the copy of the VMs is consistent and not corrupted, we strongly recommend you quiesce VM activity. The safest level of quiesce is to shutdown the VM, but if you need to leave VMs running we recommend putting the VM into a consistent state. This includes actions like placing databases into read-only or back-up mode and freezing data volumes to ensure all transactions are written to disk before the copy is performed. You can read more about quiescing VM activity in this </span><a href="https://help.skytap.com/quiescing-vm-activity.html"><span style="font-weight: 400;">help topic</span></a><span style="font-weight: 400;">.</span>

<span style="font-weight: 400;">With a bit of scripting and ingenuity, these operations can be automated. The way to approach this is to designate a VM within the environment as the controller. A script running on this VM remotely connects to each of the other VMs in the environment and quiesces the VM as appropriate. The script then calls the Skytap API to copy or save the environment. Once the copy or save operation is complete, the script again remotely connects to each VM and returns it to normal operations. This entire operation should take just a few minutes. The application will be unavailable during this brief period, but you end up with an exact copy of your entire application and networking configuration, so the brief downtime is well worth it.</span>

<span style="font-weight: 400;">What is particularly exciting about this new feature is you get an instant copy or read-only template of the entire application with one action: all VMs, networks, and other configuration information. You can copy the template to another Skytap region for disaster recovery. You could run the copy of the environment to perform backups, troubleshooting issues, or use it as a target for read-only reporting.</span>

Watch the demo video above to see live clone in action.