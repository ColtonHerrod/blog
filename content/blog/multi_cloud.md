---
title: "Should your organization use a multi-cloud architecture?"
date: 2020-02-12T20:58:40-06:00
draft: true
tags: ["aws", "azure", "cloud", "multi-cloud", "architecture"]
summary: >
  Walkthrough of how to deploy a blog site similar to this using GitLab.  The walkthrough covers:

    * Setup of GitLab CI/CD pipelines to build and deploy the site automatically on a code push.

  Walkthrough of Hugo and Azure setup can be found in a previous article.
---

Short answer: No.

Long answer: Almost certainly not.  If your organization is discussing multi-cloud architecture for DR or avoiding lock-in, then your organization almost certainly has no need to use multiple cloud platforms.  The two questions that inform this decision should be:
1. Will your organization would use any unique features of the two clouds?
2. Is dealing with the operational overhead that comes with splitting an application across two data centers worth these unique features?
