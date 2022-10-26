---
title: "Single Active Directory account not syncing to Azure AD"
date: "2016-09-28"
categories: 
  - "cloud"
  - "exchange"
  - "office365"
tags: 
  - "aadconnect"
  - "azuread"
---

A single user in AD was not being synced to Azure AD via AAD Connect.  All other users were syncing just fine.  When looking at the account the attribute msExchRecipientTypeDetails was set to 2, which indicates a linked mailbox.

We used to use linked mailboxes but stopped doing so quite some time ago.  Obviously this account got missed after we migrated all users into a single forest.

To fix

set-user -identity "useralias" -linkedmasteraccount $null
