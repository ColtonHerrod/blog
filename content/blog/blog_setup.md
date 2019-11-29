---
title: "Setting up a Blog with Hugo and Azure"
date: 2019-11-27T09:25:40-06:00
draft: true
tags: ["hugo", "azure", "github", "devops", "automation"]
summary: >
  Walkthrough of how to set up a blog site similar to this.  The walkthrough covers:

    * Required prerequisites.
    * How to set up the Hugo site.
    * How to set up Azure Storage Static Site.
    * How to set up the Azure CDN and domain CNAME.
    * Setup of GitHub Actions pipelines to build and deploy the site automatically.
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
4. A registered domain name.  My domain was purchased through [Google](https://domains.google.com/m/registrar?nfg), but any domain provider that allows CNAME configuration will work.

## Steps

### 1. Set up initial site

Before creating this site, create a new git repository in your GitHub account.

Initialization of the Hugo site is simple.  A walkthrough can be found in the Hugo site [here](https://gohugo.io/getting-started/quick-start/).  Different themes can be used in place of Ananke, and require different sets of parameters.  For example, this site uses the [minimo](https://minimo.netlify.com/) theme.

For now, the Ananke theme will work fine.  Themes can be changed at any time, but changing to a new theme may break any layout customizations you have made.

After initializing the site, set the GitHub repository previously created as the origin and push the initialized site to the repo.  The following commands will perform this operation.

```bash
  git commit -m "first commit"
  git remote add origin https://github.com/<profile_name>/<repo_name>.git
  git push -u origin master
```

### 2. Set up static website in Azure Blob Storage

After setting up your Azure account, create an Azure storage account and configure it with a static website.  This can be done with the following commands.

  ```bash
  set resourceGroupName=personalblog
  set storageName=<blog_name>
  set location=centralus
  az login
  az group create --name %resourceGroupName% --location %location%
  az extension add --name storage-preview
  az storage account create -n %storageName% -g %resourceGroupName% -l %location% --sku Standard_LRS --kind StorageV2
  az storage blob service-properties update --account-name %storageName% --static-website --404-document 404.html --index-document index.html
  ```

Running these commands logs you into Azure, creates a new Azure resource group, an Azure Storage Account, and configures the storage account to serve a static website.

### 3. Set up GitHub Actions pipeline

To automatically build and deploy changes to your blog, we next need to set up CI/CD pipelines to process those changes.  GitHub now provides a pipeline tool called GitHub Actions that take YAML-formatted pipeline files as inputs for build and release pipelines.

Example pipelines for builds and deployments can be found below.  These example pipelines should be put under the `.github/workflows/` directory of your repository.

Build pipeline, to be used for every branch except master.
  ```yaml
  name: CI

  on:
    push:
      branches-ignore:
        - master

  jobs:
    build:
      runs-on: ubuntu-latest

      steps:
      - uses: actions/checkout@v1
        with:
          fetch-depth: 1
      - name: Hugo setup
        uses: peaceiris/actions-hugo@v2.3.0
      - name: Build the Hugo project
        run: hugo -D
  ```

Deployment pipeline, to be used exclusively for the master branch.  The `<storage_name>` and `<azure_storage_secret>` variables should be replaced with your Azure account's storage blob name and storage account key respectively.

The storage access key can be found in the Azure portal, inside the Keys section of your storage account.  This key should __not__ be committed to your repository.  Instead, it should be added to the GitHub secret store and referenced by name.

Build pipeline used for the master branch.
  ```yaml
  name: CD

  on:
    push:
      branches:
        - master

  jobs:
    build:
      runs-on: ubuntu-latest

      steps:
      - uses: actions/checkout@v1
      - name: submodule checkout
        run: git submodule update --init --recursive
      - name: Hugo setup
        uses: peaceiris/actions-hugo@v2.3.0
      - name: Build the Hugo project
        run: hugo
      - name: Upload to blob storage
        run: az storage blob upload-batch --destination '<storage_name>' --source public/ --account-name <storage_account_name> --account-key <azure_storage_secret>
  ```

After committing these pipelines to master, your Hugo site should be automatically built and deployed to the new static website storage account.

### 4. Configure Azure CDN and Domain CNAME

Once your site is deployed to the Azure storage account, verify that you are able to access it through the static website URL.  This URL can be found in the Static Website section of your storage account when viewed through the portal.  After verifying the site loads, note the hostname URL.

In the same Azure Portal, visit the resource group created in the previous step.  Add a new CDN profile to be used for the static site to the resource group.  In this new CDN profile add an endpoint with the following settings:

  * __Origin Type:__ Custom Origin
  * __Origin Hostname:__ Hostname URL recorded from the storage account
  * __Origin Host Header:__ Hostname URL, the same as above
  * __Origin Path:__ Leave this section blank

With the CDN profile created, we can now set up a custom domain to capture traffic for the site.  First, create a CNAME pointed at the Azure CDN Endpoint that was previously created..

After creating the CNAME, visit the Azure CDN Endpoint console and select the Custom Domains section.  Click the button to add a Custom Domain, set the Custom Hostname to the CNAME created previously.  If you wish your site to use HTTPS, which is highly recommended, select your custom domain and set HTTPS to __On__.  This will require you to input your CNAME from the previous step.

Azure will then validate that your CNAME is pointed at the CDN endpoint.  If the CNAME is configured correctly, Azure will automatically validate your domain and provision the certificate across the CDN network, which may take up to 6 hours.

After this process is complete, validate that you are able to access your site over your domain name as expected.
