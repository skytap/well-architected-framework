---
title: EXPRESS ROUTE Inter-Connect from Skytap to Azure Native
description: EXPRESS ROUTE Inter-Connect from Skytap to Azure Native
author: Tony Perez - Sales Engineer, Mayank Kumar - Cloud Solutions Architect
---

# EXPRESS ROUTE Inter-Connect from Skytap to Azure Native

## Table of Contents <a name="toc"></a>

* [Create Skytap Environment](#createskytapenvironment)
* [Create an Express Route Definition in Skytap](#createroutedefinition)
* [Create a Resource Group in the Azure Portal](#createazureresourcegroup)
* [Create a Virtual Network to attach the ExpressRoute](#createazurevnet)
* [Create an address space and subnet](#createazureaddressspace)
* [Create a Virtual Network Gateway](#createazureVNG)
* [Create Local Network Gateway]()
* [Add all components to the Virtual Network Gateway]()
* [Create test VM inside of Azure Native]()
* [Test end-to-end connection from Skytap to Azure Native]()
* [APPENDIX]()
  * [Connect AIX LPAR to Express Route]()

## Create Skytap Environment<a name="createskytapenvironment"></a>

Create the initial Skytap environment that contains your VMs or LPARs.

1. Login to the [Azure portal](https://portal.azure.com) and access your Skytap subscription.
   
You should land on the Dashboard page of Skytap.

<img src="./media/image6.png" width="700">
<br>

2. Select **AIX 7.1** in the search field. Select **US-Texas-M-1** as the
Region. Finally, select the AIX Template that matches your criteria.

<img src="./media/image30.png" width="700">

You should see the following page, make note of the default subnet that is
created, you will use that value (as an example: 10.0.0.0/24) when defining your Express
Route Connection.

<img src="./media/image45.png" width="700">

###### *[Back to the Top](#toc)*
## Create an Express Route Definition in Skytap<a name="createroutedefinition"></a>

In Skytap – Establish Private Network Connection with Express Route and Configure a Virtual Network Gateway in Azure for ExpressRoute, then Connect the ExpressRoute Circuit to the Virtual Network Gateway. To do this, you will create an Express Route definition in Skytap using the following steps.

![Express Route Topo](media/ExpressRouteTopo.png)

1. From the Manage Tab, Select **Public IP**

<img src="./media/image33.png" width="700">

2. Allocate a public IP address.

***NOTE***: *Even though the end-point for the Express Route connection is
label \"public IP\" in the user interface, the connection described in
this document does not send traffic onto the public Internet, all the
traffic stays within the Azure data-center.*

3. Click **+ Static public IP address**:

<img src="./media/image4.png" width="300">

4. Select the region where the connection will be created, in this case,
Texas-M1 which is \"South Central\" in Azure.

<img src="./media/image24.png" width="500">

***NOTE***: *The new unattached IP address will be used in defining the Azure side of
the Express Route connection.*

<img src="./media/image18.png" width="500">

5. Now define a new WAN connection in the Skytap user interface.

<img src="./media/image39.png" width="500">
<br>
<img src="./media/image35.png" width="300">

6. Fill in the page and press **Save**.

<img src="./media/image49.png" width="700">

***NOTE***: *You'll see the following message while the connection is being built:*

<img src="./media/image20.png" width="700">

Once finished, you\'ll see the service keys required to define the
Express Route endpoint on the Azure Native side of the connection.

<img src="./media/image11.png" width="700">

If you know what subnet(s) that will be accessed in native Azure, you
can add them now or later. The subnet 10.1.77.0/24 is what will be
defined as the VNET in Azure that the Skytap environment will talk to.

7. Add the remote subnet on the right side of the page.

<img src="./media/image40.png" width="700">

###### *[Back to the Top](#toc)*
## Create a Resource Group in the Azure Portal<a name="createazureresourcegroup"></a>

1. From the Azure Portal, create a Resource Group:

![](./media/image25.png)

2. Give the Resource Group a name: (Example **Skytap DR ExpressRoute-RG**)

![](./media/image14.png)

3. Click **Review & create** and then **Create** to finish creating the
Resource Group.

###### *[Back to the Top](#toc)*
## Create a Virtual Network to attach the ExpressRoute<a name="createazurevnet"></a>

1. <img src="./media/image13.png" width="600">


2. <img src="./media/image22.png" width="600">


3. Click: Go to Resource

<img src="./media/image1.png" width="200">


###### *[Back to the Top](#toc)*
## Create an address space and subnet<a name="createazureaddressspace"></a>

<img src="./media/image9.png" width="800">

1. Create an address space: (Example:10.1.0.0/16)

***NOTE***: *If there are any other address spaces already defined, delete them. See example, remove 10.8.0.0/16 if it exists.*

<img src="./media/image38.png" width="500">

2. Now create a /24 subnet that fits within that address space. (Example: 10.1.77.0/24 in the 10.1.0.0/16)

3. Name this subnet something like the following example: **Skytap-DR-ExpressRoute-SN**

<img src="./media/image21.png" width="500">

You should now have 1 subnet defined:

<img src="./media/image8.png" width="500">

###### *[Back to the Top](#toc)*
## Create a Virtual Network Gateway<a name="createazureVNG"></a>

![](./media/image7.png)

Create a virtual network gateway called: Skytap-DR-ExpressRoute-VNG

**\*\*\* NOTE:** The virtual network gateway creates an IP endpoint that
the Azure user interface calls \"Public IP Address\". The Express Route
connection created in this example **[\"does not\"]{.underline}** send
traffic to the public internet. All the traffic stays within the Azure
Datacenter.

![](./media/image29.png)

![](./media/image28.png)

Click \"Review + create\"

This Azure process can take 30+ minutes to complete....

Once done, click \"Go to Resource\"

![](./media/image2.png)

Review the values of your definition.

###### *[Back to the Top](#toc)*
## Create Local Network Gateway

Define and create a Local Network Gateway.

![](./media/image31.png)

Fill out the values based on what has been defined so far:

![](./media/image32.png)

20.94.177.25 is the IP endpoint defined for the Skytap side of the
Express Route Connection.

10.0.0.0/24 is the address space used by the Skytap environment that we
defined.

Click \"Review and create\" and create the Local Network Gateway.

The deployment in Azure takes a few minutes.

Click on \"Go to Resource\" to review your connection configuration.

![](./media/image2.png)
{width="1.4479166666666667in" height="0.5911614173228347in"}

###### *[Back to the Top](#toc)*
## Add all components to the Virtual Network Gateway

Search for your previously defined VNG called:\
\"Skytap-DR-ExpressRoute-VNG\"

Click \"Connections\"

Click \"+Add\"

![](./media/image23.png)
{width="6.5in" height="3.5833333333333335in"}

Define the new connection inside the VNG:

![](./media/image47.png)
{width="3.716165791776028in" height="7.70625in"}

Authorization Key = The \"Authorization Key\" shown in the WAN
definition within Skytap, see \"Task \#2\"

Peer circuit URI = The \"Resource ID\" shown in the WAN definition
within Skytap, see \"Task \#2\"

###### *[Back to the Top](#toc)*
## Create test VM inside of Azure Native

In order to test the connection from Skytap to Azure, add a VM to the
defined subnet in the Azure VNet and attempt to \"ping\" it from the
Skytap WAN page.

Add a VM to the subnet defined in Azure.

![](./media/image44.png)
{width="4.65625in" height="4.007061461067367in"}

Create the test VM with these values:\
![](./media/image37.png)
{width="4.389583333333333in" height="4.635793963254593in"}

![](./media/image46.png)
{width="5.064583333333333in" height="5.202561242344707in"}

![](./media/image36.png)
{width="4.947916666666667in" height="5.169938757655293in"}

NOTE: Set \"Public IP\" to \"None\" if you don\'t want access to the VM
from the internet. The VM will only have a private IP visible only from
within Azure.

![](./media/image48.png)
{width="4.900762248468942in" height="5.152083333333334in"}

![](./media/image26.png)
{width="4.70625in" height="4.826922572178478in"}

Click \"Review + create\" to get to the final page.

Click \"Create\" to create the VM in the Azure subnet.

It takes several minutes for the VM to be created in Azure.

Once created, click \"Go to resource\" and make sure the status says
\"Running\".

On the VM details page, look for the \"Private IP Address\":

![](./media/image5.png)
{width="4.639583333333333in" height="1.5985739282589677in"}

###### *[Back to the Top](#toc)*
## Test end-to-end connection from Skytap to Azure Native

In the Skytap portal, go to the WAN definition that was created:\
![](./media/image19.png)
{width="6.5in" height="1.8888888888888888in"}

Click \"Test\" and enter the Private IP address from the Azure portal
page:

10.1.77.4

![](./media/image17.png)
{width="4.83125in" height="4.761568241469816in"}

If everything is working, you should get \"Pass\" when pinging the Azure
VM from Skytap.

Enable the Express Route Connection in Skytap:

![](./media/image3.png)
{width="2.50625in" height="0.7133891076115486in"}

Once enabled, you can now attach Skytap environments that includes VM
and LPARs to this Express Route Connection.

\*\*\*\*\*\*\*\*\*\*END\*\*\*\*\*\*\*\*\*\*\*\*\*\*

###### *[Back to the Top](#toc)*
## APPENDIX:

## Connect AIX LPAR to Express Route

Now that the Express Route Connection is working, start the AIX LPAR in
your Skytap Environment and attach the Express Route to it.

Find the original environment that you created in Skytap:

![](./media/image27.png)
{width="6.5in" height="1.5833333333333333in"}

Once you click on it, you\'ll see this page.

Click on the \"Power On\" button to start the LPAR.

![](./media/image41.png)
{width="2.7270297462817146in" height="6.352083333333334in"}

Once powered on, the background of the LPAR will turn \"green\", and
you\'ll see some text on the little console icon.

Click on \"Network Settings\"

![](./media/image34.png)
{width="6.5in" height="2.361111111111111in"}

Then \"Attach to WANs\"

![](./media/image42.png)
{width="4.919132764654418in" height="3.366375765529309in"}

Select the WAN definition that was previously created:

![](./media/image16.png)
{width="6.5in" height="2.6666666666666665in"}

Click \"Attach\", and then \"Connect\".

Then click \"Close\".

Click \"Back\" button in the upper left corner.

![](./media/image10.png)
{width="4.722916666666666in" height="2.217652012248469in"}

Open the AIX console.

Click on the console icon, the terminal will open.

![](./media/image15.png)
{width="3.0729166666666665in" height="3.400924103237095in"}

The default user and password:

![](./media/image43.png)
{width="5.18125in" height="3.694961723534558in"}

Finally ping the VM in Azure:

```bash
ping 10.1.77.4
```


![](./media/image12.png)
{width="6.5in" height="3.0416666666666665in"}

Your Skytap AIX LPAR is now communicating with a VM in Azure Native.

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
