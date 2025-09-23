---
title: "Spring Cloud Sleuth 3.0.0 released"
summary: "With the release of the Spring Cloud 2020.0.0 (aka Ilford) release train we're more than happy to announce the general availability of Spring Cloud Sleuth 3.0.0."
date: 2021-01-04

# Featured image
# Place an image named `featured.jpg/png` in this page's folder and customize its options here.
image:
  caption: 'Image credit: [**Unsplash**](https://unsplash.com)'

authors:
  - admin

tags:
  - Blog

content_meta:
  trending: false
---
With the release of the Spring Cloud 2020.0.0 (aka Ilford) release train we're more than happy to announce the general availability of Spring Cloud Sleuth 3.0.0. In this blog post I'll describe the most notable released features (in order of their release dates).

<!--more-->

## Removes Deprecated Modules

Removes ribbon, zuul, hystrix and Spring Cloud Aws support. Check this [PR for more information](https://github.com/spring-cloud/spring-cloud-sleuth/pull/1533).

## Removes Zipkin Dependencies from Core

Zipkin is no longer a part of core of Sleuth. You can check out more in [this PR](https://github.com/spring-cloud/spring-cloud-sleuth/pull/1649).

## Added MANUAL Reactor Instrumentation

Up till now we've been supporting `ON_EACH` and `ON_LAST` Reactor instrumentation modes. That means that we would wrap every single Reactor operator (`ON_EACH`) or the last operator (`ON_LAST`). Those wrappings would do their best to put trace related entries in such a way that thread local based instrumentations would work out of the box (e.g. the MDC context, `Tracer.currentSpan()` etc.). The problem was that on each wrapping downgraded performance drastically and worked most of the time. The on last operator wrapping downgraded performance a lot and worked sometimes. Both had their issues when `flatMap` operators were called and thread switching took place.

With [this commit](https://github.com/spring-cloud/spring-cloud-sleuth/commit/bd149ce4a7c2154fe8526394d14b6ab89addd62e) we've introduced the manual way of instrumenting Reactor. We came to the conclusion that the thread local based paradigm doesn't work well with Reactor. We can't guess for the user what they really want to achieve and which operators should be wrapped. That's why with the `MANUAL` instrumentation mode you can use the `WebFluxSleuthOperators` or `MessagingSleuthOperators` to provide a lambda that should have the tracing context set in thread local.

## MANUAL Reactor Instrumentation Default in Spring Cloud Gateway

With [this issue](https://github.com/spring-cloud/spring-cloud-sleuth/issues/1710) we're setting the manual instrumentation as the default one for Spring Cloud Gateway. The performance gets drastically improved and the tracing context still gets automatically propagated. If you need to do some customized logging etc. just use the `WebFluxSleuthOperators`.

## Remove The Legacy MDC Entries

[This issue](https://github.com/spring-cloud/spring-cloud-sleuth/issues/1221) introduces a change in the MDC keys (no more `X-B3-...` entries in MDC).

Before

```
2019-06-27 19:36:11,774
INFO {X-B3-SpanId=e30b6a75bcff782b, X-B3-TraceId=e30b6a75bcff782b, X-Span-Export=false, spanExportable=false, spanId=e30b6a75bcff782b, traceId=e30b6a75bcff782b} some log!
```

After

```
2019-06-27 19:36:11,774
INFO {spanId=e30b6a75bcff782b, traceId=e30b6a75bcff782b} some log!
```

## Code Refactoring

### Removing Zipkin Starter

The `spring-cloud-starter-zipkin` dependency is removed. You need to add `spring-cloud-starter-sleuth` and the `spring-cloud-sleuth-zipkin` dependency.

### New Tracer Abstraction

OpenZipkin Brave was there in Sleuth's code as the main abstraction since Sleuth 2.0.0. We've decided that with Sleuth 3.0.0 we can create our own abstraction (as we do in each Spring Cloud project) so that OpenZipkin Brave becomes one of the supported tracer implementations.

With [this PR](https://github.com/spring-cloud/spring-cloud-sleuth/pull/1757) we've introduced a new abstraction that wraps Brave. We also added support for another tracer - [OpenTelemetry](https://github.com/spring-cloud/spring-cloud-sleuth/issues/1497).

### Aligning with Spring Boot

With [this PR](https://github.com/spring-cloud/spring-cloud-sleuth/pull/1762) and [that PR](https://github.com/spring-cloud/spring-cloud-sleuth/pull/1784) we've refactored Spring Cloud Sleuth to reflect Spring Boot's module setup. We've split the project into API, instrumentations, auto-configurations etc. Also the documentation layout was updated to look in the same way the Spring Boot one does.

## OpenTelemetry support

Initially, with [this commit](https://github.com/spring-cloud/spring-cloud-sleuth/commit/6e306e594d20361483fd19739e0f5f8e82354bf5), we've added a `spring-cloud-sleuth-otel` module inside Spring Cloud Sleuth that introduced [OpenTelemetry](https://opentelemetry.io) support.

With [this PR](https://github.com/spring-cloud/spring-cloud-sleuth/pull/1802) we've decided to move Spring Cloud Sleuth and OpenTelemetry integration to an [incubator project](https://github.com/spring-cloud-incubator/spring-cloud-sleuth-otel/). Once OpenTelemetry & OpenTelemetry Instrumentation projects become stable we will consider next steps.

# Links

* [Spring Cloud Sleuth 3.0.0 docs](https://docs.spring.io/spring-cloud-sleuth/docs/3.0.0/reference/html/)
* [Spring Cloud Sleuth OpenTelemetry project](https://github.com/spring-cloud-incubator/spring-cloud-sleuth-otel/)
* [Spring Cloud Sleuth OpenTelemetry docs](https://spring-cloud-incubator.github.io/spring-cloud-sleuth-otel/docs/current/reference/html/index.html)
* [Spring Cloud Sleuth 3.0.0 release notes](https://github.com/spring-cloud/spring-cloud-release/wiki/Spring-Cloud-2020.0-Release-Notes#spring-cloud-sleuth)
* [Spring Cloud Sleuth 3.0.0 migration guide](https://github.com/spring-cloud/spring-cloud-sleuth/wiki/Spring-Cloud-Sleuth-3.0-Migration-Guide)


## Stay in touch!

In case of any questions don't hesitate to ping us

* On [Github](https://github.com/spring-cloud/spring-cloud-sleuth/)
* On [Gitter](https://gitter.im/spring-cloud/spring-cloud-sleuth)
* On [Stackoverflow](https://stackoverflow.com/questions/tagged/spring-cloud-sleuth)