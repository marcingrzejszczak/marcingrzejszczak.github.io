---
layout: page
title: "Abstracts"
date: 2017-01-02 16:35
comments: true
sharing: true
footer: true
---

Here you can find a list of abstracts to talks that I have given:

## Tracing Microservices with Zipkin

The hype related to microservices continues. It's already common knowledge that creating distributed systems is not easy. It's high time to show how that complexity can be contained.

Service Discovery and Registry (Zookeeper / Consul / Eureka), easy request sending with client side load balancing (Feign + Ribbon), request proxying with Zuul. Everything is easy with Spring Cloud. Just add a dependency, a couple of lines of configuration and you're ready to go.

That's fixing difficulties related to writing code - what about solving the complexity of debugging distributed systems? Log correlation and visualizing latency of parts of the system? Spring Cloud Sleuth with Zipkin to the rescue!

The presentation will consist of some theory but there'll also be live coding and demos.

## Consumer Driven Contracts and Your Microservice Architecture

Consumer driven contracts (CDC) are like TDD applied to the API. It’s especially important in the world of microservices. Since it’s driven by consumers, it’s much more user friendly. Of course microservices are really cool, but most people do not take into consideration plenty of potential obstacles that should be tackled. Then instead of frequent, fully automated deploys via a delivery pipeline, you might end up in an asylum due to frequent mental breakdowns caused by production disasters.

We will write a system using the CDC approach together with Spring Boot, Spring Cloud Contract verifier. I'll show you how easy it is to write applications that have a consumer driven API and that will allow a developer to speed up the time of writing his better quality software.

## Continuous Deployment to the Cloud

“I have stopped counting how many times I’ve done this from scratch” - was one of the responses to the tweet about starting the project called Spring Cloud Pipelines. Every company sets up a pipeline from scratch. Every company creates some sort of automation to deploy its applications to servers. Enough is enough - time to automate that and focus on delivering business value.

In this presentation we’ll go through the contents of the Spring Cloud Pipelines project. We’ll start a new project for which we’ll have a deployment pipeline set up in no time. We’ll deploy to Cloud Foundry and check if our application is backwards compatible so that we can roll it back on production.
