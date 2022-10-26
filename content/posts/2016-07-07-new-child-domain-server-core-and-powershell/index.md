---
title: "New Child Domain - Server Core and PowerShell"
date: "2016-07-07"
categories: 
  - "activedirectory"
  - "powershell"
---

All of my domain controllers are now server core unless someone can give me a very good reason to install Windows with a GUI, so far no one has given me a good enough reason.

When deploying a new child domain this means we can now use some PowerShell goodness to create our new child domain.

Pre-reqs

Windows 2012R2 Server Core installed

IP address set on the box and preferred DNS server set to the IP address of a domain controller in the parent domain

Install-Windowsfeature AD-Domain-Services

Install-ADDSDomain -DomainType child -NewDomainName 'childdomname' -ParentDomainName 'parentdomname.com' -InstallDns -CreateDnsDelegation -NewDomainNetBiosName 'childdomname' -DomainMode win2012r2 -Credential (get-credential)

 

You will be prompted for the admin credentials of your parent domain and then for the safemodepassword that you want to set on this DC.
