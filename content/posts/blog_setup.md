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
2. Git installed on your local machine.  Downloads can be found [here](https://git-scm.com/).
2. An Azure account.  A free-tier Azure account can be set up [here](https://azure.microsoft.com/en-us/free/).
3. A GitHub account.  Free-tier is all that's required, and gives private repositories and up to 3000 minutes of pipeline runtime per month.  An account can be set up [here](https://github.com/).
3. A code editor.  My personal preference is [Atom](https://atom.io/), but any similar app will work.

## Steps

### 1. Set up initial site

Initialization of the Hugo site is simple.  After installing the framework, use the following steps to bootstrap your site.
1. __Create new site:__ Open a terminal in the directory you wish to store your site and run the command `hugo new site <site_name>`, replacing <site_name> with the name you wish your site to have.
2. __Theme your site:__ Run the following commands to add a default theme to your site.  This can be changed at any time.
   ```bash
   cd site_name
   git init
   git submodule add https://github.com/budparr/gohugo-theme-ananke.git themes/ananke
   echo 'theme = "ananke"' >> config.toml
   ```
3. __Create your first post:__ Hugo command line allows you to create content files using the format `hugo new <path_to_post>`.  Create a Hello World post by running `hugo new posts/hello-world.md`.  Open up the post in your code editor of choice and add any initial content.    
Note that the header marks the content as a draft with the line `draft = true`.  By default, Hugo does not publish draft posts.

### 2. Set up static website in Azure Blob Storage
