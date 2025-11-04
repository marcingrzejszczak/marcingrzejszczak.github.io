---
title: "Microservice Deployment"
summary: "It’s been a while since my last post. In the meantime, nothing has changed in terms of the microservice hype. Here’s a concrete look at deployment in a big, multinational company and a basic pipeline you can start with."
date: 2015-09-26
authors:
  - admin
tags:
  - Microservices
  - Deployment Pipelines
content_meta:
  trending: false
---

{{% callout note %}}
This blog was written in 2015, the approach is still valid today. In the meantime its implementation was done through the [Spring Cloud Pipelines](https://github.com/spring-attic/spring-cloud-pipelines) project. At one of my contracts I've managed to implement it through Java CLI, Bash and abstractions over Jenkins, Github Actions, ArgoCD, Helm etc.
{{% /callout %}}

It’s been a while since my last post. In the meantime, of course, nothing has changed in terms of the microservice hype. I’ve been attending many microservices talks, and what I often miss are **concrete details**—especially around deployment. In this post I’ll depict how, in a big multinational company, you might approach microservice deployment. I’ll walk you through a **basic deployment pipeline** that fits an enterprise setting.

<!--more-->

## The goal

Our goals were to:

### Enforce standards
Have a **single, consistent way** of deploying all microservices—standards for running apps, configuring them (externalized properties), and deploying them. Different apps doing common tasks in different ways makes support expensive and onboarding slow.

### Tackle dependency complexity
Make the deployment process maintainable from an infrastructure and operations perspective, despite the many inter-service dependencies.

### Make the pipeline fast and certain
Achieve the highest possible confidence that features work, keep the pipeline **as fast as possible**, and make **automatic rollback** easy when something goes wrong.

---

## Enforce standards

If you’re starting with microservices, introduce standards early: how apps run, how they’re configured, **and how they’re deployed**. “Why bother, we need to deliver business value?”—your manager might say. The reality: you **pay for non-standardization** in support time and cognitive load. That’s why we enforced **one way** of deploying microservices.

---

## Tackle the microservice dependencies complexity issue

With two monoliths and a small team, you can queue deployments and always run end-to-end tests.

[![Two monoliths](https://2.bp.blogspot.com/-G80VsWKpIy0/VgbyR9nENVI/AAAAAAABII4/rYV8ZIMJi6A/s320/monolith.png)](https://2.bp.blogspot.com/-G80VsWKpIy0/VgbyR9nENVI/AAAAAAABII4/rYV8ZIMJi6A/s1600/monolith.png)  
*Two monolithic applications deployed for end-to-end testing*

With microservices, scale becomes the problem:

[![Many microservices](https://2.bp.blogspot.com/-kggPwWHR-iQ/VgbyhX1x5aI/AAAAAAABIJA/tf2lLkCruxA/s320/many_microservices.png)](https://2.bp.blogspot.com/-kggPwWHR-iQ/VgbyhX1x5aI/AAAAAAABIJA/tf2lLkCruxA/s1600/many_microservices.png)  
*Many microservices deployed in different versions*

Questions arise:

- Should we queue deployments on one shared testing environment, or use an **environment per microservice**?
- If we isolate environments, **which versions** of dependent services should be deployed: development or production?

---

## Make the pipeline fast and certain

We believe in agile and continuous deployment—we want features in production **quickly**. In our monolith days we saw:

- Lots of unit, integration, and end-to-end tests.
- Overlap across test layers—same functionality covered up to three times.
- **Very long** test execution times.

For microservices, we wanted to avoid repeating those mistakes.

---

## Simplify the infrastructure complexity

To avoid environment sprawl and maintenance pain, we simplified. We practice TDD and **Consumer-Driven Contracts**, so we decided to **skip E2E tests** and instead deploy to a VM and run **smoke tests** in isolation—stubbing dependencies so parallel pipelines don’t interfere.

[![Stubbed dependencies](https://3.bp.blogspot.com/-3BIKN1VzDKA/Vgb7oxk8jXI/AAAAAAABIJQ/q_A0LifBgEI/s320/stubbed_dependencies.png)](https://3.bp.blogspot.com/-3BIKN1VzDKA/Vgb7oxk8jXI/AAAAAAABIJQ/q_A0LifBgEI/s1600/stubbed_dependencies.png)  
*Execute tests on a deployed microservice with stubbed dependencies*

Viewed this way, your application tests (“smoke tests”) fit into the pyramid:

[![No E2E](https://3.bp.blogspot.com/-7jseO68-q6A/Vgb8h7Ia1CI/AAAAAAABIJc/C8W0S4qZAic/s320/no_e2e_tests.png)](https://3.bp.blogspot.com/-7jseO68-q6A/Vgb8h7Ia1CI/AAAAAAABIJc/C8W0S4qZAic/s1600/no_e2e_tests.png)  
*We’re testing microservices in isolation*

Why smoke tests? Because we deliberately enforce the **testing pyramid**:

- **Many unit tests** during build.
- **Some integration tests** using **stubs** of dependent services during build.
- **A few acceptance tests** (also on stubs) during build.
- **A handful of smoke tests** against the **deployed** app—just to verify packaging and runtime wiring.

Benefits:

- No need to deploy dependent services.
- The **same stubs** are used during CI integration tests and smoke tests.
- Those stubs are **verified against the producer** (see [Accurest](https://github.com/Codearte/accurest)).
- Few slow tests on a deployed app → **faster pipeline**.
- No deployment queuing—pipelines run **in isolation**.
- No need to spawn a fresh VM for every pipeline.

Trade-offs:

- No full E2E before prod → **less certainty** a feature works across real dependencies.
- The **first real interaction** may happen in production.

---

## Overcoming fear of uncertainty

To counter the lack of pre-prod certainty, we invested in **production observability**:

- Technical and business monitoring with **Graphite**.
- **Seyren** for alerting to page us immediately when KPIs degrade.

No matter how much you invest in test environments or UAT, they’ll **never** perfectly match production. We chose to **reduce artificial environment complexity** and increase **prod monitoring**. With microservices, every choice has a cost.

---

## Technical overview of the solution

We split the simplest microservice deployment pipeline into these steps:

[![Pipeline overview](https://4.bp.blogspot.com/-JmkGUgmrI8Q/Vgb-y9Eg9RI/AAAAAAABIJo/QMa0rkaSfUk/s640/Microservice%2BPipeline.png)](https://4.bp.blogspot.com/-JmkGUgmrI8Q/Vgb-y9Eg9RI/AAAAAAABIJo/QMa0rkaSfUk/s1600/Microservice%2BPipeline.png)  
*Microservice deployment pipeline (without A/B testing)*

### 1) Build the app (after commit)

Preferably, after each PR merge we **trigger the pipeline** (Continuous Deployment).

Outcomes:

- Unit and integration tests run.
- Declared **stub specifications** are verified against the producer application.
- We publish to Nexus:
    - the **fat JAR** of the app, and
    - its **stubs**.

### 2) Deploy to staging

We deploy the freshly built app to **staging**.  
[Spring Cloud Contract Stub Runner](https://docs.spring.io/spring-cloud-contract/reference/project-features-stubrunner.html) downloads the **current development versions** of declared dependency stubs.

Why development versions? We aim to release after each commit; often the dev version **is** the production one (not always—this is a trade-off). Future iterations may test against **both** development and production stubs.

> **Important:** During this step we **upgrade the microservice’s DB schema**.

### 3) Test application rollback

We prefer not to roll back the database. For Mongo-like stores there’s no schema; with Liquibase, rollback scripts add DB complexity. We push complexity **out of the DB and into the code**: we roll back the **application**, not the DB.

- The **new app version must support the old DB schema**.
- **Liquibase changes must be backward-compatible**.

We then run **old smoke tests** on the **rolled-back app** against the **new schema** to validate that prod rollback will likely succeed.

### 4) Deploy to production

If smoke tests pass and rollback works, we go live. This is where **monitoring** matters: verify KPIs and alerts are in place as part of the deployment checklist.

> In this first iteration we **don’t** aim for zero-downtime. That’s another explicit trade-off. Automatic data migrations are hard, and writing code that supports both old and new schemas is mind-bending. We proceed incrementally: stop all instances, start one, **migrate schema**, then start the rest.

### 5) Rollback procedure

If KPIs go red, **rollback quickly**. Since we tested rollback, ops should be able to stop instances, deploy the **previous app version**, and run it against the **new schema**.

---

## Summary

As with all distributed systems, microservice deployment is **not easy**—it’s a series of trade-offs across infrastructure, testing, and database evolution. The approach above is a pragmatic balance of **time, effort, certainty, and feedback**.
