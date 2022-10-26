---
title: "Using Snyk with Terraform Cloud Run Tasks"
date: "2022-06-21"
categories: 
  - "devops"
  - "infrastructure-as-code"
tags: 
  - "snyk"
  - "terraform"
  - "terraform-cloud"
---

What is [Snyk](https://snyk.io/)?

_Find and automatically fix vulnerabilities in your code, open source dependencies, containers, and infrastructure as code — all powered by Snyk’s industry-leading security intelligence._

What are Terraform cloud [Run Tasks](https://www.terraform.io/cloud-docs/workspaces/settings/run-tasks)?

_Run Tasks allow you to directly integrate third-party tools and services at certain stages in the Terraform Cloud run lifecycle_

I am using Synk to scan my terraform code during the plan phase to catch potential problems with my code.

Setting up the integration of Snyk into Terraform cloud is very easy to do:

- First you need an account on Snyk.io, I have chosen to use a free account which gets me 300 IaC scans per month.
- Then you get an endpoint URL from you Synk account and a HMAC key

![](https://bluesun2017.files.wordpress.com/2022/06/synk1.png?w=1024)

Then in your Terraform cloud org:

- Settings
- Run Tasks and enter the URL endpoint and the HMAC key

![](https://bluesun2017.files.wordpress.com/2022/06/synk2.png?w=1024)

Navigate to your Terraform Cloud Workspace, enter the _"Settings"_ menu and chose _"Run Tasks"_

The "run task" you created is available under "Available Run Tasks": click on "+" to add associate it

Choose the enforcement level (Advisory or Mandatory) and click Create.

![](https://bluesun2017.files.wordpress.com/2022/06/synk3.png?w=1024)

For each run triggered in the Terraform Cloud workspace, the result of the Snyk Terraform plan scanning appears under the `run tasks` step, which triggers after the Plan stage finishes.

![](https://bluesun2017.files.wordpress.com/2022/06/synk4.png?w=1024)

After the plan we see the pass or fail of the Snyk run task and a link to the details

Clicking the details link takes us over to the Snyk site where we can see the results:

![](https://bluesun2017.files.wordpress.com/2022/06/synk5.png?w=1024)

![](https://bluesun2017.files.wordpress.com/2022/06/synk6.png?w=1024)

In my case here I have a medium level warning about Key Vault purge protection not being enabled and a low level warning about not using active active VPN gateways.

All very useful stuff and I'll be playing more with Snyk in the future

Link to Synk documentation on TF cloud integration [https://docs.snyk.io/integrations/ci-cd-integrations/integrating-snyk-with-terraform-cloud](https://docs.snyk.io/integrations/ci-cd-integrations/integrating-snyk-with-terraform-cloud)
