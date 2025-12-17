---
title: EXPRESS ROUTE Inter-Connect from Skytap to Azure Native
author: Tony Perez - Sales Engineer, Mayank Kumar - Cloud Solutions Architect, Matthew Romero, Technical Product Marketing Manager 
permalink: /operations/connectivity/express-route/
---

# {{page.title}}

## Table of Contents<a name="toc"></a>

* [Create {{site.Brand}} Environment](#createskytapenvironment)
* [Create an Express Route Definition in {{site.Brand}}](#createroutedefinition)
* [Create a Resource Group in the Azure Portal](#createazureresourcegroup)
* [Create a Virtual Network to attach the ExpressRoute](#createazurevnet)
* [Create an address space and subnet](#createazureaddressspace)
* [Create a Virtual Network Gateway](#createazureVNG)
* [Create Local Network Gateway](#createazureLNG)
* [Add all components to the Virtual Network Gateway](#hookupazureVNG)
* [Create test VM inside of Azure Native](#createazuretestvm)
* [Test end-to-end connection from {{site.Brand}} to Azure Native](#testconnection)
* [Appendix](#appendix)
  * [Connect AIX LPAR to Express Route](#connectaixviaexpressroute)

## Create {{site.Brand}} Environment<a name="createskytapenvironment"></a>

Create the initial {{site.Brand}} environment that contains your VMs or LPARs.

1. Login to the <a href="https://portal.azure.com" target="_blank">Azure portal</a> and access your {{site.Brand}} subscription.

    You should land on the Dashboard page of {{site.Brand}}.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image6.png)

1. Select **AIX 7.1** in the search field. Select **US-Texas-M-1** as the Region. Finally, select the AIX Template that matches your criteria.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image30.png)

    You should see the following page, make note of the default subnet that is created, you will use that value (as an example: 10.0.0.0/24) when defining your Express Route Connection.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image45.png)

<a name="createroutedefinition"></a>

## Create an Express Route Definition in {{site.Brand}}

In {{site.Brand}} – Establish Private Network Connection with Express Route and Configure a Virtual Network Gateway in Azure for ExpressRoute, then Connect the ExpressRoute Circuit to the Virtual Network Gateway. To do this, you will create an Express Route definition in {{site.Brand}} using the following steps.

[Express Route Topo](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/ExpressRouteTopo.png)

![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/ExpressRouteTopo.png)

1. From the Manage Tab, Select **Public IP**

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image33.png)

1. Allocate a public IP address.

    _**Note** Even though the end-point for the Express Route connection is label **public IP** in the user interface, the connection described in this document does not send traffic onto the public Internet, all the traffic stays within the Azure data-center._

1. Click **+ Static public IP address**:

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image4.png)

1. Select the region where the connection will be created, in this case, Texas-M1 which is **South Central** in Azure.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image24.png)

    _**Note** The new unattached IP address will be used in defining the Azure side of the Express Route connection._

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image18.png)

1. Now define a new WAN connection in the {{site.Brand}} user interface.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image39.png)

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image35.png)

1. Fill in the page and press **Save**.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image49.png)

    _**Note** You'll see the following message while the connection is being built:_

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image20.png)

    Once finished, you\'ll see the service keys required to define the Express Route endpoint on the Azure Native side of the connection.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image11.png)

    If you know what subnet(s) that will be accessed in native Azure, you can add them now or later. The subnet 10.1.77.0/24 is what will be defined as the VNET in Azure that the {{site.Brand}} environment will talk to.

1. Add the remote subnet on the right side of the page.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image40.png)

    <a name="createazureresourcegroup"></a>

## Create a Resource Group in the Azure Portal

1. From the Azure Portal, create a Resource Group:

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image25.png)

1. Give the Resource Group a name: (Example **{{site.Brand}} DR ExpressRoute-RG**)

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image14.png)

1. Click **Review & create** and then **Create** to finish creating the Resource Group.

<a name="createazurevnet"></a>

## Create a Virtual Network to attach the ExpressRoute

1. ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image13.png)
1. ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image22.png)
1. Click: Go to Resource

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image1.png)

<a name="createazureaddressspace"></a>

## Create an address space and subnet

1. Create an address space: (Example:10.1.0.0/16)

    _**Note** If there are any other address spaces already defined, delete them. See example, remove 10.8.0.0/16 if it exists._

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image9.png)

1. Now create a /24 subnet that fits within that address space. (Example: 10.1.77.0/24 in the 10.1.0.0/16)

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image38.png)

1. Name this subnet something like the following example: **{{site.Brand}}-DR-ExpressRoute-SN**

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image21.png)

    You should now have one subnet defined:

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image8.png)

<a name="createazureVNG"></a>

## Create a Virtual Network Gateway

![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image7.png)

1. Create a virtual network gateway, and name it similar to the following example: **{{site.Brand}}-DR-ExpressRoute-VNG**

    _**Note** The virtual network gateway creates an IP endpoint that the Azure user interface calls **Public IP Address**. The Express Route connection created in this example **does not** send traffic to the public Internet. All the traffic will stay within the Azure Data center._

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image29.png)

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image28.png)

1. Click **Review + create**.

    _**Note** This Azure process can take 30+ minutes to complete._

1. Once done, click **Go to Resource**.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image2.png)

1. Review the values of your definition.

<a name="createazureLNG"></a>

## Create Local Network Gateway

Define and create a Local Network Gateway.

![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image31.png)

1. Fill out the values based on what has been defined so far:

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image32.png)

    _**Note** In our example 20.94.177.25 is the IP endpoint defined for the {{site.Brand}} side of the Express Route Connection, and 10.0.0.0/24 is the address space used by the {{site.Brand}} environment that we defined._

1. Click **Review and create** to create the Local Network Gateway.

    _**Note** This Azure process can take few minutes to complete._

1. Click on **Go to Resource** to review your connection configuration.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image2.png)

<a name="hookupazureVNG"></a>

## Add all components to the Virtual Network Gateway

1. Search for your previously defined Virtual Network Gateway (VNG)

    (Example: **{{site.Brand}}-DR-ExpressRoute-VNG**)

1. Click **Connections**.
1. Click **+ Add**.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image23.png)

1. Define the new connection inside the VNG:

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image47.png)

    * Authorization Key = The **Authorization Key** shown in the WAN definition within {{site.Brand}}.
    * Peer circuit URI = The **Resource ID** shown in the WAN definition within {{site.Brand}}.

<a name="createazuretestvm"></a>

## Create test VM inside of Azure Native

In order to test the connection from {{site.Brand}} to Azure, add a VM to the defined subnet in the Azure VNet and attempt to **ping** it from the {{site.Brand}} WAN page.

1. Add a VM to the subnet defined in Azure.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image44.png)

1. Create the test VM with these values:

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image37.png)

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image46.png)

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image36.png)

    _**Note** Set **Public IP** to **None** if you don't want access to the VM from the Internet. The VM will only have a private IP visible only from within Azure._

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image48.png)

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image26.png)

1. Click **Review + create** to get to the final page.

1. Click **Create** to create the VM in the Azure subnet.

    _**Note** It takes several minutes for the VM to be created in Azure._

1. Once created, click **Go to resource** and make sure the status says **Running**.

1. On the VM details page, look for the **Private IP Address**:

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image5.png)

<a name="testconnection"></a>

## Test end-to-end connection from {{site.Brand}} to Azure Native

1. In the {{site.Brand}} portal, go to the WAN definition that was created:

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image19.png)

1. Click **Test** and enter the Private IP address from the Azure portal page. In our example: 10.1.77.4

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image17.png)

1. If everything is working, you should get **Pass** when pinging the Azure VM from {{site.Brand}}.
1. Enable the Express Route Connection in {{site.Brand}}:

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image3.png)

    Once enabled, you can now attach {{site.Brand}} environments that includes VM and LPARs to this Express Route Connection.

<a name="appendix"></a>

## Appendix

<a name="connectaixviaexpressroute"></a>

## Connect AIX LPAR to Express Route

Now that the Express Route Connection is working, start the AIX LPAR in your {{site.Brand}} Environment and attach the Express Route to it.

1. Find the original environment that you created in {{site.Brand}}:

    Once you click on it, you'll see this page.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image27.png)

1. Click on the **Power On** button to start the LPAR.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image41.png)

    _**Note** Once powered on, the background of the LPAR will turn **green**, and you'll see some text on the little console icon._

1. Click on **Network Settings**

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image34.png)

1. Then **Attach to WANs**

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image42.png)

1. Select the WAN definition that was previously created:

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image16.png)

1. Click **Attach**, and then **Connect**.
1. Then click **Close**.
1. Click **Back** button in the upper left corner.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image10.png)

1. Open the AIX console.
1. Click on the console icon, the terminal will open.

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image15.png)

    _**Note** The default user and password below_

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image43.png)

1. Finally ping the VM in Azure:

    ```bash
    ping 10.1.77.4
    ```

    ![](https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image12.png)

Your {{site.Brand}} AIX LPAR is now communicating with a VM in Azure Native.
