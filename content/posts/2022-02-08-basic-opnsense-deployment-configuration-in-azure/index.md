---
title: "Basic OPNsense Deployment &amp; Configuration in Azure"
date: "2022-02-08"
categories: 
  - "azure"
  - "cloud"
  - "networking"
tags: 
  - "firewalls"
  - "opnsense"
---

## Overview

Following on from my previous post titled ['Firewall Options for the cost conscious Azure lab owner](https://guiltyspark.cloud/2021/11/13/firewall-options-for-the-cost-conscious-azure-lab-owner/)' in this post I'm going to go talk about the deployment of OPNsense firewalls in Azure, a note about the costs of running the required components and some basic configuration of the OPNsense firewalls to get things working.

## Deployment

Using the code in this repository https://github.com/dmauser/opnazure/ you can create some fairly cost effective firewalls in Azure. From the repository readme: "Those template allows you to deploy an OPNsense Firewall VM using the opnsense-bootsrtap installation method. It creates an FreeBSD VM, does a silent install of OPNsense using a modified version of opnsense-bootstrap.sh...."

I won't re-hash here how to run the deployment as that is documented on the linked to Github repository. Basically using the information in the repository you end up deploying one or more Azure Virtual Machines running FreeBSD and OPNsense is then installed onto the virtual machines. The deployment also applies some configuration to get things on the OPNsense firewalls working in Azure.

You can choose from a variety of options for the deployment such as single firewall or dual firewalls using Azure load balancers for high availability. My deployment uses two firewalls as per the below:

![](https://bluesun2017.files.wordpress.com/2022/02/untitled-diagram.drawio3.png?w=586)

## A note on costs

I'm using Standard B2s virtual machines for this deployment and those combined with the public IP address and load balancers which for me will be somewhere in the region of £90 per month if I was running the virtual machines 24/7. (ymmv)

## Architecture

I'm using a [hub and spoke network](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?tabs=cli) model. The subnets in the spoke networks have a [route table](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-udr-overview) assigned to them which has a default route of 0.0.0.0/0 -> 172.20.1.36 (172.20.1.36 is the IP address of the internal load balancer)

![](https://bluesun2017.files.wordpress.com/2022/02/untitled-diagram.drawio8.png?w=627)

## OPNSense Configuration

During deployment some OPNsense configuration is applied to get OPNsense to work in Azure, this configuration includes:

Assigning the correct interfaces to trust/untrust (LAN/WAN)

Adding firewall rules so the WAN interface can get a DHCP address from the Azure fabric

Firewall rule to allow probes from the Azure loadbalancer

Adds some system gateways

Configures the high availability settings, if you chose the HA option when deploying

## Additional Configuration

My aim here to is to allow internet access for virtual machines in one of my spoke virtual networks, more specifically to allow virtual machines on the 192.168.20.128/28 subnet access to the internet. In order to allow internet access from the virtual machines in my spoke networks some additional configuration is required:

Add [aliases](https://docs.opnsense.org/manual/aliases.html) for the spoke networks

- Within the OPNsense web gui
- Firewall - Alias
- Add an alias
- Here I add an alias for the subnet 192.168.20.128/28 which is a subnet in my spoke network of 192.168.20.0/24

![](https://bluesun2017.files.wordpress.com/2022/02/spoke1.jpg?w=1024)

NAT configuration:

By default the NAT configuration (Firewall -> NAT) will be set to 'Automatic outbound NAT rule generation'

![](https://bluesun2017.files.wordpress.com/2022/02/opnsense1.jpg?w=1024)

![](https://bluesun2017.files.wordpress.com/2022/02/opnsense2.jpg?w=1024)

Because our spoke network is not included in those source networks in the NAT configuration we need to change the outbound NAT configuration to 'Hybrid outbound NAT rule generation' and add a NAT rule for our alias

![](https://bluesun2017.files.wordpress.com/2022/02/opnsense3.jpg?w=1024)

![](https://bluesun2017.files.wordpress.com/2022/02/opnsense4.jpg?w=1024)

In the above we have added an outbound NAT rule for our spoke1 alias.

Finally we need to add firewall rule to allow outbound traffic to the internet from our alias. To only allow traffic to the internet and not to additional spokes I have created another alias called 'PrivateNetworks' which contains the RFC 1918 address ranges:

![](https://bluesun2017.files.wordpress.com/2022/02/opnsense5.jpg?w=1024)

Add the required rule to allow access to the internet:

Firewall - > Rules - > LAN

Add a rule

![](https://bluesun2017.files.wordpress.com/2022/02/opnsense6.jpg?w=1024)

The rule sets the interface as LAN and the source as our spoke1 alias and the destination as the PrivateNetworks alias but we select the tick box to invert the destination so we effectively say allow access to anything but that destination.

![](https://bluesun2017.files.wordpress.com/2022/02/opnsense7.jpg?w=1024)

At this point a virtual machine in the 192.168.20.128/28 subnet can access the internet

![](https://bluesun2017.files.wordpress.com/2022/02/opnsense8.jpg?w=1024)

## Summary

To sum up what we have done here:

Deployed a pair of highly available OPNsense firewalls in Azure

Used a hub and spoke networking model

Added aliases to the firewalls for a subnet in one of the spoke virtual networks

Added an alias for all RFC1918 addresses to use in as a inverse match in our firewall rule

Added a rule which allows access to the internet from a subnet on one of the spoke networks
