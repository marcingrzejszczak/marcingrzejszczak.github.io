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

<!-- more -->

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

With the thin jar rewrite and [this PR](https://github.com/spring-cloud/spring-cloud-contract/pull/1472) we're adding support for Kafka and AMQP based solutions with the Docker images. 

// TODO: Continue here

## Gradle Plugin rewrite

This one is fully done by the one and only [shanman190](https://github.com/shanman190). The whole work on the Gradle plugin was done by him so you should buy him a beer once you get to see him :) Anyways, there are various changes to the Gradle plugin that you can check out.

* [Disable the stubs jar publication by default](https://github.com/spring-cloud/spring-cloud-contract/pull/1464)