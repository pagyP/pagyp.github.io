---
title: "Can Yoo Mix and Match Topologies in Azure Virtual WAN?"
date: "202-01-22"
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
The current topology is shown below.  The customer has a single virtual WAN hub in West Europe and a number of virtual networks in West Europe.  The customer is using a tiered vnet model in West Europe and is using the old topology (https://docs.microsoft.com/en-us/azure/virtual-wan/virtual-wan-about#topologies) to connect the virtual networks to the virtual WAN hub.  The customer is using Palo Alto Firewalls in the West Europe 'NVA vnet'

(./images/vwan-01.png?w=1024)



### The Microsoft Network:
The Microsoft network is ASN 8075<sup>1</sup> and as such Microsoft owned IP addresses are routed within AS8075

Let's first take a look at what the Microsoft documentation has to say:

### Microsoft Documentation:
- "IP traffic stays entirely within our global network and never enters the public Internet"<sup>2</sup> 

- "Azure traffic between our datacenters stays on our network and does not flow over the Internet. This includes all traffic between Microsoft services anywhere in the world. For example, within Azure, traffic between virtual machines, storage, and SQL communication traverses only the Microsoft network, regardless of the source and destination region. Intra-region VNet-to-VNet traffic, as well as cross-region VNet-to-VNet traffic, stays on the Microsoft network."<sup>3</sup> 

- "..any traffic between data centers, within Microsoft Azure or between Microsoft services such as Virtual Machines, Microsoft 365, XBox, SQL DBs, Storage, and virtual networks routes within our global network and never over the public Internet. This routing ensures optimal performance and integrity."<sup>4</sup>
  
The above seem pretty comprehensive and clear, but let's do some investigation to see if this is actually the case.

### Investigation:
#### Example 1 - Azure to Azure services in the same region and across regions

- In this example we have a virtual machine in UK South and a storage accounts in UK South East US.  Everything is in the same subscription and the same resource group.  The virtual machine is in a VNET with a private IP address of 10.10.10.10 and it does not have a public IP associated to it.  It is using the Azure default outbound access<sup>5</sup>
- The storage account is using the default settings and is not using Private Link/Endpoint

Resources deployed:
![A picture showing the Azure resources being used.  Includes Virtual Machine and two storage accounts, one in UK South and one in East US](./images/resources.png?w=1024)

The VM does not have a public IP attached to it and uses the default internet access for virtual machines:
![A picture of the IP address the machine is using to connect to to the internet, using default VM outbound access](./images/vmip.png?w=1024)

The UK South storage account is called labquqcdg4e and the blob endpoint is https://labquqcdg4e.blob.core.windows.net/
And if we do an nslookup on the storage account endpoint we get the following:
![A picture showing the IP address that the UK South storage account resolves to](./images/ukstorageaccountip.png?w=1024)

The East US storage account is called labqw68xesk and the blob endpoint is https://labqw68xesk.blob.core.windows.net/
And if we do an nslookup on the storage account endpoint we get the following:
![A picture showing the IP address that the East US storage account resolves to](./images/eusstorageaccountip.png?w=1024)


So, we have a virtual machine using default VM internet access and storage accounts in UK South and East US using default settings.  
- The VM is being identified on the internet as using IP address 51.132.235.13
- The storage account labquqcdg4e (UK South) is being resolved to IP address 20.150.40.4
- The storage account labqw68xesk (East US) is being resolved to IP address 52.239.171.228


If we now use ipinfo.io to see what AS and what company those IP address belong to we get the following:

![A picture which shows the IP addressed being used by the virtual machine to access the internet is owned by Microsoft and is part of AS8075](./images/vmipasn.png?w=1024)

![A picture which shows the IP addressed being used by the UK South storage account is owned by Microsoft and is part of AS8075](./images/storageaccountuksipasn.png?w=1024)

![A picture which shows the IP addressed being used by the East US storage account is owned by Microsoft and is part of AS8075](./images/storageaccounteusipasn.png?w=1024)

So, we can see that the VM is being identified as being in AS8075 (Microsoft) and the storage accounts are being identified as being in AS8075 (Microsoft).  So, the traffic will be routed on the Microsoft Network, it will stay on the Microsoft network and not, any any point, traverse the public internet.

What if I add a public IP to the VM and use that to access the storage accounts?  Will the traffic still stay on the Microsoft network?
![A picture which shows the virtual machine has had a public IP resource added to it and the IP address of that public IP](./images/vmpublicip.png?w=1024)

I've added a public IP address to my VM, that IP address is 20.50.112.127.  Let's look that IP address up on ipinfo.io and see what AS it belongs to:
![A picture that shows the virtual machine public IP resource is part of AS8075 and owned by Microsoft](./images/vmpublicipasn.png?w=1024)

Once again the IP address is being identified as being in AS8075 (Microsoft).



#### Example 2 - Azure to Microsoft 365 services
What about the mention earlier about Microsoft 365?  If I'm on an Azure VM do I still stay on the Microsoft network if I access those services?  Let's find out.
![A picture that shows the IP addresses that outlook.office.com resolves to from our virtual machine in Azure](./images/nslookupoutlook_office_com.png?w=1024)
We can see that outlook.office.com is being resolved to:
- 40.99.150.162
![A picture that shows IP address 40.99.150.162 is part of As8075 and is owned by Microsoft](./images/outlook_office1.png?w=1024)
- 52.97.208.18
![A picture that shows IP address 52.97.208.18 is part of As8075 and is owned by Microsoft](./images/outlook_office2.png?w=1024)
- 40.99.151.146
![A picture that shows IP address 40.99.151.146 is part of As8075 and is owned by Microsoft](./images/outlook_office3.png?w=1024)
- 52.97.133.146
![A picture that shows IP address 52.97.133.146 is part of As8075 and is owned by Microsoft](./images/outlook_office4.png?w=1024)

If we look up those IP addresses on ipinfo.io we can see that they are all in AS8075 (Microsoft)

If you have a VDI deployment hosted in Azure and users on that VDI service accessing services from Microsoft 365, such as Outlook, then that traffic will stay on the Microsoft network.

Would Microsoft route traffic out of their network, onto the public internet and back into the Microsoft network again?  No is the short answer to that question, that would be inefficient, would add latency and would be a security risk.

#### Conclusion
- Azure to Azure traffic stays on the Microsoft network
- Azure to Microsoft 365 traffic stays on the Microsoft network

#### Final Question
- Can the default behaviour of traffic staying on the Microsoft be changed? Yes it can, for some services.  Refer to https://learn.microsoft.com/en-us/azure/virtual-network/ip-services/routing-preference-overview for more information.


#### References

<sup>1</sup>. https://bgp.he.net/AS8075#_prefixes , https://learn.microsoft.com/en-us/azure/internet-peering/policy

<sup>2</sup>. https://azure.microsoft.com/en-us/explore/global-infrastructure/global-network/#documentation

<sup>3</sup>. https://azure.microsoft.com/en-us/blog/how-microsoft-builds-its-fast-and-reliable-global-network/

<sup>4</sup>. https://learn.microsoft.com/en-gb/azure/networking/Microsoft-global-network#get-the-premium-cloud-networking-experience

<sup>5</sup>. https://learn.microsoft.com/en-us/azure/virtual-network/ip-services/default-outbound-access

## Additional Information: 
https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/azure-best-practices/connectivity-to-azure-paas-services

https://learn.microsoft.com/en-us/azure/virtual-network/ip-services/routing-preference-overview

https://www.cloudflare.com/en-gb/learning/network-layer/what-is-an-autonomous-system/
