---
title: "Setting up a Blog with Hugo and Azure"
date: 2019-11-27T09:25:40-06:00
draft: true
---

## Overview

I'm using Hugo to create this blog site.  Hugo is a solid static website framework that allows users to generate HTML and JavaScript from simple Markdown content files.  There are also a lot of good community-maintained themes.  This website is hosted in Azure Blob Storage and served using Azure CDN through a Google domain name.  The source code for the entire website is hosted in GitHub.

I have also set up a GitHub Actions pipeline to automatically build and deploy the website, along with any new posts, whenever there is a commit to the master branch.  My goal was to be able to publish changes to my blog in an automated format while still being able to host the site myself, and get to know Azure a little better in the process.

Below I've outlined the process I took to set up my blogging site.

## Prerequisites

1. Hugo installed on your local machine. Setting up and maintaining the site requires installing Hugo locally.  A full walkthrough can be found at the Hugo site [here](https://gohugo.io/getting-started/installing/).
2. An Azure account.  A free-tier Azure account can be set up [here](https://azure.microsoft.com/en-us/free/).
3. A GitHub account.  Free-tier is all that's required, and gives private repositories and up to 3000 minutes of pipeline runtime per month.  An account can be set up [here](https://github.com/).
3. A code editor.  My personal preference is [Atom](https://atom.io/), but any similar app will work.

## Steps

### 1. Set up initial site

Initialization of the Hugo site is simple.  

### 2. Set up static website in Azure Blob Storage
