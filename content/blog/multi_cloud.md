---
title: "Should your organization use multiple cloud platforms?"
date: 2020-02-12T20:58:40-06:00
draft: false
tags: ["aws", "azure", "gcp", "cloud", "multi-cloud", "architecture"]
categories: ["architecture", "aws", "azure"]
summary: Discussing whether or not organizations have a need to operate in multiple clouds at once.
---

TL;DR: No.

Long version: Probably not, but it depends on why you're asking the question.  If your organization is discussing multi-cloud architecture for DR or avoiding lock-in, then your organization almost certainly has no need to use more than one cloud platform.

The two main questions that inform this decision should be:

1. Will your organization would use any __unique features__ or __specialties__ of the cloud platforms?
2. Is dealing with the __operational overhead__ that comes with splitting an application across two data centers worth these unique features?

As a rule of thumb, it is usually preferable to keep your organization's main application workloads entirely within a single cloud provider.  If there are any unique projects, such as AI/ML, voice recognition, or facial recognition, it may make sense to leverage a different cloud provider who has a greater competency in that area.

This won't always be the case, and making this choice will depend on the exact circumstances and needs of your organization.

## Why you shouldn't use multiple cloud vendors

### Multi-cloud disaster recovery

Multi-cloud architectures provide little additional uptime compared to well-implemented single-cloud architectures.  AWS and Azure occasionally have region-wide outages of specific services, such as the S3 disruption in the US East 1 region in February 2017.  These outages are region-specific, very infrequent (the last major AWS outage occurred in May 2018), and are mitigated by multi-region environments and deployments.

As well, AWS has never lost an entire Availability Zone, only some services within the zone.  In most circumstances, the time your team spends learning and automating operations in two different clouds would be better spent creating refining a multi-region Active/Active or Active/Passive architecture.

A Microsoft-provided reference architecture for running in multiple Azure regions can be found [here](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/n-tier/multi-region-sql-server).  A third-party guide for AWS multi-region architectures can be found [here](https://read.acloud.guru/why-and-how-do-we-build-a-multi-region-active-active-architecture-6d81acb7d208).

### Avoiding vendor lock-in

While this can be a nice idea in theory, in practice it's rarely worth the effort.  Ensuring that your environment is cloud-agnostic means your architecture must follow one of two paths.  Either your architecture across both clouds needs to be identical, which will prevent you from using a lot of the features of both providers, or the architecture for each cloud is unique, causing extra design and implementation work.

For example, while AWS API Gateway provides support for Canary deployments out of the box, while Azure API Management service doesn't.  This won't prevent using a different pattern for Canary deployments in Azure, but it does mean that architecture and implementation will require twice the work.

Working to have identical offerings across platforms also prevents you from taking advantage of the specialties of a given cloud, such as AWS SageMaker for machine learning, or Azure's built-in Windows and SQL integrations.

## When you should consider using multiple cloud vendors

Often, a cloud provider will have a unique offering or specialty with a particular feature.  For example, AWS SageMaker is generally the preferred tool for AI/ML projects over Azure Machine Learning.  SageMaker provides a higher average accuracy rate, better build and training features, and a more productionalized feature set.

Anyone getting started with AI/ML would be tempted to leverage this tool, but if your organization's workload is already in Azure, it would take quite a bit of effort to set up a net-new environment in AWS for the purposes of using a single tool.  If the planned project can only work in SageMaker, it may be worth the effort, especially if you expect to get a great deal of value out of the project.  However, if the project will eventually be productionalized, plan to put some serious consideration into architecting a robust environment for the new application.

There are a few other features that can take the place of SageMaker in this example, but this should serve to illustrate the main reasons to invest in multiple cloud platforms, as long as your organization is willing to deal with the additional overhead in training, design, and costs.
