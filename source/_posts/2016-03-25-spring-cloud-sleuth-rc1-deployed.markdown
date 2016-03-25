---
layout: post
title: "Spring Cloud Sleuth RC1 deployed"
date: 2016-03-25 14:18:22 +0100
comments: true
categories:
- spring-cloud
- spring-cloud-sleuth
---

On the 24.03.2016 we've managed to move our release train called Brixton to the next station: RC1.
I'm really happy about this cause it cost us a lot of energy but it was worth it!

I'm recently mostly focusing on the [Spring Cloud Sleuth project](cloud.spring.io/spring-cloud-sleuth/spring-cloud-sleuth.html) and actually quite gigantic changes happened there since the M5 release. In this short post I'll show you the rationale and describe briefly the features related to span naming and customizations related to span propagation.

<!-- more -->

## What is Spring Cloud Sleuth?

For those who don't know what Spring Cloud Sleuth is - it's a library that implements a distributed tracing solution for Spring Cloud. You can check its code at [Github](https://github.com/spring-cloud/spring-cloud-sleuth).

We're also trying to be aligned with the concepts, terminology and approaches present in the [OpenTracing Project](http://opentracing.io/).

## Distributed tracing terminology

I'll quote the [documentation](cloud.spring.io/spring-cloud-sleuth/spring-cloud-sleuth.html) to present some of the basic concepts of distributed tracing.

> *Span*: The basic unit of work. For example, sending an RPC is a new span, as is sending a response to an RPC. Span’s are identified by a unique 64-bit ID for the span and another 64-bit ID for the trace the span is a part of. Spans also have other data, such as descriptions, timestamped events, key-value annotations (tags), the ID of the span that caused them, and process ID’s (normally IP address).
>
> Spans are started and stopped, and they keep track of their timing information. Once you create a span, you must stop it at some point in the future.
>
>*Trace*: A set of spans forming a tree-like structure. For example, if you are running a distributed big-data store, a trace might be formed by a put request.
>
>*Annotation*: is used to record existence of an event in time. Some of the core annotations used to define the start and stop of a request are:
>
> - *cs* - Client Sent - The client has made a request. This annotation depicts the start of the span.
>
> - *sr* - Server Received - The server side got the request and will start processing it. If one subtracts the cs timestamp from this timestamp one will receive the network latency.
>
> - *ss* - Server Sent - Annotated upon completion of request processing (when the response got sent back to the client). If one subtracts the sr timestamp from this timestamp one will receive the time needed by the server side to process the request.
>
> - *cr* - Client Received - Signifies the end of the span. The client has successfully received the response from the server side. If one subtracts the cs timestamp from this timestamp one will receive the whole time needed by the client to receive the response from the server.

Ok since now we're on the same page with the terminology let's see what's new in Sleuth.

## Span creation and naming

### Rationale

A really big problem that is there in the distributed tracing world is the issue related to naming spans. Actually that topic can be looked at from two angles.

First one is related to what the name of the span should look like. Should be a long and descriptive name or quite the contrary? As we write in the documentation:

> The name should be low cardinality (e.g. not include identifiers).

Finding the name for the span is not that big of a problem from library's perspective. You just pass on to a span whatever the user provides. But what about the situations in which some operation is deferred in time? Or scheduled at certain intervals?

Second one is related to a bigger issue: for the sake of consistency of passing tracing data, should we enforce creating spans? Should we be *eager* with that or allow the user to control span creation? Cause in that way we can have a problem how to name this artificial instance.

For RC1 we've decided that we will be eager in creating span names - but [we will come back to the topic in the future releases](https://github.com/spring-cloud/spring-cloud-sleuth/issues/180).

### Naming spans

Ok so we know the why, now let's move to the how... There is quite a lot of instrumentation going on in Sleuth so sometimes the names of spans could sound artificial (e.g. *async* for asynchronous operations). When talking about runnables and callables often you're dealing with code similar to this one:

```
Runnable runnable = new Runnable() {
	@Override public void run() {
		// perform logic
	}
});
Future<?> future = executorService.submit(runnable);
// ... some additional logic ...
future.get();
```

What the `Runnable` is an operation that you would like to wrap in a span. What should be the name of that span? How can you pass it to the `Tracer` so that the span name is set?

To answer those issues we've introduced two approaches

- a `@SpanName` annotation for an explicit class that implements `Runnable` or `Callable`
- `toString()` method resolution of an anonymous instance of either of those interfaces

Most likely in the future releases `@SpanName` or its modification will be used more heavily to provide explicit names of spans.

Anyways examples could look like those in the documentation. Example for `@SpanName` annotated class:

```
@SpanName("calculateTax")
class TaxCountingRunnable implements Runnable {

	@Override public void run() {
		// perform logic
	}
}
```

and an anonymous instance:

```
new TraceRunnable(tracer, spanNamer, new Runnable() {
	@Override public void run() {
		// perform logic
	}

	@Override public String toString() {
		return "calculateTax";
	}
});
```

Both will have the same span name. Remember that both `Runnables` should be wrapped in a `TraceRunnable` instance.

### Customization of span propagation

It's pretty obvious that there's a lot of companies that have already created some form of distributed tracing instrumentation. In Spring Cloud Sleuth we're expecting the tracing headers to be containing certain names like `X-B3-TraceId` for the trace id containing headers or `X-B3-SpanId` for the span related one.

One of the first issues that we've created was related to [support configurable header names](https://github.com/spring-cloud/spring-cloud-sleuth/issues/19) but actually we've developed it quite late. Anyways with RC1 it's possible to customize Sleuth in such a way that it's compatible with your system's nomenclature. Let's define two terms before we go any further - `Injector` and `Extractor`.

#### Injectors

In Spring Cloud Sleuth an `Injector` is actually a functional interface called `SpanInjector`. It has the following method:

```
void inject(Span span, T carrier);
```

Its purpose is to take whatever is necessary from a `span` and
inject it to the `carrier`. Let's assume that in your system you don't set the headers for trace id with the name `X-B3-TraceId` but you call it `correlationId` and `mySpanId` for `X-B3-SpanId`. Then you would have to override the behavior of Sleuth by registering a custom implementation of the `SpanInjector`. Let's look at the following snippets from the documentation:

```
class CustomHttpServletResponseSpanInjector implements SpanInjector<HttpServletResponse> {

	@Override
	public void inject(Span span, HttpServletResponse carrier) {
		carrier.addHeader("correlationId", Span.idToHex(span.getTraceId()));
		carrier.addHeader("mySpanId", Span.idToHex(span.getSpanId()));
		// inject the rest of Span values to the header
	}
}
```

Note that this approach will work with Zipkin only if your values that you're passing are Zipkin-compatible. That means that the IDs are 64bit numbers.

Also you may wonder why do we convert values using `Span.idToHex`. We've decided that we want the values of ids in the logs and in the message headers to be the very same values as the one that you can later see in Zipkin. That way you can just copy the value and put it into Zipkin to debug your system.

Once you have the `SpanInjector` you have to register it as a bean with `@Primary` annotation as presented below:

```
@Bean
@Primary
SpanInjector<HttpServletResponse> customHttpServletResponseSpanInjector() {
	return new CustomHttpServletResponseSpanInjector();
}
```

#### Extractors

In Spring Cloud Sleuth an `Extractor` is actually a functional interface called `SpanExtractor`. It has the following method:

```
Span joinTrace(T carrier);
```

Its purpose it to create a Span from the provided carrier. Let's have the same assumption as with the `SpanInjector` and let's consider a case where traceId header is named `correlationId` and spanId header is `mySpanId`. Then we customize the Spring context by providing our own implementation of the `SpanExtractor`:

```
class CustomHttpServletRequestSpanExtractor implements SpanExtractor<HttpServletRequest> {

	@Override
	public Span joinTrace(HttpServletRequest carrier) {
		long traceId = Span.hexToId(carrier.getHeader("correlationId"));
		long spanId = Span.hexToId(carrier.getHeader("mySpanId"));
		// extract all necessary headers
		Span.SpanBuilder builder = Span.builder().traceId(traceId).spanId(spanId);
		// build rest of the Span
		return builder.build();
	}
}
```

Again note that we're considering that the values are Zipkin compatible (64bit values for ids). Also note that we've assumed that the ids are sent in a hexadecimal form like they are presented in the Zipkin UI. That's why we used the `Span.hexToId` method to convert it back to long again.

## Summary

In this very short post you could see two quite big features available in the RC1 release. You can check [Spring Cloud Sleuth documentation](http://cloud.spring.io/spring-cloud-sleuth/spring-cloud-sleuth.html) for more information about the integrations and configurations of Sleuth. Actually you can check all the things that have changed in the RC1 release by checking the [closed issues](https://github.com/spring-cloud/spring-cloud-sleuth/issues?q=is%3Aissue+is%3Aclosed+milestone%3A1.0.0.RC1) and [merged PRs](https://github.com/spring-cloud/spring-cloud-sleuth/pulls?q=is%3Apr+milestone%3A1.0.0.RC1+is%3Aclosed).

In case of any questions do not hesitate to ping us on the [Gitter channel](https://gitter.im/spring-cloud/spring-cloud-sleuth) or [file an issue on Github](https://github.com/spring-cloud/spring-cloud-sleuth/issues).
