---
layout: post
title: "New Project - Spring Cloud Pipelines"
date: 2016-10-18 19:29:24 +0200
comments: true
categories:
- spring-cloud
- deployment
- spring-cloud-contract
- consumer-driven-contracts
- cdc
- jenkins
- concourse
---

I've just published an article at the Spring blog about the creation of a new project called [Spring Cloud Pipelines](https://spring.io/blog/2016/10/18/spring-cloud-pipelines).

# Why?

Why a new project? Cause we've been all doing repetitive work. [Check out this post](http://toomuchcoding.com/blog/2015/09/27/microservice-deployment/) where I write about creation of
a deployment pipeline. Every company does it and wastes money and resource on it. In Pivotal
our goal is to give developers tools they need to deliver features as fast as possible.

[Spring Cloud Pipelines](http://cloud.spring.io/spring-cloud-pipelines/spring-cloud-pipelines.html) gives you an opinionated deployment pipeline. You can use it straight away, you can modify it. Do whatever you please :)

# Demo

The repo is setup with a demo for Concourse CI and Jenkins. Read the docs how to set it up for each of those tools. The deployment is done via Cloud Foundry. For the sake of demo we're using PCF Dev.

# Links

- [Project page](http://cloud.spring.io/spring-cloud-pipelines/)
- [Project documentation](http://cloud.spring.io/spring-cloud-pipelines/spring-cloud-pipelines.html)
- [Concourse opinionated pipeline setup](http://cloud.spring.io/spring-cloud-pipelines/spring-cloud-pipelines.html#concourse)
- [Concourse Website](http://concourse.ci)
- [Jenkins opinionated pipeline setup](http://cloud.spring.io/spring-cloud-pipelines/spring-cloud-pipelines.html#jenkins)
- [Jenkins Job Dsl Plugin](https://github.com/jenkinsci/job-dsl-plugin/wiki)
- [Spring Cloud Pipelines Gitter](https://gitter.im/spring-cloud/spring-cloud-pipelines)
- [Spring Cloud Pipelines GitHub page](https://github.com/spring-cloud/spring-cloud-pipelines)
