---
title: "Firewall options for the cost conscious Azure lab owner"
date: "2021-11-13"
categories: 
  - "azure"
  - "cloud"
  - "networking"
  - "security"
tags: 
  - "firewall"
  - "lab"
---

**Introduction**

For most businesses using some kind of firewall in Azure is not optional , these firewalls could be a cloud native offering such as Azure Firewall or a Network Virtual Appliance (NVA) running on IaaS such as Palo Alto, Checkpoint, Fortigate etc etc. A popular and common networking model in Azure that utilises some form of firewall is the [hub and spoke network](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?tabs=cli) design which uses a centralised firewall or maybe even multiple centralised firewalls behind a load balancer for high availability . From a business perspective these firewall solutions are a necessity and cost effective, but what about the cost conscious Azure lab owner who, for example, wants to run a hub and spoke network model with a centralised firewall/s? Although I said 'cost effective' from a business perspective that does not necessarily translate to cheap or cost effective for the Azure lab owner. Generally speaking the virtual machine size requirements for third party NVA Firewalls in Azure would be cost prohibitive for the Azure lab owner.

Here I'll briefly cover some of the options for the cost conscious Azure lab owner.

**Azure Firewall:**

Azure Firewall is a cloud native firewall [https://docs.microsoft.com/en-us/azure/firewall/overview](https://docs.microsoft.com/en-us/azure/firewall/overview) and therefore removes any need for you to manage IaaS VMs for your firewalling. It comes in two flavours: Standard and Premium. Azure Firewall is not necessarily cheap as such (for the Azure lab owner) [https://azure.microsoft.com/en-gb/pricing/details/azure-firewall/](https://azure.microsoft.com/en-gb/pricing/details/azure-firewall/) but everything about Azure Firewall (the firewall itself, IP groups, rules etc) can be codified using your Infrastructure as Code (IaC) tool of choice such as ARM templates,Azure Bicep or Terraform. Utilising IaC you can tear down and redeploy your Azure firewall as and when you need to. Alternatively you can stop and start Azure Firewall to save on cost [https://docs.microsoft.com/en-us/azure/firewall/firewall-faq#how-can-i-stop-and-start-azure-firewall](https://docs.microsoft.com/en-us/azure/firewall/firewall-faq#how-can-i-stop-and-start-azure-firewall) .

**OPNSense**:

Check out this great repository for how to deploy OPNsense on Azure [https://github.com/dmauser/opnazure](https://github.com/dmauser/opnazure) . You have a choice of options from a single VM to an active active pair of OPNSense firewalls. By default this deploys the VMs as Standard\_B2s sizes which should be fairly cost effective for the Azure lab owner and of course the VMs could be shutdown when they are not required [https://azure.microsoft.com/en-us/updates/set-auto-shutdown-within-a-couple-of-clicks-for-vms-using-azure-resource-manager/](https://azure.microsoft.com/en-us/updates/set-auto-shutdown-within-a-couple-of-clicks-for-vms-using-azure-resource-manager/)

**Linux with iptables**:

Another option would be to use your preferred (and supported in Azure [https://docs.microsoft.com/en-us/azure/virtual-machines/linux/endorsed-distros](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/endorsed-distros) ) Linux distro in Azure and run iptables on it. This would allow for some basic packet filtering and/or routing which may suit some requirements.

Final thoughts

There are likely more options than the above and even those I have only covered briefly in this post. I'll aim to do further posts that go into more detail on the technicalities, and maybe costings, of the above options in the future.
