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

### Question:

When does network traffic stay on the Microsoft network?  Does Azure to Azure traffic stay on the Microsoft network?  What about Azure to other Microsoft services, such as Microsoft 365?

For a video discussion of the same topic see https://www.youtube.com/watch?v=ssrAPwOKw4g 

### Background:
There seems to be some confusion as to when, and when not, traffic originating in Azure stays within the Azure backbone (from now on I'll refer to this as the Microsoft Network) network.  This is an important question and understanding this correctly is equally important.  I see many blogs/posts/articles that all seem to suggest that there are times when traffic originating from within Azure and destined for other Microsoft services will transit via the public internet and not stay on the Microsoft network. It's also a common form of mis-understanding in many conversations I have with customers. 


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
![](/content/posts/2023-18-10-when-does-network-traffic-stay-on-the-microsoft-network/images/resources.png?w=1024)

The VM does not have a public IP attached to it and uses the default internet access for virtual machines:
![](/content/posts/2023-18-10-when-does-network-traffic-stay-on-the-microsoft-network/images/vmip.png?w=1024)

The UK South storage account is called labquqcdg4e and the blob endpoint is https://labquqcdg4e.blob.core.windows.net/
And if we do an nslookup on the storage account endpoint we get the following:
![](/content/posts/2023-18-10-when-does-network-traffic-stay-on-the-microsoft-network/images/ukstorageaccountip.png?w=1024)

The East US storage account is called labqw68xesk and the blob endpoint is https://labqw68xesk.blob.core.windows.net/
And if we do an nslookup on the storage account endpoint we get the following:
![](/content/posts/2023-18-10-when-does-network-traffic-stay-on-the-microsoft-network/images/eusstorageaccountip.png?w=1024)


So, we have a virtual machine using default VM internet access and storage accounts in UK South and East US using default settings.  
- The VM is being identified on the internet as using IP address 51.132.235.13
- The storage account labquqcdg4e (UK South) is being resolved to IP address 20.150.40.4
- The storage account labqw68xesk (East US) is being resolved to IP address 52.239.171.228


If we now use ipinfo.io to see what AS and what company those IP address belong to we get the following:

![](/content/posts/2023-18-10-when-does-network-traffic-stay-on-the-microsoft-network/images/vmipasn.png?w=1024)

![](/content/posts/2023-18-10-when-does-network-traffic-stay-on-the-microsoft-network/images/storageaccountuksipasn.png?w=1024)

![](/content/posts/2023-18-10-when-does-network-traffic-stay-on-the-microsoft-network/images/storageaccounteusipasn.png?w=1024)

So, we can see that the VM is being identified as being in AS8075 (Microsoft) and the storage accounts are being identified as being in AS8075 (Microsoft).  So, the traffic will be routed on the Microsoft Network, it will stay on the Microsoft network and not, any any point, traverse the public internet.

What if I add a public IP to the VM and use that to access the storage accounts?  Will the traffic still stay on the Microsoft network?
![](/content/posts/2023-18-10-when-does-network-traffic-stay-on-the-microsoft-network/images/vmpublicip.png?w=1024)

I've added a public IP address to my VM, that IP address is 20.50.112.127.  Let's look that IP address up on ipinfo.io and see what AS it belongs to:
![](/content/posts/2023-18-10-when-does-network-traffic-stay-on-the-microsoft-network/images/vmpublicipasn.png?w=1024)

Once again the IP address is being identified as being in AS8075 (Microsoft).



#### Example 2 - Azure to Microsoft 365 services
What about the mention earlier about Microsoft 365?  If I'm on an Azure VM do I still stay on the Microsoft network if I access those services?  Let's find out.
![](/content/posts/2023-18-10-when-does-network-traffic-stay-on-the-microsoft-network/images/nslookupoutlook_office_com.png?w=1024)
We can see that outlook.office.com is being resolved to:
- 40.99.150.162
![](/content/posts/2023-18-10-when-does-network-traffic-stay-on-the-microsoft-network/images/outlook_office1.png?w=1024)
- 52.97.208.18
![](/content/posts/2023-18-10-when-does-network-traffic-stay-on-the-microsoft-network/images/outlook_office2.png?w=1024)
- 40.99.151.146
![](/content/posts/2023-18-10-when-does-network-traffic-stay-on-the-microsoft-network/images/outlook_office3.png?w=1024)
- 52.97.133.146
![](/content/posts/2023-18-10-when-does-network-traffic-stay-on-the-microsoft-network/images/outlook_office4.png?w=1024)

If we look up those IP addresses on ipinfo.io we can see that they are all in AS8075 (Microsoft)

If you have a VDI deployment hosted in Azure and users on that VDI service accessing services from Microsoft 365, such as Outlook, then that traffic will stay on the Microsoft network.

Would Microsoft out of their network, onto the public internet and back into the Microsoft network again?  No is the short answer to that question, that would be inefficient, would add latency and would be a security risk.

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
