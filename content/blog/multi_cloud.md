---
title: "Should your organization use multiple cloud platforms?"
date: 2020-02-12T20:58:40-06:00
lastmod: 2021-08-22T15:00:00-06:00
draft: false
tags: ["aws", "azure", "gcp", "cloud", "multi-cloud", "architecture"]
categories: ["architecture", "aws", "azure"]
summary: Discussing whether or not organizations have a need to operate in multiple clouds at once.
---

TL;DR: No.

Long version: Probably not, but it depends on why you're asking the question.  If your organization is discussing multi-cloud architecture for DR or avoiding lock-in, then your organization almost certainly has no need to use more than one cloud platform.

The main questions that inform this decision should be:

1. Will your organization would use any __unique features__ or __specialties__ of the cloud platforms?
2. Does your organization require as close to __100% uptime__ as is currently possible?
3. Is dealing with the __operational overhead__ that comes with splitting an application across two data centers worth these features?

As a rule of thumb, it is usually preferable to keep your organization's main application workloads entirely within a single cloud provider.  This won't always be the case, and making this choice will depend on the exact circumstances and needs of your organization.  Generally speaking, however, if the decision to use multiple cloud provides is a question rather than an application requirement, the answer should almost always be to remain with a single provider.

## Why you shouldn't use multiple cloud vendors

### Multi-cloud disaster recovery

Multi-cloud architectures provide little additional uptime compared to well-implemented single-cloud architectures.  AWS and Azure occasionally have region-wide outages of specific services, such as the S3 disruption in the US East 1 region in February 2017.  These outages are region-specific, very infrequent (the last major AWS outage occurred in May 2018), and are mitigated by multi-region environments and deployments.

As well, AWS has never lost an entire Availability Zone, only some services within the zone.  In most circumstances, the time your team spends learning and automating operations in two different clouds would be better spent creating refining a multi-region Active/Active or Active/Passive architecture.

A Microsoft-provided reference architecture for running in multiple Azure regions can be found [here](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/n-tier/multi-region-sql-server).  A third-party guide for AWS multi-region architectures can be found [here](https://read.acloud.guru/why-and-how-do-we-build-a-multi-region-active-active-architecture-6d81acb7d208).

There are some circumstances, noted in the following section, where this may be necessary, but most organizations I've worked with in the past will be served perfectly well with a multi-region highly available architecture.

### Avoiding vendor lock-in

While this can be a nice idea in theory, in practice it's rarely worth the effort.  Ensuring that your environment is cloud-agnostic means your architecture must follow one of two paths.  Either your architecture across both clouds needs to be identical, which will prevent you from using a lot of the specialty features of both providers, or the architecture for each cloud is unique, causing extra design and implementation work.

For example, while AWS API Gateway provides support for Canary deployments out of the box, while Azure API Management service doesn't.  This won't prevent using a different pattern for Canary deployments in Azure, but it does mean that architecture and implementation will require twice the work.  Working to have identical offerings across platforms also prevents you from taking advantage of the specialties of a given cloud, such as AWS RDS for easy creation of databases, or Azure's built-in Windows and SQL integrations.

As noted in the questions at the beginning of this article, these limitations don't mean that this path should be rejected out of hand.  It does mean that there should be a strong need to avoid this lock-in.

### Increased Networking costs

Most public cloud providers put a price on __egress__ traffic.  That is, any traffic leaving the cloud's network is metered.  If your multi-cloud applications send data to each other frequently or in any real size, this cost can rapidly increase.  The increased operational cost must be carefully weighed before moving to a multi-cloud implementation.

## When you should consider using multiple cloud vendors

### Leveraging Provider Specialties

Often, a cloud provider will have a unique offering or specialty with a particular feature.  For example, AWS SageMaker is generally the preferred tool for AI/ML projects over Azure Machine Learning.  SageMaker provides a higher average accuracy rate, better build and training features, and a more productionalized feature set.

Anyone getting started with AI/ML would be tempted to leverage this tool, but if your organization's workload is already in Azure, it would take quite a bit of effort to set up a net-new environment in AWS for the purposes of using a single tool.  If the planned project can only work in SageMaker, it may be worth the effort, especially if you expect to get a great deal of value out of the project.  However, if the project will eventually be productionalized, plan to put some serious consideration into architecting a robust environment for the new application.

### Serving Specific Clients

In some cases, a business-to-business organization will need to deploy their application to a public cloud provider in order to serve a client operating in that cloud provider.  This is a use case outside of the ones noted previously, as it is dependent entirely on business model rather than technical architecture.  This requirement will largely impact organizations providing middleman or foundational cloud types of service.  As such, this pattern will be driven by the product or application teams rather than the cloud or operational team.

### True 100% Uptime Requirements

Some rare organizations require as close to 100% uptime as humanly possible, whether due to contractual SLA requirements, internal product objectives, or application visibility.  A properly implemented multi-region architecture can provide uptime of 5 nines (99.999%).  This is suitable for the vast majority of organizations.  However, if your organization does require higher uptime assurances, a multi-cloud approach can help to fit this need.

The main callout here are that other services may not use this same approach, and as such may go down if a cloud provider goes down.  A chain of services is only as strong as its weakest link.  Strengthening links in your own application chain may expose weaknesses elsewhere.

## Summary

There are other, more niche cases to use multiple clouds, but this should serve to illustrate the main reasons to invest in multiple cloud platforms.  If your organization chooses to go this route, ensure your teams are willing to deal with the additional overhead in training, design, and costs.
