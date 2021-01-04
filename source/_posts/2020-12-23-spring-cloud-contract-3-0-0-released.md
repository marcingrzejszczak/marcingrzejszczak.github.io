---
layout: post
title: Spring Cloud Contract 3.0.0 released
date: 2020-12-23 14:58 +0100
comments: true
categories: articles
tags:
- testing
- testowanie
- Spring Cloud Contract
---

With the release of the Spring Cloud 2020.0.0 (aka Ilford) release train we're more than happy to announce the general availability of Spring Cloud Contract 3.0.0. In this blog post I'll describe the most notable released features (in order of their release dates).

<!--more-->

## Incremental Test Generation for Maven

With the [Incremental Test Generation for Maven](https://github.com/spring-cloud/spring-cloud-contract/pull/1361) we're generating tests, stubs and stubs jar only if the contracts have changed. The feature is opt-out (enabled by default).

## Resolves Credentials from settings.xml

With the [
support to resolve credentials from settings.xml](https://github.com/spring-cloud/spring-cloud-contract/pull/1362) when using Aether based solution to fetch the contracts / stubs, we will reuse your `settings.xml` credentials for the given server id (via the `stubrunner.server-id ` property).

## Rewrite Groovy to Java

It was fantastic to see so many people take part in rewriting the Spring Cloud Contract's codebase from Groovy to Java. You can check [this issue](https://github.com/spring-cloud/spring-cloud-contract/issues/1470) for more information.

## Allow to Extend Contract & Stubs

With [this issue](https://github.com/spring-cloud/spring-cloud-contract/issues/1465) and this [pull request](https://github.com/spring-cloud/spring-cloud-contract/pull/1466) we've added an option to provide `metadata` to your contracts. Since we didn't want to map all WireMock properties to the core of our Contract definition, we've allowed passing of metadata under the `wiremock` key. The passed value can be an actual WireMock definition. We will map that part to the generated stub.

Example of adding delays:

```groovy
Contract.make {
		request {
			method GET()
			url '/drunks'
		}
		response {
			status OK()
			body([
				count: 100
			])
			headers {
				contentType("application/json")
			}
		}
		metadata([wiremock: '''\
	{
		"response" : {
			"delayDistribution": {
                    "type": "lognormal",
                    "median": 80,
                    "sigma": 0.4
            }
		}
	}
'''
		])
```

That also means that you can provide your own metadata. You can read more about this in the documentation

* [existing metadata entries](https://docs.spring.io/spring-cloud-contract/docs/3.0.0/reference/html/project-features.html#contract-dsl-metadata)
* [customization of WireMock via metadata](https://docs.spring.io/spring-cloud-contract/docs/3.0.0/reference/htmlsingle/#customization-wiremock-from-metadata)
* [customization of WireMock via metadata & custom post processor](https://docs.spring.io/spring-cloud-contract/docs/3.0.0/reference/htmlsingle/#customization-wiremock-from-metadata-custom-processor)

## New [Custom] Mode of Test Generation

With [this pr](https://github.com/spring-cloud/spring-cloud-contract/pull/1511) we've introduced a new `custom` mode of test generation. You're able to pass your own implementation of an HTTP client (you can reuse our `OkHttpHttpVerifier`), thanks to which you can e.g. use HTTP/2. This was a prerequisite for the GRPC task. Thanks to the Spring Cloud Contract Workshops and the following refactoring of Spring Cloud Contract it was quite easy to add this feature, so thanks everyone involved then!

You can read more about this in [the documentation](https://docs.spring.io/spring-cloud-contract/docs/3.0.0/reference/html/project-features.html#features-custom-mode).

## Experimental GRPC Support

With the custom mode in place we could add the experimental GRPC support. Why experimental? Due to GRPC’s tweaking of the HTTP/2 Header frames, it’s impossible to assert the `grpc-status` header. You can read more about the feature, the issue and workarounds in [the documentation](https://docs.spring.io/spring-cloud-contract/docs/3.0.0/reference/html/project-features.html#features-grpc).

Here you can find an example of [GRPC producer](https://github.com/spring-cloud-samples/spring-cloud-contract-samples/tree/master/producer_grpc) and of a [GRPC consumer](https://github.com/spring-cloud-samples/spring-cloud-contract-samples/tree/master/consumer_grpc).

## GraphQL Support

With [this PR](https://github.com/spring-cloud/spring-cloud-contract/pull/1506) we've added GraphQL support. Since GraphQL is essentially POST to and endpoint with specific body, you can create such a contract and set the proper metadata. You can read more about this in [the documentation](https://docs.spring.io/spring-cloud-contract/docs/3.0.0/reference/html/project-features.html#features-graphql). 

Here you can find an example of [GraphQL producer](https://github.com/spring-cloud-samples/spring-cloud-contract-samples/tree/master/producer_graphql) and of a [GraphQL consumer](https://github.com/spring-cloud-samples/spring-cloud-contract-samples/blob/master/consumer/src/test/java/com/example/BeerControllerGraphQLTest.java).

## Stub Runner Boot Thin JAR

With [this issue](https://github.com/spring-cloud/spring-cloud-contract/issues/1385) we've migrated the Stub Runner Boot application to be a thin jar based application. Not only have we managed to lower the size of the produced artifact, but also we're able via properties turn on profiles (e.g. `kafka` or `rabbit` profiles) that would fetch additional dependencies at runtime.

## Messaging Polyglot Support

### Pre-built kafka and amqp support

With the thin jar rewrite and [this PR](https://github.com/spring-cloud/spring-cloud-contract/pull/1472) and [this issue](https://github.com/spring-cloud/spring-cloud-contract/issues/1468) we're adding support for Kafka and AMQP based solutions with the Docker images.

You'll have to have the following prerequisites met:

* Middleware (e.g. RabbitMQ or Kafka) must be running before generating tests
* Your contract needs to call a method `triggerMessage(...)` with a String parameter that is equal to the contract's label.
* Your application needs to have a HTTP endpoint via which we can trigger a message
* That endpoint should not be available on production (could be enabled via an environment variable)

Your contract can leverage the `kafka` and `amqp` metadata sections like below:

```yaml
description: 'Send a pong message in response to a ping message'
label: 'ping_pong'
input:
    # You have to provide the `triggerMessage` method with the `label`
    # as a String parameter of the method
    triggeredBy: 'triggerMessage("ping_pong")'
outputMessage:
    sentTo: 'output'
    body:
        message: 'pong'
metadata:
    amqp:
        outputMessage:
            connectToBroker:
                declareQueueWithName: "queue"
            messageProperties:
				receivedRoutingKey: '#'
```

### Standalone mode

There is legitimate reason to run your contract tests against existing middleware. Some
testing frameworks might give you false positive results - the test within your build
passes whereas on production the communication fails.

In Spring Cloud Contract docker images we give an option to connect to existing middleware.
As presented in previous subsections we do support Kafka and RabbitMQ out of the box. However,
via [Apache Camel Components](https://camel.apache.org/components/latest/index.html) we can support
other middleware too. Let's take a look at the following examples of usage.

Example of a contract connecting to a real RabbitMQ instance:

```yaml
description: 'Send a pong message in response to a ping message'
label: 'standalone_ping_pong'
input:
  triggeredBy: 'triggerMessage("ping_pong")'
outputMessage:
  sentTo: 'rabbitmq:output'
  body:
    message: 'pong'
metadata:
  standalone:
    setup:
      options: rabbitmq:output?queue=output&routingKey=#
    outputMessage:
	  additionalOptions: routingKey=#&queue=output
```

You can read more about setting this up in [this PR](https://github.com/spring-cloud/spring-cloud-contract/pull/1472) under the `Documentation of the feature with standalone mode (aka with running middleware)` section.

## Messaging with Existing Middleware

Since it's extremely easy to start a docker image with a broker via [Testcontainers](https://testcontainers.org), we're suggesting to slowly migrate your messaging tests to such an approach. From the perspective of Spring Cloud Contract it's also better since we won't need to replicate in our code the special cases of how frameworks behave when calling a real broker. Here you can find an example of how you can connect to a JMS broker on [the producer side](https://github.com/spring-cloud-samples/spring-cloud-contract-samples/tree/master/producer_jms_middleware) and [here how you can consume it](https://github.com/spring-cloud-samples/spring-cloud-contract-samples/tree/master/producer_jms_middleware).

## Gradle Plugin rewrite

This one is fully done by the one and only [shanman190](https://github.com/shanman190). The whole work on the Gradle plugin was done by him so you should buy him a beer once you get to see him :) Anyways, there are various changes to the Gradle plugin that you can check out.

* [Disable the stubs jar publication by default](https://github.com/spring-cloud/spring-cloud-contract/pull/1464)
* [Attempt to keep Kotlin off the classpath](https://github.com/spring-cloud/spring-cloud-contract/pull/1558)

## Stay in touch!

In case of any questions don't hesitate to ping us

* On [Github](https://github.com/spring-cloud/spring-cloud-contract/)
* On [Gitter](https://gitter.im/spring-cloud/spring-cloud-contract)
* On [Stackoverflow](https://stackoverflow.com/questions/tagged/spring-cloud-contract)