---
title: "Get Windows boot time - Powershell"
date: "2016-02-15"
categories: 
  - "powershell"
  - "windows"
tags: 
  - "boot-time"
  - "last-boot-time"
---

To get the last Windows boot time using PowerShell V3 or later.

Get-CimInstance Win32\_OperatingSystem | select lastbootuptime,csname

This will show you the lastbootuptime and computer name
