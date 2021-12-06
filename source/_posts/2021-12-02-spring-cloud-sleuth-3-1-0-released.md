---
layout: post
title: Spring Cloud Sleuth 3.1.0 released
date: 2021-12-02 09:30 +0100
comments: true
categories: articles
tags:
- observability
- tracing
- Spring Cloud Sleuth
- OpenZipkin
- Brave
- OpenTelemetry
- Tanzu Observability by Wavefront
---

With the release of the Spring Cloud 2021.0.0 (aka Jubilee) release train we're more than happy to announce the general availability of Spring Cloud Sleuth 3.1.0. In this blog post I'll describe the most notable released features.

<!--more-->

Here is the list of most notable features, I'll elaborate on them in the subsequent parts of this post.

* JDBC [#1930](https://github.com/spring-cloud/spring-cloud-sleuth/issues/1930)
* Tomcat Valve [#1329](https://github.com/spring-cloud/spring-cloud-sleuth/issues/1329)
* Spring Vault [#1952](https://github.com/spring-cloud/spring-cloud-sleuth/issues/1952)
* Automatic tag table generation for documentation [#1950](https://github.com/spring-cloud/spring-cloud-sleuth/issues/1950)
* Spring Cloud Deployer [#1947](https://github.com/spring-cloud/spring-cloud-sleuth/issues/1947)
* R2DBC [#1524](https://github.com/spring-cloud/spring-cloud-sleuth/issues/1524)
* Kafka [#2013](https://github.com/spring-cloud/spring-cloud-sleuth/issues/2013) and Reactor Kafka [#1708](https://github.com/spring-cloud/spring-cloud-sleuth/issues/1708)
* Spring TX [#1941](https://github.com/spring-cloud/spring-cloud-sleuth/issues/1941)
* Spring Batch [#1904](https://github.com/spring-cloud/spring-cloud-sleuth/issues/1904)
* RSocket [#1677](https://github.com/spring-cloud/spring-cloud-sleuth/issues/1677)
* Spring Cloud Task [#1903](https://github.com/spring-cloud/spring-cloud-sleuth/issues/1903)
* Spring Cloud Config [#1915](https://github.com/spring-cloud/spring-cloud-sleuth/issues/1915)
* Spring Cloud CircuitBreaker Reactive [#1910](https://github.com/spring-cloud/spring-cloud-sleuth/issues/1910)
* Cassandra [#1974](https://github.com/spring-cloud/spring-cloud-sleuth/issues/1974)
* Spring Session [#1961](https://github.com/spring-cloud/spring-cloud-sleuth/issues/1961)
* Spring Security [#2011](https://github.com/spring-cloud/spring-cloud-sleuth/issues/2011)
* Prometheus Exemplars [#2039](https://github.com/spring-cloud/spring-cloud-sleuth/issues/2039)
* Spring Cloud Stream Reactive [#2038](https://github.com/spring-cloud/spring-cloud-sleuth/issues/2038)
* Reactive Mongo [#2044](https://github.com/spring-cloud/spring-cloud-sleuth/issues/2044)
* Abstracted Redis instrumentation [#2046](https://github.com/spring-cloud/spring-cloud-sleuth/issues/2046)
* Custom Actuator for storing traces [#1879](https://github.com/spring-cloud/spring-cloud-sleuth/issues/1879)

## JDBC

We’re decorating `DataSource`s in a trace representation. We delegate actual proxying to either [p6spy](https://github.com/p6spy/p6spy) or [datasource-proxy](https://github.com/ttddyy/datasource-proxy). In order to use this feature you need to have them on the classpath.

![JDBC example](/images/sleuth_3_1_0/jdbc.jpeg)

Check the [docs](https://docs.spring.io/spring-cloud-sleuth/docs/current/reference/html/integrations.html#sleuth-jdbc-integration) for more details.

[Here](https://github.com/spring-cloud-samples/spring-cloud-sleuth-samples/tree/main/data) you can find a sample that depicts this integration.

## Tomcat Valve

The main driver behind this change is that up till Sleuth 3.1 your Tomcat logs wouldn't have any tracing information in it. With this change we're instrumenting Tomcat, so we're far earlier in the lifecycle of the request and we can instrument all possible logs with tracing information.

![MVC example](/images/sleuth_3_1_0/mvc.jpeg)

Check the [docs](https://docs.spring.io/spring-cloud-sleuth/docs/current/reference/html/integrations.html#sleuth-tomcat-integration) for more details.

[Here](https://github.com/spring-cloud-samples/spring-cloud-sleuth-samples/tree/main/mvc) you can find a sample that depicts this integration.

## Spring Vault

We’re instrumenting the `RestTemplate` or `WebClient` instances used by Spring Vault to communicate with Vault.

![Vault example](/images/sleuth_3_1_0/vault.jpeg)

Check the [docs](https://docs.spring.io/spring-cloud-sleuth/docs/current/reference/html/integrations.html#sleuth-vault-integration) for more details.

[Here](https://github.com/spring-cloud-samples/spring-cloud-sleuth-samples/tree/main/vault-resttemplate) you can find a sample that depicts this integration for `RestTemplate`.

[Here](https://github.com/spring-cloud-samples/spring-cloud-sleuth-samples/tree/main/vault-webclient) you can find a sample that depicts this integration for `WebClient`.

## Automatic tag table generation for documentation

There's nothing more frustrating in the tracing world than creating a span and not starting it, or not stopping it. We had enough of such situations... Another challenge we faced was that we didn't even remember any longer how many spans we create, what their names are and how many tags / events are being set on them.

We've decided to alter the way we code things in Sleuth so that we can introduce certain automation. We are instrumenting the code once, however we are able to turn on at the test time additional assertions that verify if spans were started and stopped etc. Also at the documentation build time we parse the sources and build a table of spans together with some details.

You can check the [docs](https://docs.spring.io/spring-cloud-sleuth/docs/current/reference/html/appendix.html#sleuth-spans) to see the results of that automation.

## Spring Cloud Deployer

If you have [Spring Cloud Deployer](https://github.com/spring-cloud/spring-cloud-deployer) running on the classpath, we wrap the `AppDeployer` in a trace representation. We are polling the application for its status at a default interval.

![Deployer example](/images/sleuth_3_1_0/deployer.jpeg)

Check the [docs](https://docs.spring.io/spring-cloud-sleuth/docs/current/reference/html/integrations.html#sleuth-deployer-integration) for more details.

[Here](https://github.com/spring-cloud-samples/spring-cloud-sleuth-samples/tree/main/deployer) you can find a sample that depicts this integration.

## R2DBC

If you have [R2DBC Proxy](http://r2dbc.io/r2dbc-proxy/docs/current-snapshot/docs/html/) on the classpath we will instrument the `ConnectionFactory` so that it contains a custom `ProxyExecutionListener`. 

![R2DBC example](/images/sleuth_3_1_0/r2dbc.jpeg)

Check the [docs](https://docs.spring.io/spring-cloud-sleuth/docs/current/reference/html/integrations.html#sleuth-r2dbc-integration) for more details.

[Here](https://github.com/spring-cloud-samples/spring-cloud-sleuth-samples/tree/main/data-reactive) you can find a sample that depicts this integration.

## Kafka and Reactor Kafka

We decorate the Kafka clients (`KafkaProducer` and `KafkaConsumer`) to create a span for each event that is produced or consumed. We also provide `TracingKafkaProducerFactory` and `TracingKafkaConsumerFactory` to be used with the Reactor Kafka clients (`KafkaSender` and `KafkaReceiver`, respectively). Additionally, we decorate any Spring Kafka `ProducerFactory` and `ConsumerFactory` available in the context.

![Kafka example](/images/sleuth_3_1_0/reactive-kafka.jpeg)

Check the [docs](https://docs.spring.io/spring-cloud-sleuth/docs/current/reference/html/integrations.html#sleuth-kafka-integration) for more details.

[Here](https://github.com/spring-cloud-samples/spring-cloud-sleuth-samples/tree/main/kafka-producer) you can find a sample that depicts this integration for Kafka producer.

[Here](https://github.com/spring-cloud-samples/spring-cloud-sleuth-samples/tree/main/kafka-consumer) you can find a sample that depicts this integration for Kafka consumer.

[Here](https://github.com/spring-cloud-samples/spring-cloud-sleuth-samples/tree/main/kafka-reactive-producer) you can find a sample that depicts this integration for Kafka Reactive producer.

[Here](https://github.com/spring-cloud-samples/spring-cloud-sleuth-samples/tree/main/kafka-reactive-consumer) you can find a sample that depicts this integration for Kafka Reactive consumer.

## Spring TX

If you have Spring Tx on the classpath we will instrument the `PlatformTransactionManager` and the `ReactiveTransactionManager` to create a span whenever a new transaction is created.

![TX example](/images/sleuth_3_1_0/jdbc.jpeg)

Check the [docs](https://docs.spring.io/spring-cloud-sleuth/docs/current/reference/html/integrations.html#sleuth-tx-integration) for more details.

[Here](https://github.com/spring-cloud-samples/spring-cloud-sleuth-samples/tree/main/data) you can find a sample that depicts this integration.

## Spring Batch

If you have Spring Batch running on the classpath, we wrap the `StepBuilderFactory` and the `JobBuilderFactory` to propagate the tracing context.

![Batch example](/images/sleuth_3_1_0/batch.jpeg)

Check the [docs](https://docs.spring.io/spring-cloud-sleuth/docs/current/reference/html/integrations.html#sleuth-batch-integration) for more details.

[Here](https://github.com/spring-cloud-samples/spring-cloud-sleuth-samples/tree/main/batch) you can find a sample that depicts this integration.

## RSocket

If you have Spring RSocket running on the classpath, we wrap the inbound and outbound communication to propagate the tracing context via the metadata

![RSocket example](/images/sleuth_3_1_0/rsocket.jpeg)

Check the [docs](https://docs.spring.io/spring-cloud-sleuth/docs/current/reference/html/integrations.html#sleuth-rsocket-integration) for more details.

[Here](https://github.com/spring-cloud-samples/spring-cloud-sleuth-samples/tree/main/rsocket-server) you can find a sample that depicts this integration for RSocket server.

[Here](https://github.com/spring-cloud-samples/spring-cloud-sleuth-samples/tree/main/rsocket-client) you can find a sample that depicts this integration for RSocket client.

## Spring Cloud Task

If you have Spring Cloud Task running on the classpath, we're instrumenting `TaskExecutionListener` and `CommandLineRunner` and `ApplicationRunner`.

![Task example](/images/sleuth_3_1_0/task.jpeg)

[Here](https://github.com/spring-cloud-samples/spring-cloud-sleuth-samples/tree/main/task) you can find a sample that depicts this integration.

## Spring Cloud Config

If you have Spring Cloud Config Server running on the classpath, we will wrap the `EnvironmentRepository` in a span.

![Config Server example](/images/sleuth_3_1_0/config-server.jpeg)

Check the [docs](https://docs.spring.io/spring-cloud-sleuth/docs/current/reference/html/integrations.html#sleuth-config-server-integration) for more details.

[Here](https://github.com/spring-cloud-samples/spring-cloud-sleuth-samples/tree/main/config-server) you can find a sample that depicts this integration.

## Spring Cloud CircuitBreaker Reactive

If you have Spring Cloud CircuitBreaker on the classpath, we will wrap the passed command `Supplier` and the fallback `Function` in its trace representations.

![CircuitBreaker Reactive example](/images/sleuth_3_1_0/reactive-circuitbreaker.jpeg)

Check the [docs](https://docs.spring.io/spring-cloud-sleuth/docs/current/reference/html/integrations.html#sleuth-circuitbreaker-integration) for more details.

[Here](https://github.com/spring-cloud-samples/spring-cloud-sleuth-samples/tree/main/circuitbreaker-reactive) you can find a sample that depicts this integration.

## Cassandra

If you have Spring Data Cassandra on the classpath, we’re instrumenting Casandra’s `CqlSession` and `ReactiveSession` interfaces and we’re providing our own implementation of the `RequestTracker`.

![Cassandra example](/images/sleuth_3_1_0/cassandra.jpeg)

Check the [docs](https://docs.spring.io/spring-cloud-sleuth/docs/current/reference/html/integrations.html#sleuth-cassandra-integration) for more details.

[Here](https://github.com/spring-cloud-samples/spring-cloud-sleuth-samples/tree/main/cassandra) you can find a sample that depicts this integration for non reactive Cassandra.

[Here](https://github.com/spring-cloud-samples/spring-cloud-sleuth-samples/tree/main/cassandra-reactive) you can find a sample that depicts this integration for reactive Cassandra.

## Spring Session

If you have Spring Session on the classpath, we’re instrumenting the `Session` repositories that wraps all operations in a span.

![Session example](/images/sleuth_3_1_0/session.jpeg)

Check the [docs](https://docs.spring.io/spring-cloud-sleuth/docs/current/reference/html/integrations.html#sleuth-session-integration) for more details.

[Here](https://github.com/spring-cloud-samples/spring-cloud-sleuth-samples/tree/main/session) you can find a sample that depicts this integration.

## Spring Security

If you have Spring Security on the classpath, we create an implementation of `SecurityContextChangedListener` that annotates a current span with an event when context has changed.

![Security example](/images/sleuth_3_1_0/security.jpeg)

[Here](https://github.com/spring-cloud-samples/spring-cloud-sleuth-samples/tree/main/security) you can find a sample that depicts this integration.

## Prometheus Exemplars

Exemplars are metadata attached to metrics (see: [Prometheus Exemplars](https://github.com/prometheus/client_java#exemplars)), e.g.: traceId so that you can get an example traceId for your metrics.

We're integrating Sleuth with Prometheus so that if somebody uses the Prometheus Java Client, they can get Exemplars using Sleuth. This issue is also connected to the Micrometer Exemplars Support, [see](https://github.com/micrometer-metrics/micrometer/issues/2672).

[Here](https://github.com/spring-cloud/spring-cloud-sleuth/issues/2039) you can read more about the issue.

## Spring Cloud Stream Reactive

Spring Cloud Sleuth can instrument Spring Cloud Function. Since Spring Cloud Stream uses Spring Cloud Function you will get the messaging instrumentation out of the box.

The way to achieve it is to provide a `Function` or `Consumer` or `Supplier` that takes in a `Message` as a parameter e.g. `Function<Message<String>, Message<Integer>>`.

If the type **is not** `Message` then instrumentation **will not** take place.

For a reactive `Consumer<Flux<Message<?>>>` remember to manually close the span and clear the context before you call `.subscribe()`.

![Stream reactive example](/images/sleuth_3_1_0/reactive-stream.jpeg)

[Here](https://github.com/spring-cloud-samples/spring-cloud-sleuth-samples/tree/main/stream-reactive-producer) you can find a sample that depicts this integration for the producer.

[Here](https://github.com/spring-cloud-samples/spring-cloud-sleuth-samples/tree/main/stream-reactive-consumer) you can find a sample that depicts this integration for the consumer.

## Reactive Mongo

We’re adding command listeners that wrap all commands in a span. It works both for the Reactive and non Reactive case.

![Reactive Mongo example](/images/sleuth_3_1_0/reactive-mongo.jpeg)

[Here](https://github.com/spring-cloud-samples/spring-cloud-sleuth-samples/tree/main/mongodb-reactive) you can find a sample that depicts this integration.

## Abstracted Redis instrumentation

With this [PR](https://github.com/spring-cloud/spring-cloud-sleuth/pull/2046) the Redis instrumentation can work for other Tracers, such as OpenTelemetry.

## Custom Actuator for storing traces

Spring Cloud Sleuth comes with a traces Actuator endpoint that can store finished spans. The endpoint can be queried either via an HTTP Get method to simply retrieve the list of stored spans or via HTTP Post method to retrieve the list and clear it.

Check the [docs](https://docs.spring.io/spring-cloud-sleuth/docs/current/reference/html/project-features.html#features-traces-actuator) for more details.

# Links

* [Spring Cloud Sleuth 3.1.0 docs](https://docs.spring.io/spring-cloud-sleuth/docs/3.1.0/reference/html/)
* [Spring Cloud Sleuth Samples](https://github.com/spring-cloud-samples/spring-cloud-sleuth-samples/)
* [Spring Cloud Sleuth OpenTelemetry project](https://github.com/spring-projects-experimental/spring-cloud-sleuth-otel/)
* [Spring Cloud Sleuth OpenTelemetry docs](https://spring-projects-experimental.github.io/spring-cloud-sleuth-otel/docs/current/reference/html/index.html)
* [Spring Cloud Sleuth 3.1.0 release notes](https://github.com/spring-cloud/spring-cloud-release/wiki/Spring-Cloud-2021.0-Release-Notes#spring-cloud-sleuth)

## Stay in touch!

In case of any questions don't hesitate to ping us

* On [Github](https://github.com/spring-cloud/spring-cloud-sleuth/)
* On [Gitter](https://gitter.im/spring-cloud/spring-cloud-sleuth)
* On [Stackoverflow](https://stackoverflow.com/questions/tagged/spring-cloud-sleuth)