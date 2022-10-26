---
title: "Querying IIS SMTP Smarthost Settings"
date: "2018-03-07"
categories: 
  - "powershell"
tags: 
  - "iis"
---

Had a request to throw something together to query multiple machines and find the smarthost server that IIS was configured to use.  All I had time for was 'quick and dirty' so this is what I came up with;

 

get-adcomputer -filter \* | Select-Object dnshostname >c:\\servers.txt Get-WmiObject -Namespace "root\\MicrosoftIISv2" -Class "IISSMTPServerSetting" -Filter "Name ='SmtpSvc/1'" -comp (Get-Content c:\\servers.txt) | Select-Object smarthost,defaultdomain | export-csv c:\\servers.csv -NoTypeInformation

Obvious problem with the above is there is no connectivity check in place so any servers not being enabled for PS Remoting or firewalls in the way will just generate an error

 

 

<table class="highlight tab-size js-file-line-container"><tbody><tr><td id="LC1" class="blob-code blob-code-inner js-file-line"></td></tr></tbody></table>
