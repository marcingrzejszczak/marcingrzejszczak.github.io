---
layout: page
title: "Abstracts"
date: 2017-01-02 16:35
comments: true
sharing: true
footer: true
---

Here you can find a list of abstracts to talks that I have given

# English

## Tracing Applications with Zipkin

### Type

_Presentation and live coding_

### Abstract

The hype related to microservices continues. It's already common knowledge that creating distributed systems is not easy. It's high time to show how that complexity can be contained.

Service Discovery and Registry (Zookeeper / Consul / Eureka), easy request sending with client side load balancing (Feign + Ribbon), request proxying with Zuul. Everything is easy with Spring Cloud. Just add a dependency, a couple of lines of configuration and you're ready to go.

That's fixing difficulties related to writing code - what about solving the complexity of debugging distributed systems? Log correlation and visualizing latency of parts of the system? Spring Cloud Sleuth with Zipkin to the rescue!

The presentation will consist of some theory but there'll also be live coding and demos.

### Video Samples

- [Example](https://www.youtube.com/watch?v=tzAyFyB56lQ&feature=youtu.be)
- [Example](https://content.pivotal.io/springone-platform-2017/distributed-tracing-latency-analysis-for-your-microservices-grzejszczak-krishna)

## Consumer Driven Contracts like TDD to the API

### Type

_Presentation and live coding_

### Abstract

Consumer driven contracts (CDC) are like TDD applied to the API. It’s especially important in the world of microservices. Since it’s driven by consumers, it’s much more user friendly. Of course microservices are really cool, but most people do not take into consideration plenty of potential obstacles that should be tackled. Then instead of frequent, fully automated deploys via a delivery pipeline, you might end up in an asylum due to frequent mental breakdowns caused by production disasters.

We will write a system using the CDC approach together with Spring Boot, Spring Cloud Contract verifier. I'll show you how easy it is to write applications that have a consumer driven API and that will allow a developer to speed up the time of writing his better quality software.
The presentation will consist of some theory but there'll also be live coding and demos.

### Video Samples

- [Example](https://www.youtube.com/watch?v=UwwaWodTj1k&list=PLxZQe6I1pYpfbUI587PZ7CY0l3oKPg9hH&index=28)
- [Example](https://www.youtube.com/watch?time_continue=538&v=sAAklvxmPmk)

## Why Contract Tests Matter?

### Type

_Live coding_

### Abstract

You are writing integration tests, aren't you? Have you ever needed to stub the HTTP or a messaging call? Have your tests passed? That's awesome, but it doesn't mean that your application is working fine or that your system will not break on end to end tests.

In this presentation you'll see a system composing of two apps written from scratch. We'll present the most frequent mistakes that take place during writing integration tests and we'll show how you can use contract testing to fix those problems.

### Video Samples

- [Example](https://www.youtube.com/watch?v=YoqSblR1nmI)
- [Example](https://www.youtube.com/watch?v=IiK9A9nQ6NU&list=PLH21tc7N2sw0WUoamLp4UqTlKq-_xZgMB&index=23)

## Contract Tests in the Enterprise

### Type

_Presentation_

### Abstract

Is your legacy application talking to a service that is never up and running on your shared testing environment? Does your company waste a lot of time and money on regression testing only to see that, yet again, someone has created a typo in the API? Enough is enough. Time to fix this problem using contract tests!

In this presentation you'll see how to migrate a legacy application to work with stubs of external applications. We'll show different ways of increasing your test reliability by writing contract tests of your API. You'll see the difference between producer and consumer driven contracts.

### Video Samples

- [Example](https://content.pivotal.io/springone-platform-2017/consumer-driven-contracts-and-your-microservice-architecture-marcin-grzejszczak-adib-saikali)

## Testing Your Message-Driven Application

### Type

_Presentation and demo_

### Abstract

So you end up with messaging and event-driven architecture. You also have heard about event sourcing and applied this principle in a few places in your code. Everything seems to be working perfectly, you are about to perform the first release and you’ve decided to change the structure of one event. The event you have changed was both used for state reconstruction (event sourcing) and integration (event-driven architecture). Due to that change, out of a sudden, your acceptance environment stopped working… “How come?! I have only changed an implementation detail in my cool event-sourced domain!” said the senior developer.

In this talk, we will chat about how to work with events that are used as integration messages in your system. We will tackle content-negotiation, versioning and acceptance tests.

There will be a lot of Spring Cloud stack and we will see how we can benefit from Consumer Driven Contracts when NOT using REST APIs.

### Video Samples

- [Example](https://www.youtube.com/watch?v=hRgD4bpS7hY&list=PLAdzTan_eSPQsR_aqYBQxpYTEQZnjhTN6&t=0s&index=64)

## Continuous Deployment of Your Application

### Type

_Presentation and demo_

### Abstract

“I have stopped counting how many times I’ve done this from scratch” - was one of the responses to the tweet about starting the project called Cloud Pipelines. Every company sets up a pipeline to take code from your source control, through unit testing and integration testing, to production from scratch. Every company creates some sort of automation to deploy its applications to servers. Enough is enough - time to automate that and focus on delivering business value.

In this presentation we’ll go through the contents of the Cloud Pipelines project. We’ll look at how we think a good deployment looks like and how a new project can have the deployment pipeline set up in no time. We’ll deploy the application to Cloud Foundry (but we also could do it with Kubernetes and Ansible) and check if it's is backwards compatible so that we can roll it back on production.

### Video Samples

- [Example](https://content.pivotal.io/springone-platform-2017/continuous-deployment-to-the-cloud-marcin-grzejszczak-cora-iberkleid)
- [Example](https://www.youtube.com/watch?v=glhXS34umbw&list=PLH21tc7N2sw0WUoamLp4UqTlKq-_xZgMB&index=41)

## How to live in a post-Spring-Cloud-Netflix world

### Type

_Presentation and demo_

### Abstract

Zuul? Gateway? Should we get rid of Ribbon? What is going on with Hystrix? If you have ever faced those questions, come and listen to this talk. In December 2018, Netflix decided to move a number of their popular OSS projects, like Hystrix and Ribbon into maintenance mode and to make newer, backward incompatible versions of some others, like Zuul and Archaius. The Spring Cloud team moved some of the corresponding Spring-Cloud-Netflix projects into maintenance mode as well and proposed a newer, more modern Spring Cloud stack that could be used instead. During this talk, we would like to show how to move over to these newer solutions. We will discuss possible approaches, show a code demo and speak about potential issues and solutions.

# Polskie

## Ciągłe wdrażanie Twoich aplikacji

### Typ

_Prezentacja i demo_

### Abstrakt

"Przestałem liczyć ile razy musiałem zbudować to od zera" - to jedna z odpowiedzi na Tweet dot. rozpoczęcia projektu Cloud Pipelines. W każdej firmie, na nowo tworzone są rury wdrożeniowe, które wyciągają kod z repozytorium, odpalają testy jednostkowe i integracyjne, po czym wdrażają paczkę na produkcję. Automatyzacja samego wdrożenia często też jest tworzona od nowa, za pomocą przeróżnych skryptów. Mamy tego dość! Czas zautomatyzować wszystkie te procesy i skupić się na dowożeniu wartości biznesowych.

W tej prezentacji przejdziemy przez koncepcje zawarte w projekcie Cloud Pipelines. Zobaczymy jak wygląda nasza modelowa rura wdrożeniowa i jak nowoutworzony projekt może z niej skorzystać. Wgramy aplikację na Cloud Foundry (moglibyśmy też ją wgrać do Kubernetesa lub gdziekolwiek za pomocą Ansiblea) i zweryfikujemy kompatybilność wsteczną aplikacji w celu przywrócenia wersji na produkcji w przypadku jakichkolwiek komplikacji.