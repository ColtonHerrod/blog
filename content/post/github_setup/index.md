---
title: "Deploying a Hugo Blog using GitHub Actions"
date: 2025-02-16T13:06:51-06:00
draft: false
tags: ["hugo", "github", "devops", "automation"]
categories: ["automation", "github"]
summary: >
  Discussion of how to deploy this blog site to GitHub.
---

## Overview

I recently found myself needing to move away from Azure due to the recent retirement of Edgio and subsequent ballooning of my Azure bill.
As I have been using GitLab in a professional capacity for a couple years, this seemed like a good opportunity to look into GitHub actions again for a quick comparison.
This article discuss the migration process and takeaways from a [previous comparison](../gitlab_setup/gitlab_setup.md) of mine.

## Comparison

In running through each pipeline's configuration, I've found that I strongly prefer GitLab's CI/CD system to the GitHub Actions system.  My takeaways from both systems are found below.

### GitHub Actions

Pros:
    - Seamless integration with GitHub repositories.
    - Extensive marketplace with pre-built actions.
    - Free for public repositories.
    - Supports matrix builds for testing across multiple environments.
    - Lots of quick-start material tailored to common use-cases.

Cons:
    - Limited free minutes for private repositories.
    - Can be more complex for advanced workflows.
    - Less mature compared to other CI/CD systems.
    - Debugging can be challenging without proper logging.

### GitLab CI/CD

Pros:
    - Separate stages make it easy to run different steps in different contexts.
    - Jobs allow lots of overwriting options, which make it very powerful and customizable.
    - Built-in dashboards allow visibility into builds. Note that this is free for public projects, but requires a paid plan for private projects.

Cons:
    - GitLab is a less commonly used system.
    - No marketplace of pre-built actions.
    - Customizable jobs lead to a great deal of complexity if not properly handled.
    - Some features require a paid plan.
    - Many simple projects don't need the full GitLab feature set.

## Migration Findings

Migrating from GitLab and Azure to GitHub and GitHub Pages was a straightforward process. Here are the steps I followed:

### 1. Export GitLab Repository

First, I pointed my existing GitLab repository at a new GitHub project. This can be done by updating the repository's remote URL using the command `git remote set-url origin git@github.com:<new_url>.git`.

### 2. Set Up GitHub Actions

With the repository now in GitHub, I needed to up GitHub Actions to handle the CI/CD pipeline.  As GitHub has a lot of community actions, I used the UI to create a new workflow and found a ready-made Hugo site deployment.  I configured this in my repository, ran it, and it worked out of the box.

### 3. Configure GitHub Pages

As always, DNS is the hardest part.  The Pages site worked immediately, but my CloudFlare DNS configuration required a little massaging.  After pointing my record at the new Pages site, the HTML was failing to load. A little searching later, I found that I needed to turn off the `Proxy` setting in CloudFlare. This change cleared up the issues I was seeing.

By following these steps, I successfully migrated my blog from GitLab and Azure to GitHub and GitHub Pages, leveraging GitHub Actions for continuous deployment.

## Outcome

GitHub Actions has matured a lot since I tried it 5 years ago (wow).  Running a marketplace of actions is a decision that has clearly paid off.  Probably more importantly, the actions and integrations are deeply baked into the UI itself.  This is a significant aid in discoverability in the tool itself, without requiring delving into documentation.

While GitLab remains an overall more powerful tool, GitHub Actions is much more user-friendly, which is just what I want in a hobbyist perspective.
