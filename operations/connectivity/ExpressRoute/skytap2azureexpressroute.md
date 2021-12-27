---
title: EXPRESS ROUTE Inter-Connect from Skytap to Azure Native
author: Tony Perez - Sales Engineer, Mayank Kumar - Cloud Solutions Architect, Matthew Romero, Technical Product Marketing Manager 
---

# EXPRESS ROUTE Inter-Connect from Skytap to Azure Native

## Table of Contents<a name="toc"></a>

* [Create Skytap Environment](#createskytapenvironment)
* [Create an Express Route Definition in Skytap](#createroutedefinition)
* [Create a Resource Group in the Azure Portal](#createazureresourcegroup)
* [Create a Virtual Network to attach the ExpressRoute](#createazurevnet)
* [Create an address space and subnet](#createazureaddressspace)
* [Create a Virtual Network Gateway](#createazureVNG)
* [Create Local Network Gateway](#createazureLNG)
* [Add all components to the Virtual Network Gateway](#hookupazureVNG)
* [Create test VM inside of Azure Native](#createazuretestvm)
* [Test end-to-end connection from Skytap to Azure Native](#testconnection)
* [APPENDIX](#appendix)
  * [Connect AIX LPAR to Express Route](#connectaixviaexpressroute)

## Create Skytap Environment<a name="createskytapenvironment"></a>

Create the initial Skytap environment that contains your VMs or LPARs.

1. Login to the [Azure portal](https://portal.azure.com) and access your Skytap subscription.
   
You should land on the Dashboard page of Skytap.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image6.png" width="700">
<br>

2. Select **AIX 7.1** in the search field. Select **US-Texas-M-1** as the
Region. Finally, select the AIX Template that matches your criteria.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image30.png" width="700">

You should see the following page, make note of the default subnet that is
created, you will use that value (as an example: 10.0.0.0/24) when defining your Express
Route Connection.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image45.png" width="700">

###### *[Back to the Top](#toc)*
## Create an Express Route Definition in Skytap<a name="createroutedefinition"></a>

In Skytap – Establish Private Network Connection with Express Route and Configure a Virtual Network Gateway in Azure for ExpressRoute, then Connect the ExpressRoute Circuit to the Virtual Network Gateway. To do this, you will create an Express Route definition in Skytap using the following steps.

![Express Route Topo](media/ExpressRouteTopo.png)

1. From the Manage Tab, Select **Public IP**

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image33.png" width="700">

2. Allocate a public IP address.

***NOTE***: *Even though the end-point for the Express Route connection is
label **public IP** in the user interface, the connection described in
this document does not send traffic onto the public Internet, all the
traffic stays within the Azure data-center.*

3. Click **+ Static public IP address**:

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image4.png" width="300">

4. Select the region where the connection will be created, in this case,
Texas-M1 which is **South Central** in Azure.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image24.png" width="500">

***NOTE***: *The new unattached IP address will be used in defining the Azure side of
the Express Route connection.*

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image18.png" width="500">

5. Now define a new WAN connection in the Skytap user interface.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image39.png" width="500">
<br>
<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image35.png" width="300">

6. Fill in the page and press **Save**.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image49.png" width="700">

***NOTE***: *You'll see the following message while the connection is being built:*

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image20.png" width="700">

Once finished, you\'ll see the service keys required to define the
Express Route endpoint on the Azure Native side of the connection.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image11.png" width="700">

If you know what subnet(s) that will be accessed in native Azure, you
can add them now or later. The subnet 10.1.77.0/24 is what will be
defined as the VNET in Azure that the Skytap environment will talk to.

7. Add the remote subnet on the right side of the page.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image40.png" width="700">

###### *[Back to the Top](#toc)*
## Create a Resource Group in the Azure Portal<a name="createazureresourcegroup"></a>

1. From the Azure Portal, create a Resource Group:

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image25.png" width="600">

2. Give the Resource Group a name: (Example **Skytap DR ExpressRoute-RG**)

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image14.png" width="600">

3. Click **Review & create** and then **Create** to finish creating the
Resource Group.

###### *[Back to the Top](#toc)*
## Create a Virtual Network to attach the ExpressRoute<a name="createazurevnet"></a>

1. <img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image13.png" width="600">

2. <img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image22.png" width="600">

3. Click: Go to Resource

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image1.png" width="200">

###### *[Back to the Top](#toc)*
## Create an address space and subnet<a name="createazureaddressspace"></a>

1. Create an address space: (Example:10.1.0.0/16)

***NOTE***: *If there are any other address spaces already defined, delete them. See example, remove 10.8.0.0/16 if it exists.*
<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image9.png" width="800">

2. Now create a /24 subnet that fits within that address space. (Example: 10.1.77.0/24 in the 10.1.0.0/16)

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image38.png" width="500">

3. Name this subnet something like the following example: **Skytap-DR-ExpressRoute-SN**

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image21.png" width="500">

You should now have 1 subnet defined:

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image8.png" width="500">

###### *[Back to the Top](#toc)*
## Create a Virtual Network Gateway<a name="createazureVNG"></a>

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image7.png" width="500">

1. Create a virtual network gateway, and name it similar to the following example: **Skytap-DR-ExpressRoute-VNG**

***NOTE***: *The virtual network gateway creates an IP endpoint that the Azure user interface calls **Public IP Address**. The Express Route connection created in this example **does not** send traffic to the public Internet. All the traffic will stay within the Azure Data center.*

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image29.png" width="700">

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image28.png" width="700">

2. Click **Review + create**.

***NOTE***: *This Azure process can take 30+ minutes to complete.*

3. Once done, click **Go to Resource**.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image2.png" width="200">

4. Review the values of your definition.

###### *[Back to the Top](#toc)*
## Create Local Network Gateway<a name="createazureLNG"></a>

Define and create a Local Network Gateway.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image31.png" width="500">

1. Fill out the values based on what has been defined so far:

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image32.png" width="700">

***NOTE***: *In our example 20.94.177.25 is the IP endpoint defined for the Skytap side of the Express Route Connection, and 10.0.0.0/24 is the address space used by the Skytap environment that we defined.*

2. Click **Review and create** to create the Local Network Gateway.

***NOTE***: *This Azure process can take few minutes to complete.*

3. Click on **Go to Resource** to review your connection configuration.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image2.png" width="200">

###### *[Back to the Top](#toc)*
## Add all components to the Virtual Network Gateway<a name="hookupazureVNG"></a>

1. Search for your previously defined Virtual Network Gateway (VNG) 

(Example: **Skytap-DR-ExpressRoute-VNG**)

2. Click **Connections**.

3. Click **+ Add**.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image23.png" width="700">

4. Define the new connection inside the VNG:

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image47.png" width="400">

* Authorization Key = The **Authorization Key** shown in the WAN
definition within Skytap.

* Peer circuit URI = The **Resource ID** shown in the WAN definition
within Skytap.

###### *[Back to the Top](#toc)*
## Create test VM inside of Azure Native<a name="createazuretestvm"></a>

In order to test the connection from Skytap to Azure, add a VM to the
defined subnet in the Azure VNet and attempt to **ping** it from the
Skytap WAN page.

1. Add a VM to the subnet defined in Azure.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image44.png" width="600">

2. Create the test VM with these values:\
<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image37.png" width="600">

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image46.png" width="600">

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image36.png" width="600">

***NOTE***: *Set **Public IP** to **None** if you don't want access to the VM from the Internet. The VM will only have a private IP visible only from within Azure.*

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image48.png" width="600">

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image26.png" width="600">

3. Click **Review + create** to get to the final page.

4. Click **Create** to create the VM in the Azure subnet.

***NOTE***: *It takes several minutes for the VM to be created in Azure.*

5. Once created, click **Go to resource** and make sure the status says **Running**.

6. On the VM details page, look for the **Private IP Address**:

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image5.png" width="400">

###### *[Back to the Top](#toc)*
## Test end-to-end connection from Skytap to Azure Native<a name="testsconnection"></a>

1. In the Skytap portal, go to the WAN definition that was created:\
<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image19.png" width="700">

2. Click **Test** and enter the Private IP address from the Azure portal
page. In our example: 10.1.77.4

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image17.png" width="400">

3. If everything is working, you should get **Pass** when pinging the Azure
VM from Skytap.

4. Enable the Express Route Connection in Skytap:

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image3.png" width="300">

Once enabled, you can now attach Skytap environments that includes VM
and LPARs to this Express Route Connection.

###### *[Back to the Top](#toc)*
## APPENDIX:<a name="appendix"></a>

## Connect AIX LPAR to Express Route<a name="connectaixviaexpressroute"></a>

Now that the Express Route Connection is working, start the AIX LPAR in
your Skytap Environment and attach the Express Route to it.

1. Find the original environment that you created in Skytap:

Once you click on it, you'll see this page.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image27.png" width="700">

2. Click on the **Power On** button to start the LPAR.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image41.png" width="300">

***NOTE***: *Once powered on, the background of the LPAR will turn **green**, and you'll see some text on the little console icon.*

3. Click on **Network Settings**

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image34.png" width="700">

4. Then **Attach to WANs**

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image42.png" width="700">

5. Select the WAN definition that was previously created:

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image16.png" width="700">

6. Click **Attach**, and then **Connect**.

7. Then click **Close**.

8. Click **Back** button in the upper left corner.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image10.png" width="700">

9. Open the AIX console.

10. Click on the console icon, the terminal will open.

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image15.png" width="400">

***NOTE***: *The default user and password below*

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image43.png" width="500">

11. Finally ping the VM in Azure:

```bash
ping 10.1.77.4
```

<img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/ExpressRoute/media/image12.png" width="500">

Your Skytap AIX LPAR is now communicating with a VM in Azure Native.
###### *[Back to the Top](#toc)*
## Next steps

**Main Overview**
> [Skytap Well-Architected Framework](../../../README.md)

**Operational Excellence**
>[Skytap Operational Excellence Pillar](../../README.md)
>* [Power Discovery](../../Discovery/README.md)
>* [Connectivity](../README.md) > [Getting Started with Azure Networking](../skytaponazureconnectivity.md)
<!--  
> [Getting Started with IBM Cloud Networking](../skytaponibmconnectivity.md)
-->

**Resiliency**
> [Skytap Resiliency Pillar](../../../resiliency/README.md)

>**Design**
>* [Design Considerations for Azure](../../../resiliency/designconsiderationsazure.md)
<!--
>* [Design Considerations for IBM Cloud](../../../resiliency/designconsiderationsibm.md)
-->

**Security**
> [Skytap Security Pillar](../../../security/README.md)
