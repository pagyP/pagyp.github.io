---
title: "Blog Migration from hosted Wordpress to Hugo and Github Pages"
date: "2022-10-27"
featured_image: ""
author: "Paul Paginton"
categories: 
  - "blog"
  - "github"
  - "hugo"
tags: 
  - "blog"
  - "hugo"
  - "github pages"
  - "github"
---

## Problem Statement:

My blog is hosted on Wordpress and I want to migrate it to Github Pages using Hugo and enable version control for my blog posts and ensure I bring across all the content from my Wordpress blog.



The above problem statement is a bit of a mouthful so let's break it down into smaller chunks:

- Blog hosted on Wordpress.com and was costing in the region of £80 per year.
- I have always found Wordpress a bit awkward to use and too 'heavy' for my needs.
- I wanted to use a static site generator to generate my blog and host it on Github Pages.
- I wanted to author my blog posts in Markdown.
- Bring costs down to zero.
  
Answer:
I ended up using Hugo for my static site generator and Github Pages for hosting.  

## Method:

- Followed the quickstart guide onthe Hugo website https://gohugo.io/getting-started/quick-start/ 
- Exported all my blog posts from Wordpress using the Wordpress export tool https://wordpress.com/support/export/ 
- Followed the guide for setting up Github Pages https://docs.github.com/en/pages/getting-started-with-github-pages/about-github-pages 
- Followed the guide for setting up Hugo on Github Pages https://gohugo.io/hosting-and-deployment/hosting-on-github/
- Used this tool to convert the Wordpress export to markdown https://github.com/lonekorean/wordpress-export-to-markdown
- Copied over the exported folders and files to the content folder in my Hugo project
- The Hugo quickstart guide uses the Ananke theme by default. I wanted to customise the theme so I followed some examples from the github repo for the ananke theme https://github.com/theNewDynamic/gohugo-theme-ananke 
- Set a custom domain name of blog.paginton.cloud by following the guide here https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site

### Some notes:

- The Github Pages guide on the github site pushes you to using Jekyll for your static site generator. I found the Hugo documentation to be much more comprehensive and easier to follow.  I had problems trying to change/update the theme with Jekyll.
- When you enable a custom domain name an automatic commit adds a file to the gh-pages repo called CNAME, this CNAME file contains your custom domain name.  In my case blog.paginton.cloud. This file contains the custom domain name.  If you delete this file then your custom domain name will stop working.  When using Hugo this file needs to be in a folder called static.  So I created a folder called static in the root of my Hugo project and copied the CNAME file into this folder.  This is because Hugo will copy the contents of the static folder into the public folder when it generates the static site.  This is the folder that is pushed to the gh-pages branch when you run the command Github action runs to generate the static site.  If you don't do this then your custom domain name will stop working.
- Following the various guides the Github actions are created for you, or you are given the code to add.  This means I don't necessarily understand what is going on under the hood.  I am hoping to learn more about this in the future.  But at the moment it is enough for me to know that it works and all I have to do is author my posts in markdown and push them to the main branch of my Hugo project and the Github action will generate the static site and push it to the gh-pages branch.  The Github Pages site will then be updated with the new content.

### Looking forward:

I plan to play with the theme some more and add some more customisation. This will likely take the form of adding an about page first of all.
Ensure that I do not renew my Wordpress.com subscription and cancel it.

## Conclusion:

- The migration from Wordpress to Hugo and Github pages was relatively painless and hassle free and I hope to blog more now that I can simple author posts in markdown.
- If anyone is looking to migrate from Wordpress to Hugo and Github Pages then I hope this post helps you, please reach out to me if you have any questions.

