---
title: "Terraform 0.12"
date: "2019-05-23"
categories: 
  - "devops"
  - "infrastructure-as-code"
tags: 
  - "azure"
  - "terraform"
  - "terraform0-12"
---

So Terraform 0.12 was just [released](https://www.hashicorp.com/blog/announcing-terraform-0-12). I sat down this morning to have a quick play with it. I strongly recommend following the upgrade [guide](https://www.terraform.io/upgrade-guides/0-12.html) and make a separate branch of your TF files before testing 0.12.

After getting everything setup and ready to test I ran a plan and apply using Terraform 0.11.14 and got my infrastructure to a state where no further changes were required. I then ran terraform.exe 0.12checklist and I was presented with the below message:

.\\terraform 0.12checklist  
_After analyzing this configuration and working directory, we have identified some necessary steps that we recommend you take before upgrading to Terraform v0.12:_

- _\[ \] The variable name "count" is now reserved. Terraform 0.12 reserves certain variable names that have (or will have in future) a special meaning when used in a "module" block. The name of this variable must be changed before upgrading to v0.12. This will unfortunately be a breaking change for any user of this module, requiring a major release to indicate that.  
    For more information on the reserved variable names, see the documentation at https://www.terraform.io/docs/configuration/variables.html ._

_Taking these steps before upgrading to Terraform v0.12 will simplify the upgrade process by avoiding syntax errors and other compatibility problems._

So yeah, I had a variable called count which I used to control how many VMs I was creating in one go. This was a fairly easy fix as I just needed to call the variable something else, in my case I renamed the variable to 'vmcount' and updated the relevant stanzas in the TF files. Then running terraform.exe 0.12checklist yielded:

_.\\terraform 0.12checklist  
Looks good! We did not detect any problems that ought to be  
addressed before upgrading to Terraform v0.12._

_This tool is not perfect though, so please check the v0.12 upgrade  
guide for additional guidance, and for next steps:  
https://www.terraform.io/upgrade-guides/0-12.html_

Finally I ran terraform.exe 0.12upgrade:

_.\\terraform.exe 0.12upgrade_

_This command will rewrite the configuration files in the given directory so  
that they use the new syntax features from Terraform v0.12, and will identify  
any constructs that may need to be adjusted for correct operation with  
Terraform v0.12._

_We recommend using this command in a clean version control work tree, so that  
you can easily see the proposed changes as a diff against the latest commit.  
If you have uncommited changes already present, we recommend aborting this  
command and dealing with them before running this command again._

_Would you like to upgrade the module in the current directory?  
Only 'yes' will be accepted to confirm._

_Enter a value: yes_

This modified my TF files, most notably removing the old interpolation syntax. Here's a before and after example:

**Before:**

_resource "azurerm\_virtual\_network" "hub" { name = "${var.hubvnetname}" address\_space = "${var.hubaddressspace}" location = "${azurerm\_resource\_group.networkrg.location}" resource\_group\_name = "${azurerm\_resource\_group.networkrg.name}" tags = "${azurerm\_resource\_group.networkrg.tags}"}_

**After:**

_resource "azurerm\_virtual\_network" "hub" {  
name = var.hubvnetname  
address\_space = var.hubaddressspace  
location = azurerm\_resource\_group.networkrg.location  
resource\_group\_name = azurerm\_resource\_group.networkrg.name  
tags = azurerm\_resource\_group.networkrg.tags  
}_
