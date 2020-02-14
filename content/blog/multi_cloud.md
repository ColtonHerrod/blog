---
title: "Should your organization use a multi-cloud architecture?"
date: 2020-02-12T20:58:40-06:00
draft: true
tags: ["aws", "azure", "cloud", "multi-cloud", "architecture"]
summary: Discussing whether or not organizations have a need to operate in multiple clouds at once.
---

Short answer: No.

Long answer: Probably not, but it depends on why you're asking the question.  If your organization is discussing multi-cloud architecture for DR or avoiding lock-in, then your organization almost certainly has no need to use more than one cloud platform.  

The two questions that inform this decision should be:

1. Will your organization would use any unique features of the two clouds?
2. Is dealing with the operational overhead that comes with splitting an application across two data centers worth these unique features.

## Why you shouldn't use multiple cloud vendors

### Multi-cloud disaster recovery

Multi-cloud architectures provide little additional uptime compared to the best single-cloud architectures.  In most circumstances, the time spent learning and automating operations in two different clouds would be better spent refining a multi-region Active/Active or Active/Passive architecture.  

### Avoiding vendor lock-in

While this can be a nice idea in theory, in practice it's rarely worth the effort.  Ensuring that your environment is cloud-agnostic means that your architecture will never be able to take advantage of the unique offerings of a given cloud.

For example, while AWS API Gateway provides support for Canary deployments out of the box, while Azure API Management service doesn't.  This won't stop Canary deployments in Azure, but it does mean that the same architecture can't be used across both environments.  Since the architectures differ, the
