---
title: "Issue #18: NIST Admits Defeat, CVE Chaos Continues"
date: 2026-04-18
lastmod: 2026-04-18
draft: false
description: "NIST limits CVE enrichment after 263% submission surge, Claude Opus 4.7 launches, Spring Framework 7 keeps shipping less code, and quantum computing gets the GPU treatment."
tags: ["newsletter"]
---

Hi!

This week, NIST threw in the towel on enriching every single CVE under the sun (263% more submissions since 2020 will do that), Claude Opus 4.7 is making developers look bad by being *way* too good at autonomous tasks, Spring Boot 4 continues its quest to eliminate all your boilerplate code (85% less, they claim), and NVIDIA launched quantum AI models because apparently we needed one more reason to be confused about what's cutting-edge. Oh, and Allbirds pivoted from selling sneakers to selling GPU time. I guess you could say their business model just got the *runs* it needed. (I apologize for that one. It's gruesome, and I'm keeping it anyway.)

In the TooMuchCoding.com world - there are 2 new blog posts. [One](/post/2026-04-16-stubborn/) related to creation of the new library / contract (like in Spring Cloud Contract) broker called [Stubborn.sh](https://stubborn.sh) and the [other](/post/2026-04-18-cto/) about the fact that in June I'm going to be a full-time CTO of the [Obenan.ai](https://obenan.ai) startup!

Grab a hot beverage.

Let's go. I do hope that you'll enjoy the reading!

---

# Shameless self-promotion

Learn how to build resilient distributed systems in the AI era. Check out my "Generate, Break, Fix: Distributed Systems in the AI Era" workshop on Maven. Use promo code TOOMUCHCODING for $100 off: https://maven.com/toomuchcoding/generate-break-fix-distributed-systems-in-the-ai-era

---

# TooMuchCoding.com

On my blog you can find 2 new articles.

[One](/post/2026-04-16-stubborn/) related to creation of the new library / contract (like in Spring Cloud Contract) broker called [Stubborn.sh](https://stubborn.sh) - STUBborn - get it? It allows you to have visual mapping of your contracts, CLIs / agents to know which application got checked against which version of the contract etc. Check the blog [post here](/post/2026-04-16-stubborn/).

[Second one](/post/2026-04-18-cto/) about the fact that in June I'm going to be a full-time CTO of the [Obenan.ai](https://obenan.ai) startup! Check the blog [post here](/post/2026-04-18-cto/).

---

# This Week's Highlight

## NIST Limits CVE Enrichment After 263% Surge in Vulnerability Submissions

The National Institute of Standards and Technology (NIST) has announced a major pivot in its CVE enrichment strategy. Instead of trying to enrich every vulnerability that lands in their inbox, they'll now focus only on those in CISA's Known Exploited Vulnerabilities catalog, plus anything affecting critical federal infrastructure software. This is a direct response to the absolute tsunami of CVE submissions - a 263% increase since 2020 that has created a backlog so massive that even NIST's caffeine budget couldn't handle it. The move is sensible but also a bit of an admission that the current vulnerability disclosure ecosystem is fundamentally broken.

https://thehackernews.com/2026/04/nist-limits-cve-enrichment-after-263.html

**Marcin's comment:** Translation: "We can't keep up, so we're going to pretend everything except the truly critical stuff doesn't exist." The real lesson here is that we need fewer noise CVEs and more signal. Maybe that's the real quantum computing problem we should be solving.

---

# AI

## Claude Opus 4.7 is Now Generally Available

Anthropic launched Claude Opus 4.7, positioning it as a major step forward in autonomous software engineering and long-horizon agentic tasks. The new model adds high-resolution image support and introduces a new effort tier that lets you dial up the reasoning horsepower when you need it. This is the kind of release that makes senior engineers simultaneously excited and slightly worried about their job security. Autonomous tasks that previously required hand-holding and explicit step-by-step instructions can now run on their own - which is great for productivity until you realize the LLM is hallucinating your entire deployment pipeline.

https://www.anthropic.com/news/claude-opus-4-7

**Marcin's comment:** "Long-horizon agentic tasks" is fancy for "the AI stays focused on your problem for more than three seconds." Give it a few months and we'll have war stories about production Opus 4.7 instances making decisions we didn't authorize.

## NVIDIA Launches Ising: Open AI Models for Quantum Error Correction

NVIDIA released Ising, a family of open-source AI models designed to act as a control plane for quantum processors. These models dramatically improve quantum error-correction decoding - getting to 2.5x faster and 3x more accurate performance compared to traditional methods. The quantum computing space has been stuck in a performance bottleneck for years, and using AI to improve error correction is exactly the kind of meta-solution that makes sense. NVIDIA is essentially saying: "You want quantum computers to work better? Use our AI models to fix the quantum errors." No pressure on the quantum team.

https://nvidianews.nvidia.com/news/nvidia-launches-ising-the-worlds-first-open-ai-models-to-accelerate-the-path-to-useful-quantum-computers

**Marcin's comment:** Using AI to fix quantum computers feels like using a staircase to fix an elevator. But hey, if it gets us closer to practical quantum computing, I'm here for it.

## Three-Quarters of AI's Economic Gains Are Being Captured by Just 20% of Companies

PwC's latest global study shows that the top 20% of companies are pulling away from the pack by using AI to reinvent their entire business models rather than just automating routine tasks. These winners are nearly three times more likely to automate decision-making while maintaining rigorous AI governance. The takeaway? If you're treating AI as a productivity hack rather than a strategic business lever, you're already losing. The real value isn't in asking Claude to write your unit tests - it's in fundamentally rethinking how your organization makes money.

https://www.pwc.com/gx/en/news-room/press-releases/2026/pwc-2026-ai-performance-study.html

**Marcin's comment:** Yet another study proving that winners act like winners and losers act like losers. Next week's headline: "Companies That Make Good Decisions Outperform Those That Make Bad Ones."

---

# Security

## CISA Adds Known Exploited Vulnerability to Catalog

CISA added CVE-2026-34197, an improper input validation flaw in Apache ActiveMQ, to its Known Exploited Vulnerabilities catalog. Federal agencies must patch this immediately - it's already being exploited in the wild, and leaving it unpatched is basically a self-inflicted security wound. ActiveMQ is everywhere in enterprise Java applications, so this one deserves serious attention. If you're running ActiveMQ and haven't patched yet, you should probably stop reading this newsletter and go fix it.

https://www.cisa.gov/news-events/alerts/2026/04/16/cisa-adds-one-known-exploited-vulnerability-catalog

**Marcin's comment:** "Improper input validation" is the security equivalent of "we forgot to check if the user is trying to burn down our infrastructure." Classic.

## Oracle Critical Patch Update Advisory - April 2026

Oracle shipped its April CPU with dozens of severe vulnerabilities across Java SE, Enterprise Manager, and other product lines. Many are remotely exploitable without authentication, which in Oracle terms means "install this yesterday." Java SE updates are particularly important for anyone shipping containerized JVM applications - these patches often contain critical fixes that could leave you exposed to trivial exploits if ignored. The April updates are standard operating procedure for any responsible Java shop.

https://www.oracle.com/security-alerts/cpuapr2026.html

**Marcin's comment:** Oracle's CPUs are like tax season - predictable, mandatory, and somehow always worse than you expected.

---

# JVM

## JDK 27 Early-Access Release Notes Highlight Post-Quantum Cryptography

The JDK 27 early-access release notes are out, and the star feature is Post-Quantum Hybrid Key Exchange for TLS 1.3. This combines quantum-resistant algorithms with traditional ones by default, protecting you against future quantum computing attacks that don't exist yet but that everyone pretends to worry about. It's a thoughtful defensive measure - preparing for the quantum apocalypse while staying backward-compatible with the present. The Java platform is taking cryptographic future-proofing seriously, which is exactly what you want from your runtime.

https://jdk.java.net/27/release-notes

**Marcin's comment:** Java, preparing for quantum computers while half of enterprise Java still runs on HTTP. At least someone's thinking ahead.

## Java News Roundup: JDK 27, Hibernate, LangChain4j, Keycloak, and Friends

The weekly Java news roundup highlighted the formally proposed JDK 27 release schedule, targeting September 2026 general availability. It also covered point releases across the ecosystem - Hibernate, LangChain4j, Keycloak, and other staples got updates. The Google Agent Development Kit for Java also shipped improvements. If you're keeping dependencies updated, this is your weekly reminder that the ecosystem is alive and moving.

https://www.infoq.com/news/2026/04/java-news-roundup-apr06-2026/

**Marcin's comment:** When the Java ecosystem is moving this fast, you know we're either approaching peak productivity or peak chaos. Probably both.

## IntelliJ IDEA 2026.1: Day-One Java 26 Support and Virtual Thread Debugging

JetBrains released IntelliJ IDEA 2026.1 with day-one support for Java 26 and improved debugging for virtual threads - because debugging 100,000 concurrent threads at once is apparently now a routine task. The IDE also added advanced Spring Data integration and native AI tools to help you ship code faster. Virtual thread debugging is the real story here; it's been a pain point for teams adopting project Loom, and JetBrains delivering tooling support day-one is exactly what the community needs.

https://blog.jetbrains.com/idea/2026/04/java-annotated-monthly-april-2026/

**Marcin's comment:** "Native AI tools" in the IDE. We've finally reached the point where telling your editor what to do is more efficient than telling it what you need. Welcome to the future.

---

# Spring Framework

---

## Spring Framework 6.2.18 and 7.0.7 Available Now

Spring Framework 6.2.18 and 7.0.7, bring dozens of bug fixes and documentation improvements. These updates will ship with Spring Boot 3.5.14 and 4.0.6 respectively, which means if you're on the latest Spring Boot versions, you're getting these framework improvements automatically. Nothing revolutionary here, just solid maintenance work keeping the most popular Java framework running smoothly. If you're not on Spring Boot 4 yet, this is a gentle reminder that you're running on borrowed time.

https://spring.io/blog/2026/04/17/spring-framework-6-2-18-and-7-0-7-available-now

**Marcin's comment:** Spring releasing minor updates is like watching the sun rise - predictable, reliable, and somehow still comforting.

## Spring Data 2026.0.0-RC1: Release Candidate and New Upsert Functionality

Spring Data hit its 2026.0 release candidate phase with feature-complete implementations of new upsert functionality for Spring Data Relational and optimized caching resets in Redis. The RC phase means we're close to final release, which will align with Spring Boot 4.1. Upsert support is a practical win for teams doing bulk data operations - it's a commonly needed operation that was previously awkward to express. Expect this to land as GA within weeks.

https://spring.io/blog/2026/04/17/spring-data-2026-0-0-goes-RC

**Marcin's comment:** "Upsert support" finally. What a time to be alive. Developers in 2016 are weeping with joy from the future.

## Developers Are Shipping 85% Less Code With Spring Boot 4. Here's How

This article breaks down Spring Boot 4's architecture built on Spring Framework 7 and Jakarta EE 11, with focus on how declarative HTTP clients using @HttpExchange eliminate REST boilerplate. The 85% less code claim is marketing math, but there's real substance here - the shift toward declarative patterns is genuinely reducing verbosity. For senior developers, the real win is that you're spending less time writing predictable REST glue code and more time on actual business logic. Spring Boot 4's direction is solidly forward.

https://medium.com/@ujjawalr/developers-are-shipping-85-less-code-with-spring-boot-4-heres-how-7e165b7c81ff

**Marcin's comment:** "85% less code" is probably "85% of your code is now hidden behind a framework annotation." Not complaining though - less boilerplate is always better, even if it means less typing to procrastinate with.

## Spring Team Discusses Spring Framework 7 and Spring Boot 4

Key Spring team members walked through the architecture of Spring Framework 7.0 and Spring Boot 4.0, highlighting first-class REST API versioning and built-in resilience features. They also mentioned Jackson 3 adoption and exploring AI coding tools for the framework itself. This is the kind of insider perspective that matters - seeing where the Spring team is placing their bets for the next era of enterprise Java development. REST versioning is particularly relevant for teams managing multiple API versions in production.

https://www.infoq.com/articles/spring-team-spring-7-boot-4/

**Marcin's comment:** The Spring team using AI coding tools to build Spring. We've achieved peak recursive productivity. Next year they'll announce that Spring is now self-modifying.

---

# Observability

## Grafana v13.0 Launches with Dynamic Dashboards and AI-Powered Health Checks

Grafana 13.0 went GA with Dynamic Dashboards now generally available, featuring a new layout engine and editing experience that actually feels modern. The release also introduces Grafana Advisor - AI-powered health checks for your Grafana instances - and switches Image Renderer authentication to stateless JSON Web Tokens. Dynamic Dashboards are the real win here; they let you build dashboards that adapt to your data rather than forcing your data into rigid static layouts. For teams managing complex observability stacks, this is a meaningful quality-of-life improvement.

https://grafana.com/docs/grafana/latest/whatsnew/whats-new-in-v13-0/

**Marcin's comment:** "AI-powered health checks" for Grafana. We've reached the point where we need AI to tell us if our monitoring tool is working properly. Beautiful.

## OpenTelemetry Declarative Configuration Reaches Stable Status

OpenTelemetry's declarative configuration specification is now stable (hopefully more stable then the usual), meaning you can define all your telemetry settings in a single YAML file across metrics, logs, and traces. This is a vendor-neutral approach that simplifies setup for complex observability pipelines across different programming languages. For Java teams, this is particularly relevant since OpenTelemetry Java instrumentation is production-ready. Single-file configuration beats hand-coding instrumentation across multiple service instances - it's a meaningful quality-of-life win for platform engineers.

https://www.infoq.com/news/2026/04/opentelemetry-declarative-config/

**Marcin's comment:** Finally, observability configuration you can actually version control without needing a dedicated team to maintain it. Revolutionary stuff. Also when I hear "OpenTelemetry" and "stable" in one sentence I think that it must be the perfect anti-thesis.

---

That's all for now.

Thanks again for being here, and see you in the next one.