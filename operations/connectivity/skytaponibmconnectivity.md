---
title: Skytap Operational Excellence - Connectivity - Getting Started with IBM Cloud Networking
author: Colleen E Hamilton - Senior Product Manager
permalink: /operations/connectivity/ibm/
---

# **Getting started with IBM Cloud Networking**

When migrating your application to IBM Cloud for {{site.Brand}} Solutions
(ICSS), your primary Edge Networking solutions will be internet-based:
For applications with a public endpoint, you can set up a static public
IP address, or a dynamic <a href="https://help.skytap.com/comparing-static-and-dynamic-public-ip-addresses.html" target="_blank">public IP address with its own FQDN</a>.
For applications that need private, encrypted connections back to your
on-premises location, ICSS recommends setting up a VPN.

ICSS's built-in VPN service gives customers a streamlined option to
establish a VPN tunnel between your environment in {{site.Brand}}, and your
on-premises deployment. ICSS's VPN tunnels are encrypted and
encapsulated, routed over the public internet.

### Setting up a Site-to-Site VPN in ICSS

<!-- <img src="https://raw.githubusercontent.com/skytap/well-architected-framework/master/operations/connectivity/media/image2.png" width="500"> -->

A VPN is like a bridge: both sides must be "facing each other" for
traffic to flow. This means that each VPN endpoint must be configured
the same way, with the same parameters. Before you begin, speak to your
company's IT department to <a href="https://help.skytap.com/wan-vpn-configuration-parameters.html" target="_blank">find out which VPN parameters</a> you'll need to use for {{site.Brand}}\'s side of the VPN.

Also like a bridge, VPNs are most reliable when distance being spanned
is short, which means the two endpoints should be as close together as
possible: You'll want to set up the {{site.Brand}} VPN endpoint in a region
which is nearest to your corporate VPN endpoint. Choose the region in
your {{site.Brand}} account which is nearest to your corporate endpoint, and
<a href="https://help.skytap.com/managing-public-ip-addresses.html#AddingastaticpublicIPaddresstoyouraccount" target="_blank">create the static public IP address</a>
for your new VPN.

Now you're ready to create your VPN. Using the parameters you've agreed
upon with your IT department, <a href="https://help.skytap.com/wan-create-vpn.html" target="_blank">create the VPN endpoint in
{{site.Brand}}</a>, and work with your company\'s IT department to set up a matching-configuration endpoint on
your On-Premises VPN device. Once both endpoints are set up, make sure
that you specify at least one remote subnet on the {{site.Brand}} side (this is
the on-prem network subnet that {{site.Brand}} will be sending to and receiving
from), and then **be sure to <a href="https://help.skytap.com/wan-testing.html#Test_the_VPN" target="_blank">test your
VPN</a>** to confirm that it will properly establish a tunnel.

Once you've confirmed that your VPN can successfully establish Phase1
and Phase2 connectivity, <a href="https://help.skytap.com/wan-connecting-environments-to-vpn-or-pnc.html#Connect" target="_blank">connect your VPN to its intended the {{site.Brand}} Environment(s)</a>,
choose a server from each side of the WAN topology that need to talk to
each other (one from {{site.Brand}} and one from your on-premises network), and
<a href="https://help.skytap.com/wan-testing.html#Further" target="_blank">test the final end-to-end
connectivity</a> between each server.

### Next steps

**Main Overview**
> [{{site.Brand}} Well-Architected Framework]({{ site.navigation.Home }})

**Operational Excellence**
> [{{site.Brand}} Operational Excellence Pillar]({{ site.navigation.Operations }})
> * [Power Discovery]({{ site.navigation.Operations }}Discovery/)
> * [Connectivity]({{ site.navigation.Operations }}connectivity/)
	* [Getting Started with Azure Networking]({{ site.navigation.Operations }}connectivity/azure)
> * [Ecosystems]({{ site.navigation.Operations }}ecosystems/)
> * [Performance]({{ site.navigation.Operations }}performance/)

**Resiliency**
> [{{site.Brand}} Resiliency Pillar]({{ site.navigation.Resiliency }})<br><br>
> **Design**
> * [Design Considerations for Azure]({{ site.navigation.Resiliency }}design-considerations-azure)
> * [Design Considerations for IBM Cloud]({{ site.navigation.Resiliency }}design-considerations-ibm)

**Security**
> [{{site.Brand}} Security Pillar]({{ site.navigation.Security }})
