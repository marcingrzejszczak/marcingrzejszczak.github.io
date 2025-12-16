---
title: "The Story Of Micrometer Observation"
summary: "In this blog post we'll look into why and how Micrometer Observation was founded"
date: 2025-12-07

authors:
  - me

tags:
  - Micrometer
  - Micrometer Docs Generator

content_meta:
  trending: false
---
> [!NOTE]
> Before you read on, I'd be grateful if you could spend a moment filling out a short survey for my upcoming Java Microservices with Spring course: https://maven.com/forms/bbafe1.

In this article, I want to talk about a problem every mature codebase eventually faces:  
**how do we instrument existing business logic without rewriting it?**

Whether you want to add:

- business metrics
- distributed tracing
- logs enriched with correlation IDs
- or even custom JFR events

…instrumentation can leak in a very intrusive and hard to support way.

We'll look at how [Micrometer Observation](https://docs.micrometer.io/micrometer/reference/observation.html) addresses this problem, and I'll also share some behind-the-scenes history from when we designed the API. I'm very proud to have been part of that effort - and this is the story of how it came to be.

<!-- more -->

#### Instrumenting Your Codebase

When looking at a piece of "business logic," the question is:  
**How do we wrap it with observability concerns without polluting it?**

```java
class TaxService {
    // Pure business logic - no instrumentation
    public BigDecimal calculateTax(Invoice invoice) {
        return invoice.amount().multiply(new BigDecimal("0.23"));
    }
}
```

To instrument this, a common approach is to introduce an interface and wrap the business logic with:

- tracing
- metrics
- logging

Then compose them in the correct order:

- First: start a span (metrics exemplars depend on it)
- Then: record a timer
- Finally: log with enriched trace/span IDs

This quickly becomes repetitive and error-prone. And the moment you need a new type of instrumentation (e.g., JFR events), the business setup code must change again.

In 2021, we decided there had to be a better way.

---

#### Micrometer + Spring Cloud Sleuth FTW?

If we're working on the JVM, then for metrics, we would definitely pick [Micrometer](https://micrometer.io) - a good abstraction over various metrics providers. What about distributed tracing? If we have a Spring Boot app most likely we would pick [Spring Cloud Sleuth](https://github.com/spring-cloud/spring-cloud-sleuth/). The problem is that we are then forced to use the Spring Cloud BOM (even for a monolith). What if our application is not a Spring application? Why would we need to add Spring in order to use a distributed tracing solution? An option there would be to use [OpenZipkin Brave](https://github.com/openzipkin/brave) and set things up manually.

Let us now check how the approach explained above could be implemented using this technology stack.

```java
// PSEUDOCODE - it should compile though 🫣

interface TaxCalculator {
    BigDecimal calculate(Invoice invoice);
}

class DefaultTaxCalculator implements TaxCalculator {
    public BigDecimal calculate(Invoice invoice) {
        return invoice.amount().multiply(new BigDecimal("0.23"));
    }
}

class MetricsTaxCalculator implements TaxCalculator {

    private final TaxCalculator delegate;
    private final MeterRegistry registry;

    MetricsTaxCalculator(TaxCalculator delegate, MeterRegistry registry) {
        this.delegate = delegate;
        this.registry = registry;
    }

    public BigDecimal calculate(Invoice invoice) {

        // Low-cardinality tags for metrics
        String country = invoice.country();
        String customerType = invoice.customerType();
        String invoiceType = invoice.type();

        Timer.Sample sample = Timer.start(registry);
        try {
            return delegate.calculate(invoice);
        } finally {
            sample.stop(
                Timer.builder("tax.calculation.time")
                    .tag("country", country)
                    .tag("customer_type", customerType)
                    .tag("invoice_type", invoiceType)
                    .register(registry)
            );
        }
    }
}

class TracingTaxCalculator implements TaxCalculator {

    private final TaxCalculator delegate;
    private final Tracer tracer;

    TracingTaxCalculator(TaxCalculator delegate, Tracer tracer) {
        this.delegate = delegate;
        this.tracer = tracer;
    }

    public BigDecimal calculate(Invoice invoice) {

        // Low-cardinality tags (same as metrics)
        String country = invoice.country();
        String customerType = invoice.customerType();
        String invoiceType = invoice.type();

        // High-cardinality tags (tracing only)
        String invoiceId = invoice.id();
        String customerId = invoice.customerId();

        Span span = tracer.nextSpan()
                .name("tax.calculate")
                // Low-cardinality (same as metrics)
                .tag("country", country)
                .tag("customer_type", customerType)
                .tag("invoice_type", invoiceType)
                // High-cardinality (tracing only)
                .tag("invoice_id", invoiceId)
                .tag("customer_id", customerId)
                .start();

        try (Tracer.SpanInScope ws = tracer.withSpan(span)) {
            return delegate.calculate(invoice);
        } finally {
            span.end();
        }
    }
}

class LoggingTaxCalculator implements TaxCalculator {

    private final TaxCalculator delegate;
    private static final Logger log = LoggerFactory.getLogger(LoggingTaxCalculator.class);

    LoggingTaxCalculator(TaxCalculator delegate) {
        this.delegate = delegate;
    }

    public BigDecimal calculate(Invoice invoice) {
        log.info("Calculating tax for {}", invoice.id());
        try {
            BigDecimal result = delegate.calculate(invoice);
            log.info("Tax result: {}", result);
            return result;
        } catch (Exception ex) {
            log.error("Error calculating tax", ex);
            throw ex;
        }
    }
}

// Stitching it all together
TaxCalculator calculator =
        new TracingTaxCalculator(
            new MetricsTaxCalculator(
                new LoggingTaxCalculator(
                    new DefaultTaxCalculator(),
                    meterRegistry),
                tracer));
```

---

#### The Problems

What immediately strikes us is the number of repetitions in the code. For instance starting and stopping the timer and a span are very similar. Look at the tags, the values are almost the same.

What else can we see here? We have both code instrumentation (where we start and stop the measurement) mixed with setting of names and additional metadata (tags).

Another problem, especially when you're not using dependency injection, is the need for modifying the business code setup in case of the need to add another instrumentation. Imagine that you want to emit a JFR event before and after the business code was executed. You need to create the implementation and add the wrapping mechanism to the existing object construction. In other words your object setup must know that it needs to use metrics, tracing, logging and JFR integration.

How can we solve these problems? Or rather how we at the Micrometer team decided to solve them back in 2021?

---

#### The 2021 Solution

2021 was a wild year globally - vaccines, *Squid Game*, billionaires in space - and, for us on the Spring Observability team, it was also the year we tried to fix observability instrumentation once and for all 😄

First, we thought that there's no point in reinventing the wheel and started to look if there were any existing solutions that would solve our problem. Unfortunately, there were none (or at least we couldn't find them). The important factor was also that we wanted to be able to decide how the project should shape in the future.

Next, we came to the conclusion that we would need a **dedicated API** that would treat observability as a **first-class citizen**. I remember that we had a lot of discussions about what to call the "thing that we're watching or trying to measure". If my memory serves me right it was [**Greg Turnquist**](https://github.com/gregturn) who came up with the idea of calling it an **"observation"**.

Another problem we wanted to solve was to **separate instrumenting from naming and tagging**.  
The amount of code added to the business logic to introduce observability was supposed to be minimal. Also, introducing new behaviour needed to be a configuration concern. In other words:  
**Business code should not be modified if new observability behaviour was to be added.**

We believed that the best documentation comes from the code. What we wanted to build was a solution that would automatically generate documentation from the observability code. You can check my article to learn more about the [Micrometer Docs Generator - a hidden gem](https://toomuchcoding.com/post/2025-10-18-micrometer-docs-gen-hidden-gem/).

Last but not least - the library was **not supposed to depend on Spring Framework at all**. We wanted to build something completely Spring-agnostic.

This is how [**Micrometer Observation**](https://docs.micrometer.io/micrometer/reference/observation.html) was born - a library that solves all of the aforementioned problems.

Let's now look at how the same code example would look when **Micrometer Observation** is used.

---

```java
// PSEUDOCODE - it should compile though 🫣

// Dedicated custom Observation Context
class TaxCalculationContext extends Observation.Context {

    private Invoice invoice;

    public TaxCalculationContext invoice(Invoice invoice) {
        this.invoice = invoice;
        return this;
    }

    public Invoice invoice() {
        return invoice;
    }
}

// Blueprint of observations - from this we can generate docs using Micrometer Docs Generator
enum TaxObservationDocumentation implements ObservationDocumentation {

    TAX_CALCULATION {
        @Override
        public String getName() {
            return "tax.calculate";
        }

        @Override
        public KeyName[] getLowCardinalityKeyNames() {
            return LowCardinalityKeys.values();
        }

        @Override
        public KeyName[] getHighCardinalityKeyNames() {
            return HighCardinalityKeyNames.values();
        }

        @Override
        public Class<? extends ObservationConvention<? extends Observation.Context>> 
                getDefaultConvention() {
            return DefaultTaxCalculationConvention.class;
        }

        enum LowCardinalityKeys implements KeyName {
            COUNTRY { public String asString() { return "country"; }},
            CUSTOMER_TYPE { public String asString() { return "customer_type"; }},
            INVOICE_TYPE { public String asString() { return "invoice_type"; }}
        }

        enum HighCardinalityKeyNames implements KeyName {
            INVOICE_ID { public String asString() { return "invoice_id"; }},
            CUSTOMER_ID { public String asString() { return "customer_id"; }}
        }
    }
}

// Convention: separates naming + tagging from instrumentation code
class DefaultTaxCalculationConvention
        implements ObservationConvention<TaxCalculationContext> {

    @Override
    public boolean supportsContext(Observation.Context context) {
        return context instanceof TaxCalculationContext;
    }

    @Override
    public String getName() {
        return TaxObservationDocumentation.TAX_CALCULATION.getName();
    }

    @Override
    public String getContextualName(TaxCalculationContext ctx) {
        // Example: contextual name derived from domain logic
        return "tax." + ctx.invoice().type();
    }

    @Override
    public KeyValues getLowCardinalityKeyValues(TaxCalculationContext ctx) {
        Invoice invoice = ctx.invoice();
        return KeyValues.of(
                TaxObservationDocumentation.TAX_CALCULATION
                        .LowCardinalityKeys.COUNTRY.withValue(invoice.country()),
                TaxObservationDocumentation.TAX_CALCULATION
                        .LowCardinalityKeys.CUSTOMER_TYPE.withValue(invoice.customerType()),
                TaxObservationDocumentation.TAX_CALCULATION
                        .LowCardinalityKeys.INVOICE_TYPE.withValue(invoice.type())
        );
    }

    @Override
    public KeyValues getHighCardinalityKeyValues(TaxCalculationContext ctx) {
        Invoice invoice = ctx.invoice();
        return KeyValues.of(
                TaxObservationDocumentation.TAX_CALCULATION
                        .HighCardinalityKeyNames.INVOICE_ID.withValue(invoice.id()),
                TaxObservationDocumentation.TAX_CALCULATION
                        .HighCardinalityKeyNames.CUSTOMER_ID.withValue(invoice.customerId())
        );
    }
}

// Business logic using Observation directly
class DefaultTaxCalculator implements TaxCalculator {

    private final ObservationRegistry registry;

    DefaultTaxCalculator(ObservationRegistry registry) {
        this.registry = registry;
    }

    @Override
    public BigDecimal calculate(Invoice invoice) {
        TaxCalculationContext context = new TaxCalculationContext().invoice(invoice);
		return  TaxObservationDocumentation.TAX_CALCULATION
			// For simplicity's sake we're not allowing injection of a custom convention, that's why we have twice the same argument
            .observation(new DefaultTaxObservationConvention(), new DefaultTaxObservationConvention(), () -> context,
                    registry)
            // Run the actual logic you want to observe
            .observe(() -> invoice.amount().multiply(new BigDecimal("0.23")));
    }
}

// Simple logging handler
class TaxLoggingHandler implements ObservationHandler<TaxCalculationContext> {

    private static final Logger log =
            LoggerFactory.getLogger(TaxLoggingHandler.class);

    @Override
    public boolean supportsContext(Observation.Context context) {
        return context instanceof TaxCalculationContext;
    }

    @Override
    public void onStart(TaxCalculationContext ctx) {
        log.info("Starting TAX calculation for invoice {} (type={}, country={})",
                ctx.invoice().id(),
                ctx.invoice().type(),
                ctx.invoice().country());
    }

    @Override
    public void onError(TaxCalculationContext ctx) {
        log.error("TAX calculation failed for invoice {}", ctx.invoice().id(), ctx.getError());
    }

    @Override
    public void onStop(TaxCalculationContext ctx) {
        log.info("Finished TAX calculation for invoice {}", ctx.invoice().id());
    }
}

// Setting up observability concerns
ObservationRegistry registry = ObservationRegistry.create();
registry.observationConfig()
        // DefaultTracingObservationHandler - Comes from Micrometer Tracing - knows how to handle tracing        
        .observationHandler(new DefaultTracingObservationHandler(tracer))
        // DefaultMeterObservationHandler - Comes from Micrometer Core - knows how to handle metrics
        // TracingAwareMeterObservationHandler - Comes from Micrometer Tracing - knows how to handle exemplars
        .observationHandler(new TracingAwareMeterObservationHandler(new DefaultMeterObservationHandler(meterRegistry), tracer))
        .observationHandler(new TaxLoggingHandler());

// Business code setup
TaxCalculator calc = new DefaultTaxCalculator(registry);
```

---

Notice how the code concerns get completely separated:

- We have a **custom observation context**, a mutable holder of values.
- We have an **observation convention** that defines how to set concrete key-value pairs.  
  Low cardinality will end up in metrics and spans, high cardinality will end up in spans, and for logs it is up to us to decide.
- We have **observation documentation** that describes how the observations will look like. Then, the [Micrometer docs generator project](https://toomuchcoding.com/post/2025-10-18-micrometer-docs-gen-hidden-gem/) can scan the sources to produce the observability documentation.
- Last but not least - we simply **inject the ObservationRegistry** into the business code and wrap the business logic once.  
  Adding behavior (metrics, tracing, custom logging) becomes a **configuration problem**, completely outside of the business logic.

We could further simplify this example and inline setting of key values inside the business code but I wanted to show the example of full sepration of concerns.

Let's now look into some history - how was the Micrometer Observation API forged?

---

#### The Forging of Micrometer Observation API

I can joke that *"I was there Gandalf. I was there 3000 years ago"* when the first version of the API was created. In reality, it started much more humbly: [**Jonatan Ivanov**](https://github.com/jonatan-ivanov) built an initial prototype as a side project, outside of the Micrometer codebase.

We experimented with different locations in the Micrometer/Spring repos. At one point [**Spencer Gibb**](https://github.com/spencergibb) suggested putting it directly into Micrometer. [**Tommy Ludwig**](https://github.com/shakuzen/), the Micrometer lead, had understandable concerns - but after several discussions we reached consensus:

> **Observation would become a first-class module in Micrometer.**

The choice of the exact place in that repo was the next hurdle to tackle.

In the [micrometer-core](https://github.com/micrometer-metrics/micrometer/tree/v1.16.0/micrometer-core) we already had certain abstractions like `Tags` that we wanted to reuse in Micrometer Observation. Putting the Observation API in `core` would be a problem - it would live in the same JAR as metrics do. That means that from a classpath perspective, observation and metrics would always be brought together. From Spring Boot's perspective (which configures features by looking at the classpath), it would be impossible to know whether the user wanted observation and/or metrics.

We tried moving common abstractions around the repo - **hundreds of files changed**, binary compatibility broke constantly, and every attempt caused new headaches.

Eventually we made a pragmatic decision:

> **Duplicate some classes (e.g. Tag → KeyValue) to preserve decoupling.**

The [new micrometer-observation module](https://github.com/micrometer-metrics/micrometer/tree/v1.16.0/micrometer-observation) had almost zero dependencies and was extremely small - a prerequisite for Spring Framework to consider putting it on the classpath.

Back at that time we were working on **Spring Framework 6** and **Spring Boot 3**. These projects already had huge themes underway: JDK17 baseline, Jakarta migration, AOT support. After discussions across teams we decided to introduce another major theme: **Observability**. Because why not 😃

However, due to the sheer amount of work happening simultaneously, teams often gave feedback very late. They pulled new Micrometer snapshots at the end of each sprint - which meant if we introduced a breaking change, we could unintentionally block an entire team until they adjusted. Stress levels were high.  
Welcome to the life of OSS maintainers 🫠

But one of the great things about the Micrometer and Spring teams is that **feedback is always welcome**, even if it means changing direction by 180 degrees. One example was when [**Brian Clozel**](https://github.com/bclozel/) proposed a completely different approach to treating observation contexts for remote calls. We rewrote that part based on his feedback - and it turned out to be the right call.

After countless iterations, experiments, reviews, and rewrites across multiple Spring projects, the API finally stabilized. And the greatest validation is that I don't recall any issues caused by ambiguity in the Observation API. That means we probably did our job right 🙂

If you want to learn more about Micrometer Observation just read the [docs](https://docs.micrometer.io/micrometer/reference/observation.html). If you feel there's anything missing, please file an issue - or even better, a pull request. The [Micrometer team](https://github.com/orgs/micrometer-metrics/people) will be more than happy to help.

---

#### Summary

In this article we've managed to look at what kind of problems Micrometer Observation solves and what was the history behind its founding. You could check some of the behind the scenes of how OSS libraries are maintained and how cross-cutting decisions are made across the ecosystem.

**BTW:** I'm gathering feedback before running a *Java Microservices with Spring* course.  
I will be very grateful if you spend a couple of minutes to fill out the survey:  
👉 https://maven.com/forms/bbafe1
