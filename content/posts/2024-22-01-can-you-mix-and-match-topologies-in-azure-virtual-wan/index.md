---
title: "Can Yoo Mix and Match Topologies in Azure Virtual WAN?"
date: "2024-01-22"
featured_image: ""
author: "Paul Paginton"
categories: 
  - "blog"
  - "networking"
  - "azure"
  - "virtual wan"
tags: 
  - "blog"
  - "azure"
  - "networking"
  - "microsoft"
  - "microsoft network"
  - "azure virtual wan"
---

### Background:

A colleague asked me an interesting question about an Azure Virtual WAN scenario their customer was facing.  The customer is using Azure Virtual WAN with a single virtual WAN hub, which was placed in the West Europe region, and this hub had been deployed before Azure Virtual WAN routing intent was available (https://docs.microsoft.com/en-us/azure/virtual-wan/virtual-wan-routing-preference-overview).  The customer had deployed a number of virtual networks in West Europe and was using a tiered vnet model in West Europe https://learn.microsoft.com/en-us/azure/virtual-wan/scenario-route-through-nva.  The customer now wanted to deploy a new Virtual WAN hub in the UK South region, place an Azure Firewall in the UK South vwan hub and enable routing intent on the UK South, whilst leaving the configuration of the West Europe as is.  Some of the questions the customer had were:

- The customer wanted to know if they could use the new Azure Virtual WAN routing intent to route traffic from the UK South virtual networks to the West Europe virtual WAN hub and to the West Europe tiered virtual networks. 
  
- The customer wanted to know if they could have some virtual networks connected to the virtual WAN hub using the old topology and   some virtual networks connected to the virtual WAN hub using the new topology.
  
- The customer wanted to know if they could mix and match topologies in Azure Virtual WAN is a supported configuration.



### Topology:

#### Current Topology:
The current topology is shown below.  The customer has a single virtual WAN hub in West Europe and a number of virtual networks in West Europe.  The customer is using a tiered vnet model in West Europe (https://docs.microsoft.com/en-us/azure/virtual-wan/virtual-wan-about#topologies) to connect the virtual networks to an 'NVA vnet' which in turn is linked to the West Europe virtual WAN hub.  The customer is using Palo Alto Firewalls in the West Europe 'NVA vnet'

![A picture showing the existing Azure Virtual WAN topology, a virtual wan with a single hub in West Europe](./images/vwan-01.png?w=1024)

#### Proposed Topology:
The proposed topology is shown below.  The customer wants to deploy a new virtual WAN hub in UK South and place an Azure Firewall in the UK South virtual WAN hub.  The customer wants to use the new Azure Virtual WAN routing intent to route private traffic to UK South hosted spoke virtual networks and to the West Europe tiered virtual networks and internet egress via the Azure Firewall in the UK South virtual WAN Hub.  The customer wants to leave the West Europe virtual WAN hub and virtual networks as is, with no routing intent configured.

(./images/vwan-02.png?w=1024)


### Investigation:
#### Short Version:

- The customer can use the new Azure Virtual WAN routing intent in the UK South virtual WAN hub and continue to use the tiered vnet model in the West Europe region and traffic will route correctly across regions, i.e. vnet to vnet traffic across regions will route via the Palo Alto firewalls in the NVA vnet in West Europe and via the Azure Firewall in the UK South virtual WAN hub (dependant upon the necessary firewall rules allowing the traffic).  The customer can mix and match topologies in Azure Virtual WAN.
- The above is achieved using the BGP Peering feature in Azure Virtual WAN https://learn.microsoft.com/en-us/azure/virtual-wan/scenario-bgp-peering-hub. The BGP peering feature allows the customer to use the new Azure Virtual WAN routing intent in the UK South virtual WAN hub and continue to use the tiered vnet model in the West Europe region.  The BGP peering feature allows the customer to mix and match topologies in Azure Virtual WAN.
  

#### Final Toplogy:
  
(./images/vwan-03.png?w=1024)

- To re-create this I used an OpnSense firewall (customer is using Palo Alto) in West Europe and an Azure Firewall in UK South.  Notice the BGP peering between the NVA and the Virtual WAN Hub in West Europe.  

- After establishing the BGP peering the OpnSense firewall is then configured to advertise the spoke virtual network CIDR ranges to the virtual WAN hub.  Also note the Network Import check is disabled in the BGP configuration on the OpnSense firewall, this is to ensure we can advertise the manually added routes, which do not exist in the routing table.

#### Longer Version:

Actually, this will be very short.  Whilst investigating I discovered that this can be done with some very convoluted static routing.  I'm not going to go into the details of this as it's not a supported configuration and I don't want to encourage anyone to do this.  The supported way to achieve this is to use the BGP peering feature in Azure Virtual WAN https://learn.microsoft.com/en-us/azure/virtual-wan/scenario-bgp-peering-hub.  The BGP peering feature allows the customer to use the new Azure Virtual WAN routing intent in the UK South virtual WAN hub and continue to use the tiered vnet model in the West Europe region.  The BGP peering feature allows the customer to mix and match topologies in Azure Virtual WAN.

