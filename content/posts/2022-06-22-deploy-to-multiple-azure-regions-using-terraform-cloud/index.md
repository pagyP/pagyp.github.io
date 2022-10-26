---
title: "Deploy to multiple Azure regions using Terraform Cloud"
date: "2022-06-22"
categories: 
  - "cloud"
  - "github"
  - "infrastructure-as-code"
tags: 
  - "azure"
  - "iac"
  - "terraform"
  - "terraform-cloud"
---

Problem Statement:

I have Terraform modules in a single repository that enable me to deploy a hub virtual network, firewall and VPN gateway. How can I deploy this code to multiple Azure regions using [Terraform Cloud](https://cloud.hashicorp.com/products/terraform)?

Answer:

There is more than one way to achieve this:

- A Github repository dedicated to each region and a T[erraform Cloud workspace](https://www.terraform.io/cloud-docs/workspaces) for each region
- Publish the modules to terraform cloud private registry, reference those modules in a repository dedicated for each region and a terraform cloud workspace for each region
- Use a single repo for everything with different directories containing a root module for each environment and a terraform workspace for each region

I'm sure there are other options as well as the above

I chose to use the final option out of the three above. And here is what the workspaces in terraform cloud look like

![](https://bluesun2017.files.wordpress.com/2022/06/tfcloud1.png?w=1024)

As you can see, I have two workspaces: one for the uk south region and another for uk west.

My Github repository looks like this:

![](https://bluesun2017.files.wordpress.com/2022/06/github1.png?w=1024)

The modules folder contains my terraform modules for the resources I need:

![](https://bluesun2017.files.wordpress.com/2022/06/github2.png?w=730)

The connectivity-uks and connectivity-ukw contain the same code file layout and filenames but with one difference:

![](https://bluesun2017.files.wordpress.com/2022/06/github3.png?w=848)

![](https://bluesun2017.files.wordpress.com/2022/06/github5.png?w=822)

The contents of main.tf, outputs.tf and variables.tf is the same in each folder, what differentiates the region (and other things like resource names) is the variable value for location in the \*\*\*\_connectivity.auto.tfvars

![](https://bluesun2017.files.wordpress.com/2022/06/github4.png?w=490)

Why is the file called .auto.tfvars? Because that's the filename convention to load variable values from a file when using terraform cloud [https://www.terraform.io/cloud-docs/workspaces/variables/managing-variables#loading-variables-from-files](https://www.terraform.io/cloud-docs/workspaces/variables/managing-variables#loading-variables-from-files)

Let's now look at the terraform cloud workspace configuration:

![](https://bluesun2017.files.wordpress.com/2022/06/tfcloud2.png?w=1024)

In the version control settings of my alz-connectivity-uks I have configured the working directory to be ./connectivity-uks and so terraform will change into this directory to execute.

Still within the version control settings, I have configured the workspace to only trigger a run when a change is detected in the ./uks-connectivity folder, therefore updating variables/config for uk west will not trigger a run of terraform in the alz-connectivity-uks workspace, and vice versa.

![](https://bluesun2017.files.wordpress.com/2022/06/tfcloud4.png?w=1024)

And correspondingly the workspace alz-connectivity-ukw has the same settings but using the ./connectivity-ukw folder instead.

![](https://bluesun2017.files.wordpress.com/2022/06/tfcloud5.png?w=1024)

![](https://bluesun2017.files.wordpress.com/2022/06/tfcloud6.png?w=1024)

Wrap Up:

Using a single repository containing my terraform code and modules and a Terraform Cloud workspace dedicated to each region I am able to deploy my resources to multiple regions.

It also gives me some separation of concerns as I now have a state file (stored in Terraform Cloud) for each region, so I couldn't easily cause a problem across all regions.

Why this way?

As stated at the beginning of this post there is more than one way to achieve deployment of resources to multiple regions. This happened to be the method that worked best for me. I'll test out other methods in the future.
