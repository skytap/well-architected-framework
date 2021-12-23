---
title: Skytap Operational Excellence - Connectivity - Getting Started with IBM Cloud Networking
author: Colleen E Hamilton - Senior Product Manager
---

# **Getting started with IBM Cloud Networking**

When migrating your application to IBM Cloud for Skytap Solutions
(ICSS), your primary Edge Networking solutions will be internet-based:
For applications with a public endpoint, you can set up a static public
IP address, or a dynamic [public IP address with its own
FQDN](https://help.skytap.com/comparing-static-and-dynamic-public-ip-addresses.html).
For applications that need private, encrypted connections back to your
on-premises location, ICSS recommends setting up a VPN.

ICSS's built-in VPN service gives customers a streamlined option to
establish a VPN tunnel between your environment in Skytap, and your
on-premises deployment. ICSS's VPN tunnels are encrypted and
encapsulated, routed over the public internet.

### Setting up a Site-to-Site VPN in ICSS

<img src="./media/image2.png" width="600">

A VPN is like a bridge: both sides must be "facing each other" for
traffic to flow. This means that each VPN endpoint must be configured
the same way, with the same parameters. Before you begin, speak to your
company's IT department to [find out which VPN
parameters](https://help.skytap.com/wan-vpn-configuration-parameters.html)
you'll need to use for Skytap\'s side of the VPN.

Also like a bridge, VPNs are most reliable when distance being spanned
is short, which means the two endpoints should be as close together as
possible: You'll want to set up the Skytap VPN endpoint in a region
which is nearest to your corporate VPN endpoint. Choose the region in
your Skytap account which is nearest to your corporate endpoint, and
[create the static public IP
address](https://help.skytap.com/managing-public-ip-addresses.html#AddingastaticpublicIPaddresstoyouraccount)
for your new VPN.

Now you're ready to create your VPN. Using the parameters you've agreed
upon with your IT department, [create the VPN endpoint in
Skytap,](https://help.skytap.com/wan-create-vpn.html) and work with your
company\'s IT department to set up a matching-configuration endpoint on
your On-Premises VPN device. Once both endpoints are set up, make sure
that you specify at least one remote subnet on the Skytap side (this is
the on-prem network subnet that Skytap will be sending to and receiving
from), and then **be sure to [test your
VPN](https://help.skytap.com/wan-testing.html#Test_the_VPN)** to confirm
that it will properly establish a tunnel.

Once you've confirmed that your VPN can successfully establish Phase1
and Phase2 connectivity, [connect your VPN to its intended the Skytap
Environment(s)](https://help.skytap.com/wan-connecting-environments-to-vpn-or-pnc.html#Connect),
choose a server from each side of the WAN topology that need to talk to
each other (one from Skytap and one from your on-premises network), and
[test the final end-to-end
connectivity](https://help.skytap.com/wan-testing.html#Further) between
each server.

## Next steps

**Main Overview**
> [Skytap Well-Architected Framework](../../README.md)

**Operational Excellence**
>[Skytap Operational Excellence Pillar](README.md)
>* [Power Discovery](../Discovery/README.md)
>* [Connectivity](README.md) > [Getting Started with Azure Networking](skytaponazureconnectivity.md)

**Resiliency**
> [Skytap Resiliency Pillar](README.md)

>**Design**
>* [Design Considerations for Azure](../../resiliency/designconsiderationsazure.md)
>* [Design Considerations for IBM Cloud](../../resiliency/designconsiderationsibm.md)

**Security**
> [Skytap Security Pillar](../../security/README.md)
