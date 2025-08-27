---
title: Skytap Operational Excellence - Connectivity - Getting Started with Azure Networking
author: Colleen E Hamilton - Senior Product Manager, Jeffry Lane - Sr. Cloud Solutions Architect, Mike Neil - VP, Solutions and Performance Engineering
permalink: /operations/connectivity/azure/
---

# Getting Started with Azure Networking

Even though {{site.SoA}} is running within an Azure data center, Azure treats {{site.Brand}} as if it were a separate on-premises location, for network isolation purposes. To make networking more straightforward for our customers, {{site.Brand}} and Microsoft Azure have partnered in the design of several common network patterns, which you can put to work in your own applications. Whether you need to connect your {{site.Brand}} Environment to your on-premises applications, or your Azure Virtual Network (vNet), or both, there is a network topology to address your needs.

## Table of Contents <a name="toc"></a>

* [{{site.SoA}} Network Connectivity Considerations](#begin)
* [TCP/IP Performance Tuning for {{site.SoA}} IBM Power Logical Partitions (LPARs)](#tuning)
* [Option 01: Direct VPN into {{site.Brand}}](#option1)
  * [Setting up a Site-to-Site VPN between On-Premises and {{site.SoA}}](#site2site)
* [Option 02: VPN into Azure Hub](#option2)
  * [Setting up a Site-to-Site VPN between {{site.Brand}} and your Azure vNet Gateway](#site2sitevnetgateway)
* [Option 03: Global Reach Enabled ExpressRoute](#option3)
  * [Setting up Global Reach Enabled ExpressRoute to {{site.Brand}}](#expressroutewithglobalreach)
* [Appendix](#appendix)
  * [{{site.Brand}} UI ports](#UIPorts)
  * [Global Reach Enabled ExpressRoute Guide](#expressroutewithglobalreachguide)
  * [Connect your {{site.Brand}} application just to On-Premises, using an existing ExpressRoute to On-Premises](#expressrouteonprem)
  * [Connect your {{site.Brand}} application just to an Azure Virtual Network (vNet) through an ExpressRoute](#expressroute2vnet)
  * [Connect your {{site.Brand}} application to *both* On-Premises and an Azure vNet](#expressroute2vnetandonprem)
  * [Connect your {{site.Brand}} application over an existing On-Premises ExpressRoute circuit, when the connection between {{site.Brand}} and Azure is a VPN](#expressroute2vnetandonprem+vpn)
  * [Connect your {{site.Brand}} application to *both* On-Premises and an Azure vNet, when you need transitive routing](#expressroute2vnetandonpremtransroute)
* [Next steps](#next-steps)

###### *[Back to the Top](#toc)*

## {{site.SoA}} Network Connectivity Considerations <a name="begin"></a>

* **Regions**: <a href="https://help.skytap.com/understanding-regions.html#understanding-regions" target="_blank">{{site.Brand}} Global Regions</a>

**NOTE**: ***Create a LOCK on the Azure resource group** that
    contains the {{site.SoA}} service. This lock may help prevent
    someone from accidentally deleting your {{site.SoA}} subscription
    that resides in an Azure resource group.*

* **Satellite Centers Connectivity**:

There are two methods to connect satellite centers into {{site.Brand}}
        on Azure via VPN connection:

   * **Method 1:** VPN between satellite facilities **terminating** **directly** into {{site.SoA}}

   * **Method 2**: VPN connection between satellite facilities and Azure VPN Gateways in your Azure hub leveraging an Azure Virtual WAN hub or an Azure Route Server

**Best practice:** To determine latency between your connectivity into {{site.SoA}} (i.e., your DC, Distribution Warehouse), we recommend conducting a <a href="http://speedtest.skytap.com/" target="_blank">{{site.Brand}} Speed Test</a>.

###### *[Back to the Top](#toc)*

## TCP/IP Performance Tuning for {{site.SoA}} IBM Power Logical Partitions (LPARs) <a name="tuning"></a>

This article discusses common best practices for tuning IBM Power IBM i and AIX workloads for {{site.SoA}} (SOA). One of the biggest challenges organizations have when migrating and running Power workloads in SOA is tuning the TCP/IP stack for both the local (within a SOA) environment and externally either between on-premises and SOA, or between SOA deployments in different SOA regions. This article makes several references to general Azure tuning techniques as well as those from IBM for AIX and IBM i.

 


### Executive Summary

An efficient network is key to rapid cloud migration and ongoing application performance for any cloud deployment. There are several factors that impact network performance, two of those being latency and fragmentation. 

 

To establish a baseline for network performance based on several tuning parameters for both AIX and IBM i, {{site.Brand}} tested the following scenarios:

 



* Two LPARs on the same Power Hosting Node (PHN)
* Two LPARs within the same {{site.Brand}} environment and subnet
* Two LPARs, one in SOA’s Singapore region to a second in SOA’s Hong Kong region using VPN to connect both regions
* Two LPARs, one in SOA Singapore region to a second in SOA’s Hong Kong region using Azure ExpressRoute provisioned at 1Gb/sec

 

Each of these scenarios were done for AIX and IBM i. For AIX, {{site.Brand}} tested using SCP as a file transfer protocol in addition to IPERF3. For IBM i, {{site.Brand}} used FTP only.

 

{{site.Brand}} testing found:

 



* LPARs of both Operating Systems performed better with TCP Send Offload enabled.
* Network performance between the two systems was best when source and target machines matched for both MTU and Send/Receive buffer settings.
* A Maximum Transmission Unit (MTU) of over 1500 for WAN connections was problematic. {{site.Brand}} recommends using an MTU between 1250 and 1330 for ExpressRoute and VPN connections.
* When using a VPN, set an MSS Clamp no lower than 1200 and no higher than 1254.
* When transferring large amounts of data, look at using applications that can support multiple file transfer connections or transfer multiple files at once. This allows the client to maximize available bandwidth of a connection.

 

_Table 1: AIX Network Performance Recommendations for local traffic and between SOA Singapore and Hong Kong region_

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/media/PT_img1.png" width="800">

_Table 2: IBM i Network Performance Recommendations for local traffic and between SOA Singapore and Hong Kong regions_

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/media/PT_img2.png" width="800">

These settings were shown to have the best performance between LPARs of like Operating System in these scenarios but should only be considered a baseline. Performance will vary based on the configuration and settings within your {{site.Brand}} environment.

 


### The Role of MTU, Fragmentation, and Large Send Offload

The MTU is the largest size frame (packet) that can be sent over a network interface that is measured in bytes. The general default setting for most devices is 1500. However, for Power workloads this is commonly not the case as discussed later in this article.

 


### Fragmentation

Fragmentation occurs when a packet is larger than the device, LPAR, and/or virtual machine (VM) is set to handle. When a larger packet is received, it is either fragmented into one or more packets and resent, or dropped altogether. If fragmented, the device on the receiving end then must reassemble the original packet so it can be processed by the end system.

 

Fragmentation generally has a negative impact on performance. When a packet must be fragmented, it requires more CPU on the device or receiving system. The device must hold all the fragmented packets, reassemble them, and then send it to the next device. If it has an MTU size that doesn’t match, this process repeats until the traffic finally reaches the target system. The impact is increased latency due to devices having to reassemble the packets and the potential for packets to be received out of order by the device. In some cases, they can be dropped causing the packet to have to be resent.

 


### The Pros and Cons of Modifying the MTU

Generally, the larger the MTU, the more efficient your network and the faster you can move data between two systems. This is the first consideration as to network tuning with Power workloads as they often don’t default to the standard 1500 MTU. For AIX, it is common to see Jumbo Frames enabled which has an MTU of 9000. For IBM i, the default is at the opposite end of the spectrum at 576, which dates back to being optimized for dial up modems.

 

If the packet is set larger than devices in the path can handle, the packets will be fragmented, new header information will be added, and the packet is sent onto its destination. This all adds overhead. For low latency connections, it will prevent you from consuming maximum bandwidth. For WAN connections such as ExpressRoute and VPN over greater distances, it can have a much greater impact. This is especially apparent during the migration phase where you are working to move a lot of data in a short amount of time.

 

Conversely, if you set the MTU size too small, then the destination system must process more packets and that too hinders performance. The key is to find the right MTU that is optimized for the speed and latency of the connection you are using.


### {{site.Brand}} and LPAR MTU

{{site.Brand}} Power Hosting Nodes (PHNs) use VIOS. VIOS is the virtualization layer provided by PowerVM which controls all virtual input and output from a given LPAR. It virtualizes the physical hardware. This exists both for network traffic to local and WAN connections, in addition to traffic to {{site.Brand}}’s Software Defined Storage layer. Traffic that leaves the LPAR will be governed by VIOS and the Shared Ethernet Adapter (SEA) of the VIOS LPAR. {{site.Brand}} sets the MTU of the SEA to 1500. As a result, setting your LPAR’s MTU higher than 1500 will result in fragmentation. However, this changes if you have multiple LPARs on the same network that are on the same subnet and PHN. In this situation, traffic never leaves the PHN and VIOS essentially “steps out of the way” allowing the LPAR virtual adapters to communicate directly with one another. The result is very fast network throughput and the ability to use Jumbo Frames.

 


### Azure and Fragmentation

Azure’s Virtual Network stack will drop out of order fragments or fragmented packets that do not arrive in the order that they were supposed to. When this happens, the sender must resend those packets. Microsoft does this to protect itself against a vulnerability called FragmentSmack that was announced in November of 2018. This can have a substantial impact when you have high latency connections or connections where there is a lot of fragmentations because of an MTU that is set too high.

 


### {{site.Brand}} and Azure ExpressRoute


{{site.Brand}} and Azure ExpressRoute use an MTU size of 1438. Sending traffic between two SOA regions will result in an MSS of 1348. This includes 20 bytes of IP header and 20 bytes of TCP header, then Azure modifies this which adds another 50 bytes.

 


### SOA and VPN

SOA’s virtual networking stack includes native support for IPSec. This allows users to create VPN tunnels on the SOA side without having to add IPSec Gateways and/or virtual appliances. Like ExpressRoute, the MTU of IPSec connections should not be set higher than 1438 and an MSS of 1348.

 

Setting the correct MSS is critical in reducing fragmentation. To illustrate the point, here is an example of an IPERF test where {{site.Brand}} set the MTU within the MSS of 1254 vs. setting it one byte above the MSS as 1255, resulting in fragmentation.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/media/PT_img3.png" width="800">

 

The above example was taken between the Hong Kong and Singapore regions. The differences are dramatic - approximately a 10x delta. The lesson learned is to avoid fragmentation at all costs as file transfer speed is greatly reduced as the latency between source and destination increases.

 


### Large Send Offload

Large Send Offload (LSO) allows the LPAR in SOA to send large packets and frames through the network. When enabled, the Operating System creates a large TCP packet and sends it to the Ethernet Adapter for segmentation before sending it through the network. For LPARs that are on the same PHN with LSO enabled, the result is extremely high network throughput. SOA’s VIOS SEA has LSO enabled, so that using this setting can improve communication between LPARs on the same subnet within a {{site.Brand}} Environment. However, when the traffic is destined to another LPAR in the network, it must go through the SEA with an MTU of 1500, which will result in fragmentation.

 

For AIX with LSO enabled, you will see maximum throughput between two AIX LPARs on the same subnet at ~2Gb/sec due to the MTU size of 1500. With two LPARs on the same PHN, VIOS and the physical network layer is bypassed allowing far greater speeds at a maximum of about 2.6Gb/sec.<sup>[1]</sup>

 

For IBM i with LSO enabled, performance is going to be very protocol dependent. Using FTP as a test, maximum throughput between two IBM i systems is ~472Mb/sec with an MTU of 576 with two IBM i systems on the same subnet running on different PHNs. When running on the same PHN, performance increases slightly at ~482Mb/sec with an MTU size of 1330. It is important to note that you can expect higher throughput with multiple connections. Therefore, when transferring a lot of data from an IBM i system, you should use multiple FTP streams. For example, if you have three 5GB files to transfer, utilize three separate interactive sessions to increase your overall throughput.

 


### VPN and MTU

If you are using a VPN between {{site.Brand}} Environments in different SOA regions or between SOA and an external environment, you need to account for the increased overhead for the VPN encapsulation.

 

For AIX, testing was done between two SOA regions with ~34ms of latency using SCP between both systems. {{site.Brand}} recommends setting the MTU to 1200 with an MSS of 1250 with LSO enabled. In this configuration, {{site.Brand}} has seen transfer rates of ~366Mb/sec. When the physical network layer is bypassed, {{site.Brand}} has seen far greater transfer speeds at a maximum of about 2.6Gb/sec.

 

For IBM i, testing was done between two SOA regions with ~34ms of latency using FTP between both systems. {{site.Brand}} recommends setting the MTU to 1200 with an MSS of 1250 with LSO enabled. In this configuration, {{site.Brand}} has seen transfer rates of ~190Mb/sec.

 


### Azure ExpressRoute and MTU

 

If you are using an ExpressRoute between {{site.Brand}} Environments in different SOA regions or between SOA and an external environment, you need to account for two important factors:

 



1. Azure’s virtual networking stack will drop out of order packets. This often increases as latency increases. As a result, fragmentation needs to be managed carefully for optimal network performance.
2. ExpressRoute adds some IP overhead when used with SOA. The amount added is 50 bytes per frame/segment.

 

For AIX, testing was done between two SOA regions with ~34ms of latency using SCP between both systems. {{site.Brand}} recommends setting the MTU to 1200 with an MSS of 1250 with LSO enabled. In this configuration, {{site.Brand}} has seen transfer rates of ~366Mb/sec, and when the physical network layer is bypassed, it allows far greater speeds at a maximum of about 2.6Gb/sec.

 

For IBM i, testing was done between two SOA regions with ~34ms of latency using FTP between both systems. {{site.Brand}} recommends setting the MTU to 1200 with an MSS of 1250 with LSO enabled. In this configuration, {{site.Brand}} has seen transfer rates of ~190Mb/sec per any given connection.

 


### Latency and Round-Trip Time


Network latency is governed by the speed of light. As a result, the maximum network throughput for TCP/IP is going to be dependent on the round-trip time (RTT) between source and target devices. This table shows the distance between SOA regions. 

 

_Table 3: Example Latency and route-trip time_

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/media/PT_img4.png" width="800">


### Power OS Tunning Summary

Every deployment of AIX and IBM i that is on-premises and/or in SOA will have different variables from what was tested and described in this article. {{site.Brand}}’s tests were in a relatively controlled environment between two SOA regions with a known network path and devices. When communicating with systems in SOA regions to those located on-premises and other cloud providers, different devices and their configurations will have a significant impact on network performance. This needs to be accounted for early in your network design and solutions. It is always best to tune and optimize from a position of knowing vs. not knowing. As a result, {{site.Brand}} has provided a summary of the testing done and the tuning parameters that were used for each. A list of the parameters is provided below with a link to the appropriate documentation for you to learn more, and/or make the change to your system.

 


### AIX Network Tuning<sup>[2]</sup>

For AIX network tuning, IBM’s guide for[ AIX Network Adapter Performance](https://www.ibm.com/docs/en/aix/7.2?topic=tuning-adapter-performance-guidelines) is a great reference. Key tuning parameters that were used to tune {{site.Brand}} performance include:

 



* **LSO**[ AIX](https://www.ibm.com/docs/en/aix/7.2?topic=tuning-tcp-large-send-offload)/[IBM i](https://www.ibm.com/docs/en/i/7.4?topic=ssw_ibm_i_74/cl/chgtcpifc.htm): The TCP LSO option allows the AIX TCP layer to build a TCP message up to 64 KB long. The adapter sends the message in one call down the stack through IP and the Ethernet device driver. LSO is enabled by default on SOA’s PHN SEAs (Shared Ethernet Adapter of VIOS).
* **Maximum Transmission Unit** (MTU)[ AIX](https://www.ibm.com/docs/en/aix/7.2?topic=nfs-setting-mtu-sizes)/[IBM i](https://www.ibm.com/docs/en/i/7.4?topic=ssw_ibm_i_74/cl/chgtcpifc.htm): The maximum network packet size that can be transmitted or received by a network device. 
* **Remote Maximum Transmission Unit** (RMTU): The MTU size that is used for remote networks. AIX attempts to calculate which networks are remote by looking at the route table. IBM i allows you to set the MTU for each route.
* **Default TCP/IP Send Receive Buffer Size**[ AIX](https://techchannel.com/SMB/01/2014/tuning-aix-network-performance)/[IBM i](https://www.ibm.com/docs/en/i/7.1?topic=ssw_ibm_i_71/cl/chgtcpa.html#CHGTCPA.TCPSNDBUF): The tcp_recvspace option specifies how many bytes of data the receiving system can buffer in the kernel for receiving traffic, likewise the tcp_sendspace tunable specifies how much data the sending application can buffer when sending data. For these tests, {{site.Brand}} found that the default AIX Send/Receive Buffer size of 2097152 bytes yielded the best performance. For IBM i, there was some variation of values that performed well.
* **[Maximum Segment Size (MSS)](https://help.skytap.com/wan-troubleshooting-vpn.html#Poorperformance)**: The largest amount of data, measured in bytes, that a computer or communications device can handle in a single, unfragmented piece. For VPN testing between regions, an MSS of 1200 was used on both sides of the VPN tunnel.
* **[Latency](https://learn.microsoft.com/en-us/azure/networking/azure-network-latency)**: Inside of a {{site.Brand}} Environment yields near zero network latency. All remote tests were done between Hong Kong (CN-HongKong-M-1) and Singapore (SG-Singapore-M-1). Latency between these regions was between 33ms and 38ms.

_Table 4: AIX Network Performance with LSO Enabled using SCP_

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/media/PT_img5.png" width="800">

_Table 5: AIX Network Performance with LSO Disabled using SCP_

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/media/PT_img6.png" width="800">

_Table 6: AIX Network Performance with LSO Enabled using IPERF8 with eight connections_

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/media/PT_img7.png" width="800">

_Table 7: AIX Network Performance with LSO Disabled using IPERF8 with eight connections_

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/media/PT_img8.png" width="800">

_Table 8: IBM i Network Performance with LSO Enabled using FTP_

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/media/PT_img9.png" width="800">

_Table 9: IBM i Network Performance with LSO Disabled using FTP_

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/media/PT_img10.png" width="800">

 


### Summary

To maximize the network performance between systems in SOA, one must consider latency and fragmentation and work to reduce both variables. This is not only true when looking at source and destination systems within SOA regions, but also when moving data in and out of SOA externally. In {{site.Brand}}’s testing, it found these points critical to ensuring maximum network performance: 

 



* TCP Send Offload should be enabled for both AIX and IBM i workloads.
* Make sure that source and target machines match both MTU and Send/Receive buffer settings.
* Do not use an MTU larger than 1500. {{site.Brand}} recommends using an MTU between 1250 and 1330 for ExpressRoute and VPN connections.
* When using a VPN, look at setting an MSS Clamp no lower than 1200 and no higher than 1254.
* When transferring large amounts of data, look at using applications that can support multiple file transfer connections or transfer multiple files at once to maximize available bandwidth.
* It is important to remember each system and network can be different, so this needs to be accounted for in your tuning efforts.


<sup>1</sup> Tests were done using IPERF3 set to use up to eight connections.

<sup>2</sup> One of the best “unofficial” resources on AIX Tuning,[ Network Tuning in AIX by Jaqui Lynch](http://www.circle4.com/movies/networkperf/networkperf.pdf) it is a must read for anyone working on AIX Networking

###### *[Back to the Top](#toc)*

## Option 1: Direct VPN Connection into {{site.Brand}}<a name="option1"></a>

For context, this methodology will leverage a direct VPN into {{site.Brand}} on
Azure (bypassing the Azure hub network). While the Global Reach enabled
ExpressRoute (option 3 below) is the most robust option, consider this
direct VPN approach when the satellite facilities do not require access
to additional Azure resources.

Keep in mind that this option may have the highest latency; however, the
latency may be within the margin of successful operations. As is the
case with all options, {{site.Brand}} recommends conducting a {{site.Brand}} Speed Test
as mentioned above.

**Links to helpful resources:**

*<a href="https://help.skytap.com/wan-create-vpn.html#creating-a-vpn-connection-to-your-skytap-account" target="_blank">{{site.Brand}} VPN documentation</a> on how to make VPN connections into {{site.SoA}}:

**Reference Architecture - Direct VPN**

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/Azure/media/image1.png" width="800">

### Setting up a Site-to-Site VPN between On-Premises and {{site.SoA}}<a name="site2site"></a>

{{site.Brand}} built-in VPN service gives you a streamlined option to establish a VPN tunnel between your environment in {{site.Brand}}, and your on-premises deployment. The tunnel is encrypted and encapsulated, routed over the public internet.

<!-- <img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/media/image2.png" width="500"> -->

A VPN is like a bridge: both sides must be "facing each other" for traffic to flow. This means that each VPN endpoint must be configured the same way, with the same parameters. Before you begin, speak to your company's IT department to <a href="https://help.skytap.com/wan-vpn-configuration-parameters.html" target="_blank">find out which VPN parameters</a> you'll need to use for {{site.Brand}}\'s side of the VPN.

Also like a bridge, VPNs are most reliable when distance being spanned is short. This means the two endpoints should be as close together as possible. You'll want to set up the {{site.Brand}} VPN endpoint in a region which is nearest to your corporate VPN endpoint. Choose the region in your {{site.Brand}} account which is nearest to your corporate endpoint, and <a href="https://help.skytap.com/managing-public-ip-addresses.html#AddingastaticpublicIPaddresstoyouraccount" target="_blank">create the static public IP address</a> for your new VPN.

Now you're ready to create your VPN. Using the parameters you've agreed upon with your IT department, <a href="https://help.skytap.com/wan-create-vpn.html" target="_blank">create the VPN endpoint in {{site.Brand}}</a>, and work with your company\'s IT department to set up a matching-configuration endpoint on your On-Premises VPN device. Once both endpoints are set up, make sure that you specify at least one remote subnet on the {{site.Brand}} side (this is the on-prem network subnet that {{site.Brand}} will be sending to and receiving from), and then **be sure to <a href="https://help.skytap.com/wan-testing.html#Test_the_VPN" target="_blank">test your VPN</a>** to confirm that it will properly establish a tunnel.

Once you've confirmed that your VPN can successfully establish Phase1 and Phase2 connectivity, <a href="https://help.skytap.com/wan-connecting-environments-to-vpn-or-pnc.html#Connect" target="_blank">connect your VPN to its intended the {{site.Brand}} Environment(s)</a>, choose a server from each side of the WAN topology that need to talk to each other (one from {{site.Brand}} and one from your on-premises network), and <a href="https://help.skytap.com/wan-testing.html#Further" target="_blank">test the final end-to-end connectivity</a> between each server.

###### *[Back to the Top](#toc)*

## Option 2: VPN into Azure WAN Hub<a name="option2"></a>

For context, the VPN into Azure Hub option may provide a lower latency connection over Option 1 (direct VPN into {{site.Brand}}) and perhaps even Option 3 due to the specific routing of your current ExpressRoute.

In this case, you will establish VPN connections between satellite facilities and the closest Azure VPN point-of-presence (POP) which leverages the Azure backbone to hit your Azure hub region. Once there, you will either leverage an <a href="https://docs.microsoft.com/en-us/azure/virtual-wan/virtual-wan-global-transit-network-architecture" target="_blank">Azure VWAN HUB</a> or <a href="https://docs.microsoft.com/en-us/azure/route-server/overview" target="_blank">Azure Route Server</a>.

Links to helpful resources:

* Specific Azure doc relevant to this option: <a href="https://docs.microsoft.com/en-us/azure/route-server/expressroute-vpn-support#:~:text=Azure%20Route%20Server%20supports%20not%20only%20third-party%20network,peering%20between%20the%20gateway%20and%20Azure%20Route%20Server." target="_blank">About Azure Route Server supports for ExpressRoute and Azure VPN \| Microsoft Docs</a>

* VWAN Hub info <a href="https://docs.microsoft.com/en-us/azure/virtual-wan/virtual-wan-about" target="_blank">https://docs.microsoft.com/en-us/azure/virtual-wan/virtual-wan-about</a>

**Reference Architecture**

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/Azure/media/image2.png" width="800">

### Setting up a Site-to-Site VPN between {{site.Brand}} and your Azure vNet Gateway<a name="site2sitevnetgateway"></a>

Site-to-Site VPNs that connect into Azure virtual network (vNet) Gateways are just like site-to-site VPNs that connect to on-premises deployment: they send traffic that is both encrypted and encapsulated. But there's also something a little different: When {{site.Brand}} VPNs use Azure's own internet routing, this means that traffic sent from {{site.Brand}} to an Azure vNet via VPN will generally remain on the Azure backbone. This makes VPNs a relatively simple, lower cost, and behaviorally consistent option, especially for proofs-of-concept and Dev/Test applications.

Setting up a Site-to-Site VPN between {{site.Brand}} and Azure begins by sorting out which VPN parameters you'll need to use. When connecting a {{site.Brand}} VPN to an Azure vNet, the parameters you'll configure on {{site.Brand}} will be determined by the intersection of your IT department's requirements, <a href="https://help.skytap.com/wan-vpn-configuration-parameters.html" target="_blank">{{site.Brand}}-supported parameter set</a>, and <a href="https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-about-vpn-devices#ipsec" target="_blank">Azure's supported parameter set</a>.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/media/image3.png" width="500" align="center">

It's worth noting that {{site.Brand}} no longer supports Azure's default IKE Phase 1 DH (Diffie-Hellman) Group---Group 2 (1024 bit)---due to its weak security. You'll need to <a href="https://docs.microsoft.com/en-us/azure/vpn-gateway/ipsec-ike-policy-howto#s2sconnection" target="_blank">manually configure the parameters for your Azure VPN Gateway</a>, rather than accepting the default configuration provided by Azure.

Due to Maximum Transmission Unit (MTU) limits between Azure and {{site.Brand}}, you'll also need to ensure that you clamp the {{site.Brand}}-side VPN MSS to 1200. (Despite its smaller packet size, {{site.Brand}} has found that clamping your maximum segment size (MSS) to 1200, rather than Azure's standard-recommended 1350, ensures the best performance, and least amount of packet loss when the {{site.Brand}}-side of the VPN is sending data.) It will ensure that your packets flow freely between {{site.Brand}} and Azure, rather than being dropped or bounced, which can cause your VPN traffic to be slow, or even come to a standstill. In the {{site.Brand}} portal, the setting to clamp your VPN's MSS is at the bottom of the Edit WAN page:

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/media/image4.png" width="300">

Since VPNs are most reliable when distance being spanned is short, the two endpoints should be as close together as possible. You'll want to set up the {{site.Brand}} VPN endpoint in a region which is nearest to your existing Azure services. Choose the region in your {{site.Brand}} account which is nearest to your existing Azure vNet, and <a href="https://help.skytap.com/managing-public-ip-addresses.html#AddingastaticpublicIPaddresstoyouraccount" target="_blank">create the static public IP address</a> for your new VPN.

Now you're ready to create your VPN. Using the parameters you've agreed upon with your IT department, <a href="https://help.skytap.com/wan-create-vpn.html" target="_blank">create the VPN endpoint in {{site.Brand}}</a>. Then <a href="https://docs.microsoft.com/en-us/azure/vpn-gateway/ipsec-ike-policy-howto#s2s-vpn-with-ipsecike-policy" target="_blank">configure the VPN Gateway</a> to your Azure virtual network. Once both endpoints are set up, make sure that you specify at least one remote subnet on the {{site.Brand}} side (this is the on-prem network subnet that {{site.Brand}} will be sending to and receiving from), and then **be sure to <a href="https://help.skytap.com/wan-testing.html#Test_the_VPN" target="_blank">test your VPN</a>** to confirm that it will properly establish a tunnel.

Once you've confirmed that your VPN can successfully establish Phase1 and Phase2 connectivity, <a href="https://help.skytap.com/wan-connecting-environments-to-vpn-or-pnc.html#Connect" target="_blank">connect your VPN to its intended the {{site.Brand}} Environment(s)</a>, choose a server from each side of the WAN topology that needs to talk to each other (one from {{site.Brand}} and one from your on-premises network), and <a href="https://help.skytap.com/wan-testing.html#Further" target="_blank">test the final end-to-end connectivity</a> between each server.

###### *[Back to the Top](#toc)*

## Option 3: Global Reach Enabled ExpressRoute<a name="option3"></a>

This is the recommended robust connection path for performance-sensitive workloads connecting directly between {{site.Brand}} and on-premises. Keep in mind, you will need to consider your existing ExpressRoute configuration and latency between the endpoints.

This approach will give you transitive routing between on-premises and {{site.SoA}}. To set this up, create a new Azure Backbone ExpressRoute connection (see architecture below) between the {{site.SoA}} service in Azure US East and your Azure hub. Note that the backbone ExpressRoute is not a new MPLS ExR circuit; rather, it's a native Azure ExR configured in the Portal that does not require you to engage a Telco provider. Once configured in the Azure Portal, Microsoft will create this connection on the backend (may take 20-30 mins. to propagate). Once the Backbone ExpressRoute is established between {{site.Brand}} and your Azure hub, you will enable Global Reach to allow transitive routing between both ExpressRoutes.

### Setting up Global Reach Enabled ExpressRoute to {{site.Brand}}<a name="expressroutewithglobalreach"></a>

**Pre-requisites -- Global Reach Enabled ExpressRoute**

* Azure administrative rights to create the Azure resources:

    * "Back Bone" ExpressRoute. This is not a Telco ExpressRoute (Step 1)

    * Global Reach (Step 3)

* /29 IP address space -- for Global Reach (Step 3 below)

* Any specific application ports that need to traverse your
    on-premises firewall.

    * The ExpressRoute connection into the {{site.Brand}} service is not
        firewalled, so all ports are open; however, it is a private
        connection in your network.

    * The {{site.Brand}} UI does have standard ports it uses for access as
        detailed in the Appendix. Note: Some public IP addresses are
        dependent on the region deployed. The IP's listed in the
        Appendix are based on the intended region for deployment.

**Recommended Path**

The recommended path to connect {{site.SoA}} to on-premises and Azure
resources is a customer-managed ExpressRoute from {{site.Brand}} that has Global
Reach enabled to allow network transit from {{site.Brand}} to on-prem.

* Step 1: Create the Azure circuit that your {{site.Brand}} Customer-Managed ExpressRoute will go through

    * <a href="https://help.skytap.com/wan-create-self-managed-expressroute.html#creating-a-customer-managed-expressroute-circuit-for-a-private-network-connection" target="_blank">Creating a customer-managed ExpressRoute circuit for a Private Network Connection</a>


* Step 2: Create the WAN connection in {{site.SoA}}

    * <a href="https://help.skytap.com/wan-create-expressroute.html" target="_blank">Creating and editing a Private Network Connection with ExpressRoute for your {{site.Brand}} account</a>

    * Be sure to select "Customer-managed circuit" when creating your {{site.Brand}} WAN per the instructions above

* <img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/Azure/media/image3.png" width="450">

  * Enter the service key from Step 1, where you created the ExpressRoute circuit in Azure
  
  * On the WAN Details page for your new circuit, enter the remote subnet of the on-premises network which your existing on-premises ER circuit connects to

  * Wait to enable the WAN in {{site.Brand}} until the "pending" notification is no longer there and you have completed the Global Reach steps below

* Step 3: Peer your new ExpressRoute circuit with your existing on-premises circuit using Global Reach

    * <a href="https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fexpressroute%2Fexpressroute-howto-set-global-reach-portal&data=04%7C01%7Cb-jeffrylane%40microsoft.com%7Cec5729a0938749d66ab108d9a2d2f409%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637719849195262977%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=GsFcP53iCEDjTyZNTbZoOnn0u2D7lsOMBsq6GtLQ2wI%3D&reserved=0" target="_blank">Azure ExpressRoute: Configure Global Reach using the Azure portal \| Microsoft Docs</a>

    * Please note that Step 3 in the Global Reach setup requires a /29 IP address space from your network team that does not overlap with any other on-prem or Azure networks you have deployed.

    * Be sure to notify your security / firewall team of the application ports that need to traverse the connection from {{site.SoA}} to on-prem and back

* Step 4: Enable the WAN in {{site.Brand}}

    * Review and follow Step 16 in the <a href="https://help.skytap.com/wan-create-expressroute.html" target="_blank">Creating and editing a Private Network Connection with ExpressRoute for your {{site.Brand}} account</a> Guide.



* Step 5: Attach the WAN to your <a href="https://help.skytap.com/creating-an-environment.html#creating-an-environment" target="_blank">{{site.Brand}} Environment</a>

    * <a href="https://help.skytap.com/wan-connecting-environments-to-vpn-or-pnc.html#additional-information" target="_blank">Connecting an environment network to a VPN or Private Network Connection</a>

    * Please note, there must be at least one running VM and/or LPAR in the environment and on the network for the connection to start

* Step 6: Test your connection from the VM or LPAR in {{site.Brand}} to an on-prem system within the remote networks set on the WAN during Step 2

    * A simple ping test to be sure the connection flows all the way through

**Reference Architecture - Global Reach Enabled ExpressRoute**

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/Azure/media/image4.png" width="800">

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/Azure/media/image5.png" width="800">

## Appendix<a name="appendix"></a> 

**{{site.Brand}} UI ports:**<a name="UIPorts"></a> 

* <a href="https://help.skytap.com/faq-ip-addresses-and-port-ranges.html#what-ip-addresses-and-port-ranges-does-skytap-use" target="_blank">IP addresses and port ranges for {{site.Brand}}</a>

## [ExpressRoute and Global Reach]({{ site.navigation.Operations }}connectivity/express-route/)<a name="expressroutewithglobalreachguide"></a>

While VPNs are a solid and commonplace solution to traffic isolation and encapsulation, they do suffer from the "vagaries" of the public internet: At any time, new routing hubs can come online, BGP paths can change, and variable internet traffic congestion can impact or even degrade your application's throughput. For performance-sensitive workloads, Azure and {{site.Brand}} recommend <a href="https://docs.microsoft.com/en-in/azure/expressroute/expressroute-introduction" target="_blank">Azure ExpressRoute</a>, a private network connection service which can be configured between your on-premises site and Azure, {{site.Brand}} and Azure, or both.

<a href="https://help.skytap.com/wan-expressroute-overview.html" target="_blank">Creating ExpressRoutes with {{site.Brand}}</a> is a fairly simple process. ExpressRoutes created by {{site.Brand}} are all configured as 1Gbps, using the Standard SKU. Alternatively, you can <a href="https://help.skytap.com/wan-create-self-managed-expressroute.html" target="_blank">create an ExpressRoute in Azure using the set of parameters {{site.Brand}} supports</a>, and connect it to {{site.Brand}}. This is ideal when you want to create an ExpressRoute with a Premium SKU, or with Global Reach, which aren't supported through ERs created by {{site.Brand}}.

What kind of ExpressRoute circuit(s) you'll need, and how they'll be connected to other parts of your WAN, will depend on where your traffic needs to go. In each of the following topologies, whether you need a 'Standard' or 'Premium' ExpressRoute depends on whether your source and target locations are in the <a href="https://docs.microsoft.com/en-us/azure/expressroute/expressroute-locations#locations" target="_blank">same geopolitical region</a>.

## To connect your {{site.Brand}} application just to On-Premises, using an existing ExpressRoute to On-Premises<a name="expressrouteonprem"></a>

To connect your application in {{site.Brand}} to On-Premises, when you already have an ExpressRoute between {{site.Brand}} and Azure, follow: [**Option #3: Global Reach Enabled ExpressRoute**](#option3)

### To connect your {{site.Brand}} application just to an Azure Virtual Network (vNet) through an ExpressRoute<a name="expressroute2vnet"></a> 

<a href="https://help.skytap.com/wan-expressroute-overview.html" target="_blank">Walk through the {{site.Brand}} help documentation</a> to follow the three major steps of connecting your {{site.Brand}} application to your vNet: Create the ER; Configure a vNet Gateway; and Connect the circuit. An even more detailed walkthrough can be found here:
* [{{site.SoA}} - ExpressRoute Configuration]({{ site.navigation.Operations }}connectivity/express-route/)

**Reference Architecture - Azure Virtual Network (vNet) through an ExpressRoute**

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/Azure/media/image6.png" width="800">


### To connect your {{site.Brand}} application to *both* On-Premises and an Azure vNet<a name="expressroute2vnetandonprem"></a> 

The two network topologies described above can also happily co-exist: you can connect the same ExpressRoute circuit from {{site.Brand}} in the first case to the vNet Gateway in the second case.

There's one important caveat for this topology: any traffic sent through the vNet Gateway *won't transit to On-Prem*, even if the On-Prem ExpressRoute circuit is also connected to that same vNet. So if, for example, you need your application traffic to flow to services on Azure, such as a firewall, an Azure VM, or other SaaS offering, before it moves out to on-premises, you'll need transitive routing. To set up transitive routing, follow the steps in Example 5.

### To connect your {{site.Brand}} application over an existing On-Premises ExpressRoute circuit, when the connection between {{site.Brand}} and Azure is a VPN<a name="expressroute2vnetandonprem+vpn"></a>  

VPNs between {{site.Brand}} and Azure are a cost-efficient, low-friction way to allow traffic to transmit encrypted, encapsulated, and generally all within Azure's own backbone. If you want to connect a VPN from {{site.Brand}} to an existing ExpressRoute circuit to your On-Premises site, according to <a href="https://docs.microsoft.com/en-us/azure/expressroute/expressroute-howto-coexist-resource-manager#to-enable-transit-routing-between-expressroute-and-azure-vpn" target="_blank">Azure's documentation</a>, this topology <a href="https://docs.microsoft.com/en-us/azure/route-server/expressroute-vpn-support" target="_blank">requires an Azure Route Server</a>. Microsoft's new Azure Route Server service allows network virtual appliances (NVAs) such as firewalls, ExpressRoute circuits, and VPNs, to connect to one another, as a <a href="https://docs.microsoft.com/en-us/azure/route-server/overview" target="_blank">high-availability managed service</a>.

Once you've created a VPN from {{site.Brand}} to Azure, follow the Azure instructions to <a href="https://docs.microsoft.com/en-us/azure/route-server/quickstart-configure-route-server-portal" target="_blank">create a Route Server</a> on the vNet, and configure route exchange to your {{site.Brand}} VPN and your OnPrem ExpressRoute.

## To connect your {{site.Brand}} application to *both* On-Premises and an Azure vNet, when you need transitive routing<a name="expressroute2vnetandonpremtransroute"></a>  

A common need among your applications is to send traffic out from OnPrem, into an Azure vNet with one or more preexisting Azure SaaS solutions, and then onward to a {{site.Brand}} Environment. Traffic also frequently needs to flow the other way as well.

If the SaaS objects in Azure are VMs and NVAs (such as firewalls) on a particular vNet, Azure Route Server is a low-friction solution, as shown in Example 4.

If the topology you're trying to mesh is relatively simple, you can set up the configuration yourself by <a href="https://docs.microsoft.com/en-us/azure/expressroute/cross-network-connectivity#cross-connecting-vnets" target="_blank">establishing the proper peering relationships and route tables</a> in Azure, bearing in mind that you must be careful to avoid <a href="https://docs.microsoft.com/en-us/azure/expressroute/expressroute-asymmetric-routing" target="_blank">the pitfalls around asymmetric routing</a>.

However, if you need to connect several (or complex topologies of) vNets, you can\'t get the necessary ASNs, your NVAs don't support BGP, or <a href="https://docs.microsoft.com/en-us/azure/virtual-wan/virtual-wan-faq#how-does-virtual-wan-hub-routing-differ-from-azure-route-server-in-a-vnet" target="_blank">Route Server won't work for you for any other reason</a>, an <a href="https://docs.microsoft.com/en-us/azure/virtual-wan/virtual-wan-about" target="_blank">Azure Virtual WAN (vWAN)</a> is a full-service solution for configuring transitive connectivity between {{site.Brand}}, Azure, and OnPrem. The vWAN is a global transit network, with regional hubs. You can use it to establish transitive routing <a href="https://docs.microsoft.com/en-us/azure/virtual-wan/virtual-wan-expressroute-portal" target="_blank">between multiple ExpressRoutes</a> (such as an On-Prem ER, through Azure, to a {{site.Brand}} ER), or <a href="https://docs.microsoft.com/en-us/azure/virtual-wan/virtual-wan-site-to-site-portal" target="_blank">between an ExpressRoute and a VPN</a> (such as an OnPrem ER, through Azure, to a {{site.Brand}} VPN), and through any meshed topology of regional vNets. To allow traffic to transit two ExpressRoutes that are both connected to your vWAN mesh, you'll need to <a href="https://docs.microsoft.com/en-us/azure/virtual-wan/virtual-wan-faq#how-does-the-virtual-hub-in-a-virtual-wan-select-the-best-path-for-a-route-from-multiple-hubs" target="_blank">peer the ER circuits with Global Reach</a>, as in Example 1.

**Reference Architecture - Azure Virtual Network (vNet) to *both* On-Premises and an Azure vNet through an ExpressRoute with Global Reach**

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/Azure/media/image7.png" width="800">

###### *[Back to the Top](#toc)*

### Next steps

**Main Overview**
> [{{site.Brand}} Well-Architected Framework]({{ site.navigation.Home }})

**Operational Excellence**
> [{{site.Brand}} Operational Excellence Pillar]({{ site.navigation.Operations }})
> * [Power Discovery]({{ site.navigation.Operations }}Discovery/)
> * [Connectivity]({{ site.navigation.Operations }}connectivity/)
  * [Getting Started with IBM Cloud Networking]({{ site.navigation.Operations }}connectivity/ibm)
> * [Ecosystems]({{ site.navigation.Operations }}ecosystems/)
> * [Performance]({{ site.navigation.Operations }}performance/)

**Resiliency**
> [{{site.Brand}} Resiliency Pillar]({{ site.navigation.Resiliency }})<br><br>
> **Design**
> * [Design Considerations for Azure]({{ site.navigation.Resiliency }}design-considerations-azure)
> * [Design Considerations for IBM Cloud]({{ site.navigation.Resiliency }}design-considerations-ibm)

**Security**
> [{{site.Brand}} Security Pillar]({{ site.navigation.Security }})
