**Skytap on Azure Network Connectivity**

**Decision Framework**

Three options:

1)  Direct VPN into Skytap

2)  VPN into Azure hub

3)  Global Reach Enabled ExpressRoute

**Considerations**

-   **Regions**: [Skytap Global
    Regions](https://help.skytap.com/understanding-regions.html#understanding-regions)

-   **Important**: **create a LOCK on the Azure resource group** that
    contains the Skytap on Azure service. This lock is may help prevent
    someone from accidentally deleting your Skytap on Azure subscription
    that resides in an Azure resource group.

-   Satellite Centers Connectivity:

    -   There are two methods to connect satellite centers into Skytap
        on Azure via VPN connection:

        -   **Option 1:** VPN between satellite facilities
            **terminating** **directly** into Skytap on Azure --
            **Satellite facilities à Skytap on Azure**

        -   **Option 2**: VPN connection between satellite facilities
            and Azure VPN Gateways in your Azure hub leveraging an Azure
            Virtual WAN hub or an Azure Route Server

    -   **Best practice:** To determine latency between your
        connectivity into Skytap on Azure (i.e., your DC, Distribution
        Warehouse), we recommend conducting a [Skytap Speed
        Test](http://speedtest.skytap.com/).

**Option 1: Direct VPN Connection into Skytap**

For context, this methodology will leverage a direct VPN into Skytap on
Azure (bypassing the Azure hub network). While the Global Reach enabled
ExpressRoute (option 3 below) is the most robust option, consider this
direct VPN approach when the satellite facilities do not require access
to additional Azure resources.

Keep in mind that this option may have the highest latency; however, the
latency may be within the margin of successful operations. As is the
case with all options, Skytap recommends conducting a Skytap Speed Test
as mentioned above.

Links to helpful resources:

-   [Skytap VPN
    documentation](https://help.skytap.com/wan-create-vpn.html#creating-a-vpn-connection-to-your-skytap-account)
    on how to make VPN connections into Skytap on Azure:

**Reference Architecture**

![Diagram Description automatically
generated](I:\Repos\Skytap\WAF\operations\Connectivity\Azure/media/image1.png){width="6.5in"
height="3.582638888888889in"}

**Option 2: VPN into Azure WAN Hub**

For context, the VPN into Azure Hub option may provide a lower latency
connection over Option 1 (direct VPN into Skytap) and perhaps even
Option 3 due to the specific routing of your current ExpressRoute.

In this case, you will establish VPN connections between satellite
facilities and the closest Azure VPN point-of-presence (POP) which
leverages the Azure backbone to hit your Azure hub region. Once there,
you will either leverage an [Azure VWAN
HUB](https://docs.microsoft.com/en-us/azure/virtual-wan/virtual-wan-global-transit-network-architecture)
or [Azure Route
Server](https://docs.microsoft.com/en-us/azure/route-server/overview)

Links to helpful resources:

-   Specific Azure doc relevant to this option: [About Azure Route
    Server supports for ExpressRoute and Azure VPN \| Microsoft
    Docs](https://docs.microsoft.com/en-us/azure/route-server/expressroute-vpn-support#:~:text=Azure%20Route%20Server%20supports%20not%20only%20third-party%20network,peering%20between%20the%20gateway%20and%20Azure%20Route%20Server.)

-   VWAN Hub info
    <https://docs.microsoft.com/en-us/azure/virtual-wan/virtual-wan-about>

**Reference Architecture**

![Diagram Description automatically
generated](I:\Repos\Skytap\WAF\operations\Connectivity\Azure/media/image2.png){width="6.5in"
height="3.588888888888889in"}

**Option 3: Global Reach Enabled ExpressRoute**

This is the recommended robust connection path for performance-sensitive
workloads connecting directly between Skytap to On-Prem. Keep in mind,
you will need to consider your existing ExpressRoute configuration and
latency between the endpoints.

This approach will give you transitive routing between on-premises and
Skytap-on-Azure. To set this up, create a new Azure Backbone
ExpressRoute connection (see architecture below) between the Skytap on
Azure service in Azure US East and your Azure hub. Note that the
backbone ExpressRoute is not a new MPLS ExR circuit; rather, it's a
native Azure ExR configured in the Portal that does not require you to
engage a Telco provider. Once configured in the Azure Portal, Microsoft
will create this connection on the backend (may take 20-30 mins. to
propagate). Once the Backbone ExpressRoute is established between Skytap
and your Azure hub, you will enable Global Reach to allow transitive
routing between both ExpressRoutes.

**Pre-requisites -- Global Reach Enabled ExpressRoute**

-   Azure administrative rights to create the Azure resources:

    -   "Back Bone" ExpressRoute. This is not a Telco ExpressRoute
        (Step 1)

    -   Global Reach (Step 3)

-   /29 IP address space -- for Global Reach (Step 3 below)

-   Any specific application ports that need to traverse your
    on-premises firewall.

    -   The ExpressRoute connection into the Skytap service is not
        firewalled, so all ports are open; however, it is a private
        connection in your network.

    -   The Skytap UI does have standard ports it uses for access as
        detailed in the Appendix. Note: Some public IP addresses are
        dependent on the region deployed. The IP's listed in the
        Appendix are based on the intended region for deployment.

**Recommend Path**

The recommended path to connect Skytap on Azure to on-premises and Azure
resources is a customer-managed ExpressRoute from Skytap that has Global
Reach enabled to allow network transit from Skytap to on-prem.

-   Step 1: Create the Azure circuit that your Sktyap Customer-Managed
    ExpressRoute will go through

    -   [Creating a customer-managed ExpressRoute circuit for a Private
        Network
        Connection](https://help.skytap.com/wan-create-self-managed-expressroute.html#creating-a-customer-managed-expressroute-circuit-for-a-private-network-connection)

-   Step 2: Create the WAN connection in Skytap-on-Azure

    -   [Creating and editing a Private Network Connection with
        ExpressRoute for your Skytap
        account](https://help.skytap.com/wan-create-expressroute.html)

    -   Be sure to select "Customer-managed circuit" when creating your
        Skytap WAN per the instructions above

        -   ![](I:\Repos\Skytap\WAF\operations\Connectivity\Azure/media/image3.png){width="2.6458333333333335in"
            height="1.5654516622922134in"}

    -   Enter the service key from Step 1, where you created the
        ExpressRoute circuit in Azure

    -   On the WAN Details page for your new circuit, enter the remote
        subnet of the on-premises network which your existing
        on-premises ER circuit connects to

    -   Wait to enable the WAN in Skytap until the "pending"
        notification is no longer there and you have completed the
        Global Reach steps below

-   Step 3: Peer your new ExpressRoute circuit with your existing
    on-premises circuit using Global Reach

    -   [Azure ExpressRoute: Configure Global Reach using the Azure
        portal \| Microsoft
        Docs](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fexpressroute%2Fexpressroute-howto-set-global-reach-portal&data=04%7C01%7Cb-jeffrylane%40microsoft.com%7Cec5729a0938749d66ab108d9a2d2f409%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637719849195262977%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=GsFcP53iCEDjTyZNTbZoOnn0u2D7lsOMBsq6GtLQ2wI%3D&reserved=0)

    -   Please note that Step 3 in the Global Reach setup requires a /29
        IP address space from your network team that does not overlap
        with any other on-prem or Azure networks you have deployed.

    -   Be sure to notify your security / firewall team of the
        application ports that need to traverse the connection from
        Skytap on Azure to on-prem and back

-   Step 5: Enable the WAN in Skytap

    -   Step 16 of - [Creating and editing a Private Network Connection
        with ExpressRoute for your Skytap
        account](https://help.skytap.com/wan-create-expressroute.html)

-   Step 6: Attach the WAN to your [Skytap
    Environment](https://help.skytap.com/creating-an-environment.html#creating-an-environment)

    -   [Connecting an environment network to a VPN or Private Network
        Connection](https://help.skytap.com/wan-connecting-environments-to-vpn-or-pnc.html#additional-information)

    -   Please note, there must be at least one running VM and/or LPAR
        in the environment and on the network for the connection to
        start

-   Step 7: Test your connection from the VM or LPAR in Skytap to an
    on-prem system within the remote networks set on the WAN during Step
    2

    -   A simple ping test to be sure the connection flows all the way
        through

**Reference Architecture Global Reach Enabled ExpressRoute**

![Diagram Description automatically
generated](I:\Repos\Skytap\WAF\operations\Connectivity\Azure/media/image4.png){width="6.5in"
height="3.613888888888889in"}![](I:\Repos\Skytap\WAF\operations\Connectivity\Azure/media/image5.png){width="6.270833333333333in"
height="3.422830271216098in"}

**Appendix** **Skytap UI ports:**

[**IP addresses and port ranges for
Skytap**](https://help.skytap.com/faq-ip-addresses-and-port-ranges.html#what-ip-addresses-and-port-ranges-does-skytap-use)
