---
title: "vNet Peering PowerShell"
date: "2018-07-31"
categories: 
  - "arm"
  - "azure"
  - "cloud"
  - "powershell"
tags: 
  - "peering"
  - "vnet"
---

We have a [hub and spoke design](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/hybrid-networking/shared-services) in Azure for our vNets and needed to peer the vNets together.

This can be done in an ARM template and we could have deployed all three networks in one go and peered them as part of the ARM template deployment.  For various reasons that approach didn't really work for us, 1 reason was the customer is very sensitive to change control and having all 3 vNets being controlled by one ARM deployment didn't sit very well with them and made them nervous.

So PowerShell was the obvious answer, and it's very simple in PowerShell anyway.  So we deploy all 3 vNets using separate ARM templates and then peer them together with powershell.  Code also over on [github](https://github.com/pagyP/PowerShell/blob/master/vNet%20Peering/Peering.ps1)

#Peering for hub and spoke network design
#Variables Section
$hubVnetResourceGroup = "RG\_hubVnet"
$hubVnetName = "hubprodVnet"
$spoke1VnetResourceGroup = "RG\_spoke1vnet"
$spoke2VnetResourceGroup = "RG\_spoke2Vnet"
$spoke1VnetName = "spoke1prodVnet"
$spoke2VnetName = "spoke2prodVnet"
$hubVnet = Get-AzureRmVirtualNetwork -Name $hubVnetName -ResourceGroupName $hubVnetResourceGroup 
$spoke1Vnet = Get-AzureRmVirtualNetwork -Name $spoke1VnetName -ResourceGroupName $spoke1VnetResourceGroup
$spoke2Vnet = Get-AzureRmVirtualNetwork -Name $spoke2VnetName -ResourceGroupName $spoke2VnetResourceGroup
#End Variables

#Add Hub to spoke1 peer and allow gateway transit through hub1
Add-AzureRmVirtualNetworkPeering -Name 'hubtospoke1peer' -VirtualNetwork $hubvnet -RemoteVirtualNetworkId $spoke1vnet.id -AllowForwardedTraffic  -AllowGatewayTransit 

#Add spoke 1 to hub and use hub 1 gateways
Add-AzureRmVirtualNetworkPeering -Name 'spoke1tohubpeer' -VirtualNetwork $spoke1vnet -RemoteVirtualNetworkId $hubVnet.id -AllowForwardedTraffic  -UseRemoteGateways 

#Add hub to spoke2 peer and allow gateway transit through hub
Add-AzureRmVirtualNetworkPeering -Name 'hubtospoke2peer' -VirtualNetwork $hubvnet -RemoteVirtualNetworkId $spoke2vnet.id -AllowForwardedTraffic  -AllowGatewayTransit

#Add spoke 2 to hub and use hub 1 gateways
Add-AzureRmVirtualNetworkPeering -Name 'spoke2tohubpeer' -VirtualNetwork $spoke2vnet -RemoteVirtualNetworkId $hubVnet.id -AllowForwardedTraffic  -UseRemoteGateways
