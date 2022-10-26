---
title: "Move users to OU based on description"
date: "2015-10-30"
categories: 
  - "activedirectory"
  - "powershell"
tags: 
  - "ou"
---

Trying to keep up with job changes and ensuring users accounts are in the correct OU in AD can be problematic.  In the environment I work in each team has their own OU (I'm not sure why it is like this,  I suspect it's a case of 'that's the way we've always done it').

Anyway mine is not to reason why.  So the good thing is that the descriptions for users are fairly well defined, for example someone in the 2nd line team the description is 'Second Line Support Team'.   Using this description users can be moved to the correct OU using Powershell

First I create variables for the OUs

$secondlineou = "OU=SecondLineOU,DC=Example,DC=Com"

then I can use Get-Aduser and filter on the description

Get-Aduser -Filter "Description -eq 'Second Line Support Team'"

This gives me all users in AD with a description of 'Second Line Support Team'

I then pipe that information to the Move-Adobject cmdlet and specify the variable as the target

Move-Adobject -TargetPath $secondlineou

Putting this all together gives

$secondlineOU = "OU=SecondLineOU,DC=Example,DC=Com"

Get-Aduser -Filter "Description -eq 'Second Line Support Team'"| Move-Adobject -TargetPath $secondlineou

 

I have this running as a scheduled task.  This has made things easier on the service desk team.  All they now need to do is updated a users description and the automation will ensure the account gets placed into the correct OU
