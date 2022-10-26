---
title: "Domain Controller promotion stops responding"
date: "2016-07-07"
categories: 
  - "activedirectory"
tags: 
  - "dcpromo"
  - "domain"
---

Whilst promoting a Windows 2012R2 server to a domain controller it got as far as 'Replicating the schema directory partition' and then nothing else happened.

Now, this server has NetBios over TCPIP disabled which was causing the above problem.  The quick answer to this is to use the long version of the username when entering the credentials for the domain controller promotion i.e. domainname.comadministrator and not domainadministrator

 

More info here [https://support.microsoft.com/en-us/kb/2948052](https://support.microsoft.com/en-us/kb/2948052)
