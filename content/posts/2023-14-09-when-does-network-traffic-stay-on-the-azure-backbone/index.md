---
title: "When does network traffic stay on the Azure backbone network? "
date: "2023-09-14"
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
---

## Problem Statement:

When does network traffic stay on the Azure backbone network?  Under what circumstances would network traffic stay on the Azure backbone network?  What are the implications of this?  When would network traffic not stay on the Azure backbone network?  What are the implications of this?

## Background:
There seems to be a lot of confusion around as to when, and when not, traffic originating in Azure stays within the Azure backbone (from now on I'll refer to this as the Microsoft Network) network.  This is an important question and understanding this correctly is equally important.  I see many blogs/posts/articles that all seem to suggest that there are times when traffic originating from within Azure will transit via the public internet and not stay on the Microsoft network.
Indeed some of Microsoft's own documentation sometimes seems to suggest this as well.

# The Microsoft Network:
The Microsoft network is ASN 8075<sup>1</sup> and as such Microsoft owned IP addresses are routed within AS8075

Let's first take a look at what the Microsoft documentation has to say:

## Microsoft Documentation:
- "IP traffic stays entirely within our global network and never enters the public Internet"<sup>2</sup> 

- "Azure traffic between our datacenters stays on our network and does not flow over the Internet. This includes all traffic between Microsoft services anywhere in the world. For example, within Azure, traffic between virtual machines, storage, and SQL communication traverses only the Microsoft network, regardless of the source and destination region. Intra-region VNet-to-VNet traffic, as well as cross-region VNet-to-VNet traffic, stays on the Microsoft network."<sup>3</sup> 
  
The above seem pretty comprehensive and clear.  However, there are other Microsoft documents that seem to suggest that traffic originating from within Azure may not stay on the Microsoft network.  For example:

- "Traffic between your virtual network and the service travels the Microsoft backbone network. Exposing your service to the public internet is no longer necessary" - https://learn.microsoft.com/en-us/azure/private-link/private-link-overview 
  
The above is sometimes interpreted as saying that using Private Link/Private Endpoints is a way to ensure your traffic stays off of the public internet, but that's not really what it's saying.  We'll come back to this later.

#### Let's look at some examples
## Example 1:

- Here we have a virtual machine in UK South and a storage account in East US.  Everything is in the same subscription and the same resource group.  The virtual machine is in a VNET with a private IP address of 10.10.10.10 and it does not have a public IP associated to it.  It is using the Azure default outbound access<sup>4</sup>
- The storage account is using the default settings and is not using Private Link/Endpoint




<sup>1</sup>. https://bgp.he.net/AS8075#_prefixes , https://learn.microsoft.com/en-us/azure/internet-peering/policy

<sup>2</sup>. https://azure.microsoft.com/en-us/explore/global-infrastructure/global-network/#documentation

<sup>3</sup>. https://azure.microsoft.com/en-us/blog/how-microsoft-builds-its-fast-and-reliable-global-network/

<sup>4</sup>. https://learn.microsoft.com/en-us/azure/virtual-network/ip-services/default-outbound-access

additional sources: 
https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/azure-best-practices/connectivity-to-azure-paas-services

https://learn.microsoft.com/en-us/azure/virtual-network/ip-services/routing-preference-overview
