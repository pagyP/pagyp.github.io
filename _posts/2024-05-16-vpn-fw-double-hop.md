---
title: "Double Hop with Azure Firewall and Statically routed site to site VPN"
date: "2024-05-16"
featured_image: ""
author: "Paul Paginton"
categories: 
  - "blog"
  - "networking"
  - "azure"
tags: 
  - "blog"
  - "azure"
  - "networking"
  - "microsoft"
  - "microsoft network"
---

### Background:

A customer asked if it was possible to use what they called a 'bridge-hub' virtual network (this virtual network would only contain an Azure firewall), and have spokes virtual networks connect to that 'bridge-hub' and then use a VPN gateway hosted in another virtual network, which in turn connected to on-prem.  The goal being to get the virtual network not peered to the virtual network containing the VPN gateway to be able to route to/from on-prem.  The virtual network hosting the VPN gateway also hosted an Azure firewall and company policy mandated that traffic pass both the firewall in the 'bridge-hub' and the firewall in the virtual network hosting the VPN gateway.

### Topology:

#### Current Topology:
The existing topology looked like:

![A picture showing the existing Azure virtual network topology, shows a hub vnet with a VPN gateway and firewall and a 'bridge-hub' virtual which as spoke virtual network peered to it](/assets/images/s2s-double-fw-hop-init.drawio.png?w=1024)

##### First thoughts on this:
Let's address the question of why the customer wanted to do this:

- They originally were cloud only and the 'bridge-hub' was handling routing between spoke virtual networks.
  
- At a later date another virtual network was added to host a VPN gateway and Azure firewall and this was connected to on-prem via a site to site VPN.
  
- The newer virtual network with the VPN gateway was managed by a different team than had originally setup the existing virtual networks.

- Some internal politics meant that the team managing the newer virtual network with the VPN gateway were not allowed to make changes to the existing virtual networks, so they could not just peer the spokes connected to the 'bridge-hub' to the virtual network with the VPN gateway.



### Summary:
This can be made to work with appropiate route tables in Azure (static routes) and the correct routing configuration on the on-prem router/firewall.  

![A picture showing the existing Azure virtual network topology, shows a hub vnet with a VPN gateway and firewall and a 'bridge-hub' virtual which as spoke virtual network peered to it. Same as the previous picture, but this time includes details on the route table configuration needed to make this work](/assets/images/s2s-double-fw-hop.drawio.png?w=1024)

### Conclusion:

Although this can be made to work it comes with some caveats:

- This can be operationally intense, the routing is all static so any new address spaces used in Azure will need route table updates, which will be manual.