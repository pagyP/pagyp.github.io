---
title: "Combining Terraform with Azure Devops"
date: "2019-05-03"
categories: 
  - "azure"
  - "cloud"
  - "devops"
  - "infrastructure-as-code"
  - "terraform"
---

Really short and straight to the point post. 😀

I use the '[Terraform Tasks for Azure DevOps](https://marketplace.visualstudio.com/items?itemName=charleszipp.azure-pipelines-tasks-terraform#overview)' extensions which makes using Terraform with Azure Devops an absolute breeze

I have a build pipleline which initiates the remote storage for the Terraform state file, perfoms the Terraform init and the Terraform plan, all relevant files are then published as an artifact.

A release pipeline then picks up the published artificat, performs another Terraform init and then a Terraform apply
