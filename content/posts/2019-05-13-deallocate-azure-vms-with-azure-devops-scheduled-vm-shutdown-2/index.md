---
title: "Deallocate Azure VMs with Azure Devops - Scheduled VM shutdown."
date: "2019-05-13"
categories: 
  - "azure"
  - "cloud"
  - "devops"
  - "powershell"
---

Having been inspired by Gregor Suttie's post '[Replacing Azure Automation using Azure CLI and Azure Devops](https://gregorsuttie.com/2019/05/08/replacing-azure-automation-using-azure-cli-and-azure-devops/)' I decided to do something similar with Powershell. My requirement was to power off VMs with a certain tag and value at a certain time.

The PowerShell for this is fairly simple:

_Get-AzVM | Where-Object {$\_.tags\['shutDown'\] -eq "19:00"} | Stop-AZVM -Force_

I then created build pipeline in Azure Devops and added an 'Azure PowerShell (Preview)' task to the pipeline. This pipeline is scheduled to run every day at 19:00.

Expanding on this I could easily create a power on version of the pipeline as well.
