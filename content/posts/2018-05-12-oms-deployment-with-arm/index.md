---
title: "OMS Deployment with ARM Template"
date: "2018-05-12"
categories: 
  - "azure"
  - "cloud"
  - "json"
tags: 
  - "arm"
  - "oms"
---

An ARM template to deploy a basic OMS workspace in Azure.  It deploys a workspace and two OMS solutions: Anti-Malware assessment and Updates.

Another template for OMS deployment, which is identical to the above, but links the workspace to an existing automation account.  Note it does not create the automation account, the automation account must already exist.

These templates are over on my Github [page](https://github.com/pagyP/AzARM/tree/master/OMS)
