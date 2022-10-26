---
title: "Set IP Address and DNS settings with PowerShell - Windows 2012 and newer"
date: "2015-10-05"
categories: 
  - "powershell"
---

get-netadapter

Name                      InterfaceDescription                    ifIndex Status ----                      --------------------                    ------- ------       ---------- Ethernet                  vmxnet3 Ethernet Adapter                     12 Up

Name is the interfacealias.

New-Netipaddress -interfacealias "ethernet" -ipaddress 192.168.0.10 -prefixlength 24 -defaultgateway 192.168.0.1

Could also use

new-netipaddress -interfaceIndex 12

 

set-dnsclientserveraddress -interfacealias "ethernet" -serveraddresses"192.168.0.10,192.168.0.11"
