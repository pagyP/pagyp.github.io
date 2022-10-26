---
title: "Mailbox Enable all users in an OU"
date: "2015-10-29"
categories: 
  - "powershell"
tags: 
  - "exchange-2010"
---

As well as having to Skype enable all users recently (see previous post) I also had to mailbox enable the users.  With Exchange 2010 you can't just import the powershell module.  Using [this](http://blogs.technet.com/b/heyscriptingguy/archive/2012/01/23/learn-how-to-use-powershell-to-run-exchange-server-commands-remotely.aspx) article as a base I came up with this;

$Session = New-Pssession -COnfigurationName Microsoft.Exchange -ConnectionUri http://exchangeservername/powershell

Import-Pssession $Session

Get-User -OrganizationalUnit "OU=ExampleOU,DC=Example,DC=Com" | Enable-Mailbox -database "your mailbox database"
