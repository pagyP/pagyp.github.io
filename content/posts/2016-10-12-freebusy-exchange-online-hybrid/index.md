---
title: "Free/Busy Exchange Online Hybrid"
date: "2016-10-12"
categories: 
  - "cloud"
  - "exchange"
  - "office365"
---

We have a hybrid Exchange setup with Exchange online.  On premises is Exchange 2016.

Users who had been migrated to Exchange online could only see availability of other users and resources that were still on premises.  A check of the organization relationship showed the following;

Run the below powershell on your on premises servers and look for the FreeBusyAccessLevel setting

Get-OrganizationRelationship | fl

FreeBusyAccessLevel   : AvailabilityOnly

 

This was why migrated users could only see availabilty of on premises users and not any more detail.  To change this use

Set-OrganizationRelationship -FreeBusyAccessLevel LimitedDetails
