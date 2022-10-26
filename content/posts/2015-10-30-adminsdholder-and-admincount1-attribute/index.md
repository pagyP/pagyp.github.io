---
title: "AdminSDHolder and admincount=1 attribute"
date: "2015-10-30"
categories: 
  - "activedirectory"
  - "powershell"
tags: 
  - "adminsdholder"
  - "sdprop"
---

Certain groups within Active Directory are considered protected groups and are protected by AdminSDHolder.  When a user becomes a member of a protected group it will no longer inherit permissions from its parent object in AD (usually an OU).  This can mess up any carefully laid permission delegations you may have configured.  Much more on AdminSDHolder [here](http://windowsitpro.com/active-directory/advanced-active-directory-security)

As an AD admin you may find that if you have been delegated permissions to , say, reset passwords of all users in OU you could come across a user who's password you can't reset.  You'll basically be told you do not have permissions on that object.  If that user account was once in a protected group they will not be inheriting the permissions from the parent OU.

To identify users that have the attribute admincount set to 1

$count = Get-Aduser -searchbase "OU=ExampleOU,DC=Example,DC-Com" -Properties adminCount -Filter \* | where {admincount -gt 0}

To then set that attribute back to 0

$count | Select SamAccountName,distinguishedName,adminCount | ForEach-Object {Set-Aduser -Identity $\_.SamAccountName -Replace @{adminCount=0}}

 

Once that is done then inheritance needs to be re-enabled on the object; the below code from [here](http://enterpriseit.co/microsoft-active-directory/enable-inheritance-ad-user-accounts/)

 

$users = Get-ADUser -ldapfilter “(objectclass=user)” -searchbase “OU=companyusers,dc=enterpriseit,dc=co” ForEach($user in $users) { # Binding the users to DS $ou = \[ADSI\](“LDAP://” + $user) $sec = $ou.psbase.objectSecurity

if ($sec.get\_AreAccessRulesProtected()) { $isProtected = $false ## allows inheritance $preserveInheritance = $true ## preserver inhreited rules $sec.SetAccessRuleProtection($isProtected, $preserveInheritance) $ou.psbase.commitchanges() Write-Host “$user is now inherting permissions”; } else { Write-Host “$User Inheritable Permission already set” } }
