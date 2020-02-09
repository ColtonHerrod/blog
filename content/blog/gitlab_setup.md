---
title: "Deploying Hugo Blog using GitLab Pipelines"
date: 2020-02-09T13:06:51-06:00
draft: false
tags: ["hugo", "azure", "gitlab", "devops", "automation"]
summary: >
  Walkthrough of how to deploy a blog site similar to this using GitLab.  The walkthrough covers:

    * Setup of GitLab CI/CD pipelines to build and deploy the site automatically on a code push.

  Walkthrough of Hugo and Azure setup can be found in a previous article.
---

## Overview

After spending some time with GitLab Actions, I decided to make a shift to GitLab as my repository of choice for this blog.  I made this shift first and foremost as part of my shift to use more open-source software, wherever possible.  I also wanted to broaden my expertise with first-class DevOps tools, and GitLab fits this category.

This article documents the steps required to deploy a Hugo site to Azure using GitLab pipelines.

## Comparison

In running through each pipeline's configuration, I've found that I strongly prefer GitLab's CI/CD system to the GitHub Actions system.  My takeaways from both systems are found below.

### GitHub Actions

Pros:
  - GitHub is the most well-known free Git host.
  - There are many community-developed Actions available for use.
  - The YAML syntax provides easy readability and flexibility.
  - Structure is simple and easy to pick up.

Cons:
  - GitHub Actions is simple to pick up, but does not yet provide a full integrated system.
  - Syntax is wordier and a bit unwieldy.
  - Still an early-stage system, and may be premature for productionalized workloads.

### GitLab CI/CD

Pros:
  - Integrates directly with Docker registry for easy use of containers.
  - Simple syntax makes it easy to pick up.
  - Separate stages make it easy to run different steps in different Docker contexts.
  - Built-in dashboards allow visibility into builds.  Note that this is free for public projects, but requires a paid plan for private projects.

Cons:
  - GitLab is a less commonly used system.
  - Some features require a paid plan.
  - Many simple projects don't need the full GitLab feature set.

## Prerequisites

The initial site setup is documented [here] ({{< ref "blog_setup.md" >}}).  This article will not cover the inital Azure and Hugo site setup again for the sake of brevity.  Prerequisites for the site and GitLab pipelines are documented here.

1. Hugo installed on your local machine. Setting up and maintaining the site requires installing Hugo locally.  A full walkthrough can be found at the Hugo site [here](https://gohugo.io/getting-started/installing/).
2. An Azure account.  A free-tier Azure account can be set up [here](https://azure.microsoft.com/en-us/free/).
3. A GitLab account.  Free-tier is all that's required, and gives private repositories and up to 3000 minutes of pipeline runtime per month.  An account can be set up [here](https://gitlab.com/).
3. A code editor.  My personal preference is [Atom](https://atom.io/), but any similar IDE, such as Sublime Text, VS Code, or Brackets will work.
4. A registered domain name.  My domain was purchased through [Google](https://domains.google.com/m/registrar?nfg), but any domain provider that allows CNAME configuration will work.

## Steps

### 1. Set up initial site

Set up your initial Hugo site and Azure infrastructure by following steps 1 and 2 in my previous article, found [here] ({{< ref "blog_setup.md" >}}).

### 2. Set up GitLab Actions pipeline

To automatically build and deploy changes to your blog, we next need to set up CI/CD pipelines to process those changes.  GitLab provides a robust pipeline tool that takes YAML-formatted pipeline files as inputs for build and release pipelines.

The only prerequsite for adding the below pipeline to your GitLab repo is to create a secret access key for your Azure Storage Account.  The storage access key can be found in the Azure portal, inside the Access Keys section of your storage account.

This key should __not__ be committed to your repository.  Instead, it should be added to the GitLab secret store and referenced by name.  The process for adding it to GitLab can be found [here](https://docs.gitlab.com/ee/ci/variables/#via-the-ui).

An example pipeline for builds and deployments can be found below.  This example pipeline should be put into a file called `.gitlab-ci.yml`.  GitLab will automatically detect this file and create pipelines as defined.

The `<storage_name>` , `<storage_account_name>`, and `<azure_storage_secret>` variables should be replaced with your Azure account's storage blob name, storage account name, and storage account key respectively.

```yaml
variables:
  GIT_SUBMODULE_STRATEGY: recursive

stages:
  - test
  - build
  - deploy

test:
  image: registry.gitlab.com/pages/hugo:latest
  script:
    - hugo
  except:
    - master

build:
  image: registry.gitlab.com/pages/hugo:latest
  stage: build
  script:
    - hugo
  artifacts:
    paths:
      - public/
  only:
    - master

deploy:
  stage: deploy
  image: microsoft/azure-cli
  dependencies:
    - build
  script:
    - az storage blob upload-batch --destination '<storage_name>' --source public/ --account-name <storage_account_name> --account-key <azure_storage_secret>
  only:
    - master
```
### 4. Configure Azure CDN and Domain CNAME

Configure your Azure CDN and CNAME, as described in step 4 in my previous article, found [here] ({{< ref "blog_setup.md" >}}).
