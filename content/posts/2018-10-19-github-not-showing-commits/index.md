---
title: "Github not showing commits"
date: "2018-10-19"
categories: 
  - "git"
  - "github"
tags: 
  - "commit-history"
  - "email-address"
---

 

The other day I noticed that my commits were not being shown on my commit history on Github.   I have a new laptop with a fresh install Git for Windows so I figured it was something to do with that, and it was!!

What I had forgotten to do was set my commit email address in Git, I'd obviously done it on my old laptop and forgotten I'd ever done so.

Running '_git config --global user.email_' came back with a blank result on my new laptop so I obviously needed to enter an email address in my Git config.  I found this article [https://help.github.com/articles/setting-your-commit-email-address-on-github/](https://help.github.com/articles/setting-your-commit-email-address-on-github/) which helped me solve my problem.  As the article states if you have enabled email privacy on your Github account (which I had) you need to use a Github provided 'noReply' address as your Git commit address.  This takes the format of 'username@users.noreply.github.com'

So in Git I had to do:

'_git config --global user.email "myusername@users.noreply.github.com"_

Next time I ran pushed my commits to Github the commits showed on my commit histroy
