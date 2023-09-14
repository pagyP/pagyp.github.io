---
title: "When does network traffic stay on the Azure backbone network? "
date: "2023-09-14"
featured_image: ""
author: "Paul Paginton"
categories: 
  - "blog"
  - "networking"
  - "azure"
tags: 
  - "blog"
  - "azure"
  - "networking"
  - "microsoft"
---

## Problem Statement:

When does network traffic stay on the Azure backbone network?  Under what circumstances would network traffic stay on the Azure backbone network?  What are the implications of this?  When would network traffic not stay on the Azure backbone network?  What are the implications of this?

## Background:
There seems to be a lot of confusion around as to when, and when not, traffic originating in Azure stays within the Azure backbone (from now on I'll refer to this as the Microsoft Network) network.  This is an important question and understanding this correctly is equally important.  I see many blogs/posts/articles that all seem to suggest that there are times when traffic originating from within Azure will transit via the public internet and not stay on the Microsoft network.
Indeed some of Microsoft's own documentation sometimes seems to suggest this as well.

# The Microsoft Network:
The Microsoft network is ASN 8075<sup>1</sup> and as such Microsoft owned IP addresses are routed within AS8075

Let's take our first example:

## Example 1:




<sup>1</sup>. https://bgp.he.net/AS8075#_prefixes , https://learn.microsoft.com/en-us/azure/internet-peering/policy
