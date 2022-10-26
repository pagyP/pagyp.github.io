---
title: "Restore Computer Object with AD Recycle Bin"
date: "2016-01-04"
categories: 
  - "activedirectory"
  - "powershell"
---

Over the Xmas period it would seem that someone deleted a computer account from AD.  This meant that the user of that PC could not log in using that PC.  This is a Windows 2008R2 forest so to restore the computer object;

 

Get-Adobject -filter {samaccountname -eq "pcname$"} -IncludeDeletedObjects | Restore-Adobject

 

The $ on the pcname is important - computer objects in AD always have $ at the end of the name as part of the samaccountname attribute.
