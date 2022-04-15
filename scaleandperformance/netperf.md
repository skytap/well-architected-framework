---
title: Skytap Scalability and Performance - Networking
author: Vivian Su, Senior Engineering Manager - Networking
---

# Network Scalability

## Features Overview

-   Includes information on how to set up networks in Skytap, connect Environments together, how to avoid overlapping IP spaces in multiple scenarios, connect to outbound Internet, etc.

-   [https://help.skytap.com/network-overview.html](https://help.skytap.com/network-overview.html)

## VM Network Settings

-   Includes information about attaching VMs to networks, adding/editing IP addresses & MAC addresses, enabling promiscuous mode, using static IP addresses, and ensuring unique hostnames.

-   [https://help.skytap.com/vm-network-settings.html](https://help.skytap.com/vm-network-settings.html)

<!-- ## Skytap Platform Limits

-   Talk to Product Management and ask for most up-to-date: "*[Limits
    > Document (Select Customers Only) TECHNICAL PAPER Configuration
    > Maximums]{.ul}*"

-   Last Known Link (may be out of date) - **ACTION: Confirm with PM and
    > Docs team**:
    > [[https://docs.google.com/document/d/1zO8Y19sqg8tVTX09ycoKZZTURBXflxCJ02uhkykRGb8/edit]{.ul}](https://docs.google.com/document/d/1zO8Y19sqg8tVTX09ycoKZZTURBXflxCJ02uhkykRGb8/edit)
-->

# Network Performance
**Start Here**

-   Network Connectivity and Performance:
    > [https://help.skytap.com/testing-network-connectivity-and-performance.html](https://help.skytap.com/testing-network-connectivity-and-performance.html)

-   Bandwidth:
    > [https://help.skytap.com/troubleshooting-bandwidth.html](https://help.skytap.com/troubleshooting-bandwidth.html)

-   Latency:
    > [https://help.skytap.com/troubleshooting-latency.html](https://help.skytap.com/troubleshooting-latency.html)

-   Speedtest:
    > [https://help.skytap.com/testing-bandwidth-and-latency-with-speedtest.html](https://help.skytap.com/testing-bandwidth-and-latency-with-speedtest.html)

## Common Tools

-   Common tools for analysis and troubleshooting:
    > [https://confluence.corp.skytap.com/display/SUP/Support+Network+Troubleshooting+Guide](https://confluence.corp.skytap.com/display/SUP/Support+Network+Troubleshooting+Guide)

## TCP Tuning

-   [https://en.wikipedia.org/wiki/TCP_tuning](https://en.wikipedia.org/wiki/TCP_tuning)

 **TCP tuning** techniques adjust the [network congestion avoidance](https://en.wikipedia.org/wiki/Network_congestion_avoidance) parameters of [Transmission Control Protocol](https://en.wikipedia.org/wiki/Transmission_Control_Protocol)(TCP) connections over high-[bandwidth](https://en.wikipedia.org/wiki/Bandwidth_(computing)), high-[latency](https://en.wikipedia.org/wiki/Latency_(engineering)) networks. **Well-tuned networks can perform up to 10 times faster in some cases**.[^\[1\]^](https://en.wikipedia.org/wiki/TCP_tuning#cite_note-1)

However, blindly following instructions without understanding their real consequences can hurt performance as well.

-   [https://en.wikipedia.org/wiki/Bandwidth-delay_product](https://en.wikipedia.org/wiki/Bandwidth-delay_product)

-   [https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-tcpip-performance-tuning](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-tcpip-performance-tuning)

## AIX

Includes information about a variety of configurations, settings, and
tools for working with IBM AIX.

### IBM AIX Network Interface Parameters

-   [https://www.ibm.com/docs/en/aix/7.2?topic=i-ifconfig-command](https://www.ibm.com/docs/en/aix/7.2?topic=i-ifconfig-command)

### IBM AIX Ethernet Device Driver and Device Statistics

-   [https://www.ibm.com/docs/en/aix/7.2?topic=e-entstat-command](https://www.ibm.com/docs/en/aix/7.2?topic=e-entstat-command)

### IBM AIX Device Attributes and Values

-   [https://www.ibm.com/docs/en/aix/7.2?topic=l-lsattr-command](https://www.ibm.com/docs/en/aix/7.2?topic=l-lsattr-command)

### IBM AIX Networking Tunables Documentation

-   [https://www.ibm.com/docs/en/aix/7.2?topic=n-no-comman](https://www.ibm.com/docs/en/aix/7.2?topic=n-no-command)

### LPAR Throughput & AIX Native Largesend

-   [https://confluence.corp.skytap.com/display/NT/Networking+Support+Issues+Index](https://confluence.corp.skytap.com/display/NT/Networking+Support+Issues+Index)

## Troubleshooting Index

Includes conditions to diagnose and troubleshoot throughput performance and connectivity issues.

-   [https://confluence.corp.skytap.com/display/NT/Networking+Support+Issues+Index](https://confluence.corp.skytap.com/display/NT/Networking+Support+Issues+Index)

## SCP and SFTP Performance with WAN

-   [https://fasterdata.es.net/data-transfer-tools/scp-and-sftp/](https://fasterdata.es.net/data-transfer-tools/scp-and-sftp/)

## Azure Reference

[https://docs.microsoft.com/en-us/azure/expressroute/expressroute-troubleshooting-network-performance](https://docs.microsoft.com/en-us/azure/expressroute/expressroute-troubleshooting-network-performance)
