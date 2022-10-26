---
title: "VM Deployment with ARM Template"
date: "2018-07-17"
categories: 
  - "azure"
  - "cloud"
  - "json"
tags: 
  - "arm"
---

This ARM template will deploy x number of virtual machines (takes the number you need as a parameter), a storage account in the target resource group for boot diagnostics, deploys the VMs in an availability group and deploys x number of data disks per VM.  Both the number of data disks (per VM) and size are parameters.  It attaches the VMs to an existing vnet/subnet, both of which are parameters.  it also sets the Locale to UK on Windows servers using a custom script extension as per [https://www.lewisroberts.com/2017/03/01/set-language-culture-timezone-using-powershell/](https://www.lewisroberts.com/2017/03/01/set-language-culture-timezone-using-powershell/)

Template over on my Github page [https://github.com/pagyP/AzARM/tree/master/VMs](https://github.com/pagyP/AzARM/tree/master/VMs)

This template will be updated over time to include additional functionality.
