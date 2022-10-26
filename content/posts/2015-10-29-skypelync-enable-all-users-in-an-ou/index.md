---
title: "Skype/Lync Enable all users in an OU"
date: "2015-10-29"
categories: 
  - "powershell"
tags: 
  - "lync"
  - "skype"
---

I recently had to enable all users in a specific OU for Skype for Business 2015.  Easily done with Powershell;

Either import the skypeforbusiness module into a normal Powershell window or run the Skype for Business Server Management Shell

import-module skypeforbusiness

Get-CsAduser -OU "OU=ExampleOU,DC=example,DC=com" | Enable CSuser -RegistrarPool "your pool name" -SipAddressType emailaddress
