---
title: "When life gets confusing - check your AzureRM module version"
date: "2019-01-14"
categories: 
  - "azure"
  - "powershell"
tags: 
  - "azurerm"
  - "diskencryption"
---

I had to enable disk encryption on some existing Azure VMs this morning but I kept hitting a snag even though I had done this before and was using the same powershell as before. Here's the PowerShell:

$rgName = 'MySecureRg';  
$vmName = 'MySecureVM';  
$KeyVaultName = 'MySecureVault';  
$KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;  
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;  
$KeyVaultResourceId = $KeyVault.ResourceId;

Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;

Now when I ran Set-AzureRMVMDiskEncryptionExtension it kept prompting me for an AADClient. Weird I thought it didn't ask for that the last time I had to encrypt some VMs. Then it struck me, the last time I did this I ran the powershell from another machine, so there must be something different between the machine I'm using today and the one I used previously. And indeed there was something different and this article highlighted it to me [https://docs.microsoft.com/en-us/azure/security/azure-security-disk-encryption-prerequisites.](https://docs.microsoft.com/en-us/azure/security/azure-security-disk-encryption-prerequisites) 

From the article:

Verify the installed versions of the AzureRM module. If needed, [update the Azure PowerShell module](https://docs.microsoft.com/en-us/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-6.13.0#update-the-azure-powershell-module).

- The AzureRM module version needs to be 6.0.0 or higher.

6.0.0 or higher. I check the version on the machine I'm using today:

PS C:\\WINDOWS\\system32> Get-Module AzureRM -ListAvailable | Select-Object -Property Name,Version,Path

Name Version Path  
\---- ------- ---- 
AzureRM 5.7.0 C:\\Program Files\\WindowsPowerShell\\Modules\\AzureRM\\5.7.0\\AzureRM.psd1

Updated the AzureRM Module to the latest version and the Set-AzureRMDiskEncryptionExtension cmdlet then worked as it did for me previously.
