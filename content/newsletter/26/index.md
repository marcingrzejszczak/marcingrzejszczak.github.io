---
title: "TooMuchCoding Newsletter #26"
date: 2026-06-13
lastmod: 2026-06-15
draft: false
description: "Issue #26 of TooMuchCoding Newsletter for senior JVM/Java developers. Anthropic cancels Fable 5 and Mythos 5 for everyone, CISA goes full panic mode with 3-day patch deadlines, and Spring Boot 4.1.0 ships with native gRPC support."
tags: ["newsletter"]
---

Hi!

This week started with Anthropic dropping two new models with great fanfare, then ended with a plot twist: both Claude Fable 5 and Mythos 5 got cancelled for everyone. Meanwhile, CISA decided that three days is enough time for federal agencies to patch critical vulnerabilities (spoiler: it isn't), Spring Boot 4.1.0 finally shipped native gRPC support, and Datadog admitted that SaaS-only isn't always the answer. Oh, and there are worms targeting bioinformatics and MCP developers - because apparently no niche is too small for a supply chain attack.

Grab a hot beverage.

Let's go. I do hope that you'll enjoy the reading!

## This Week's Highlight

### Claude Fable 5 & Mythos 5: Launched and Cancelled Before Most of Us Could Try Them

Anthropic dropped two new models this week with much fanfare - Claude Fable 5 as the public-facing flagship with impressive spatial reasoning and coding capabilities, and Mythos 5 as the model with "extreme cybersecurity capabilities" staying locked behind strict partnership agreements. Then came the sequel nobody expected: both models have been cancelled for everyone. Fable 5, which briefly appeared as the next public frontier, is gone. Mythos 5, which never made it out of the vault even for partners, also gone. This is quite possibly the most dramatic single-week arc in recent AI release history - a full launch announcement followed by a full rollback.

https://www.anthropic.com/news/claude-fable-5-mythos-5

https://x.com/lukolejnik/status/2066410135929471305?s=46

**Marcin's comment:** Ship it, cancel it, gone before most people noticed. The AI release cycle is starting to look like my deployment pipeline on a Friday afternoon.

---

## Shameless self-promotion

I'm doing mentoring and consulting for teams that want to improve software architecture, distributed systems, resilience, observability, developer workflows, and generally survive the AI-generated-code era without setting production on fire.

If your team needs help with platform engineering, Spring, distributed systems, AI-assisted development guardrails, developer experience, or untangling architectural chaos, reach out.

https://toomuchcoding.com/consulting

---

## AI

### Accenture and Carnegie Mellon University Launch AI Adoption Maturity Model

Accenture and CMU's Software Engineering Institute released a new framework to help organizations stop fumbling through AI adoption like everyone's still in the "throw AI at everything" phase. The model takes you from basic experimentation to actually scaling AI with predictable outcomes - baseline assessment tools, roadmaps for responsible implementation, and hopefully fewer incidents where someone's prompt-injected their way into production chaos.

https://newsroom.accenture.com/news/2026/accenture-and-the-carnegie-mellon-university-software-engineering-institute-launch-ai-adoption-maturity-model-to-help-organizations-scale-ai-with-predictable-outcomes

**Marcin's comment:** Maturity models: the enterprise equivalent of admitting you've been winging it.

---

## Security

### Twitter Security Thread by mrgretzky

A thread worth your time - mrgretzky digs into current security topics and vulnerabilities. Short and to the point, which is more than can be said for most security advisories.

https://x.com/mrgretzky/status/2064803596147765656?s=46

**Marcin's comment:** Another day, another reason to check your security logs before bed.

### Miasma Reaches Azure

An analysis of the Miasma malware now targeting Azure cloud infrastructure. Malware chasing cloud platforms isn't exactly a surprise at this point, but knowing what you're up against beats discovering it mid-incident at 3 AM.

https://opensourcemalware.com/blog/miasma-reaches-azure

**Marcin's comment:** Because nothing says 'cloud security' like malware with a Greek mythology name lurking in your Azure environment.

### Malicious Worms Target Bioinformatics and MCP Developers

Socket.dev spotted three new malicious npm packages - mini-shai-hulud, miasma, and hades - specifically hunting bioinformatics and MCP developers. This is the supply chain attack playbook: find a niche community, weaponize some dependencies, wait for the footgun.

https://socket.dev/blog/mini-shai-hulud-miasma-and-hades-worms-target-bioinformatics-and-mcp-developers-via-malicious

**Marcin's comment:** Nothing says 'productive Tuesday' like discovering worms named after sci-fi villains in your dependencies.

### CISA Adds Cisco, Chrome, and Arista Flaws to KEV Catalog Amid Active Exploitation

CISA added critical vulnerabilities affecting Cisco Catalyst SD-WAN, Google Chrome V8, and Arista EOS to the Known Exploited Vulnerabilities catalog. Federal agencies have to patch or strictly mitigate these by June 23, 2026. The flaws are actively being exploited - this isn't theoretical, this is "your adversary is actively weaponizing this right now" territory.

https://thehackernews.com/2026/06/cisa-adds-cisco-chrome-and-arista-flaws.html

**Marcin's comment:** Active exploitation + federal mandate + 10-day deadline. That's the security equivalent of an all-hands emergency meeting.

### CISA Tells Govt Agencies to Patch Critical Exploited Flaws in 3 Days

CISA issued Binding Operational Directive 26-04: patch the most critical, publicly exposed, and automatable vulnerabilities in three days or face consequences. This is a risk-based framework - the worst flaws get the shortest timelines. Three days. For a critical vulnerability. On federal systems. Welcome to the new normal.

https://www.bleepingcomputer.com/news/security/cisa-tells-govt-agencies-to-patch-critical-exploited-flaws-in-3-days/

**Marcin's comment:** Three-day SLAs. One word: yikes.

---

## JVM

### Java News Roundup: JDK 27 in Rampdown, JDK 28 Expert Group, GlassFish, Infinispan, Kotlin

JDK 27 officially hit Rampdown Phase One - feature set frozen. Nine targeted JEPs made the cut, including G1 becoming the default garbage collector and post-quantum hybrid key exchange support. Meanwhile, the JDK 28 Expert Group is already spinning up for the March 2027 release. The pipeline is at full velocity: 27 is locked down, 28 is gearing up.

https://www.infoq.com/news/2026/06/java-news-roundup-jun01-2026/

**Marcin's comment:** G1 as default: the GC wars finally have a winner.

### Performance Improvements in JDK 26

JDK 26 shipped with serious performance enhancements - Lazy Constants API preview, C2 compiler vectorization optimizations, faster garbage collection algorithms, and improved low-level cryptographic arithmetic processing. These aren't incremental gains; this is measurable performance improvement per the release notes.

https://inside.java/2026/06/09/jdk-26-performance-improvements/

**Marcin's comment:** Performance improvements that don't require you to sacrifice your JVM flags sanity. Finally.

---

## Spring

### Spring Boot 4.1.0 Available Now

Spring Boot 4.1.0 is out with native Spring gRPC support - no external libraries, no hacky workarounds. OpenTelemetry capabilities updated, HTTP Client SSRF mitigations landed via InetAddressFilter, and Log4j file rotation support is now built in. This is a feature release that actually has features.

https://spring.io/blog/2026/06/10/spring-boot-4/

**Marcin's comment:** Native gRPC support in Spring Boot. Only took forever, but the wait was worth it.

### Spring Cloud 2025.1.2 (aka Oakwood) Has Been Released

Spring Cloud 2025.1.2 is generally available and plays nice with Spring Boot 4.1.0. Patches CVE-2026-47825 in Spring Cloud Gateway and introduces a new StripContextPath filter. If you're running Spring Boot 4.1.0, this is your matching release.

https://spring.io/blog/2026/06/11/spring-cloud-2025-1-2-aka-oakwood-has-been-released

**Marcin's comment:** Oakwood release. At least it's not named after a construction material that collapses under pressure.

### Spring Cloud 2025.0.3 (aka Northfields) Has Been Released

The final open-source release for the 2025.0.x branch before support ends June 30, 2026. Backports vital security fixes and improves MVC ProxyExchange observability for URI templates. If you're still on 2025.0.x, this is your last stop before you need to upgrade.

https://spring.io/blog/2026/06/11/spring-cloud-2025-0-3-aka-northfields-has-been-released

**Marcin's comment:** Final release before end of support. Get your upgrade plans ready.

---

## Observability

### Datadog Observability Breaks Away from SaaS with BYOC

Datadog announced bring-your-own-cloud (BYOC) and federated log capabilities at DASH 2026 - enterprises can now analyze agentic AI telemetry datasets stored in their own cloud without routing everything through Datadog's infrastructure. Addresses cost reduction and digital sovereignty concerns in one move.

https://www.techtarget.com/searchitoperations/news/366644272/Datadog-observability-breaks-away-from-SaaS-with-BYOC

**Marcin's comment:** SaaS company finally admits that SaaS-only isn't always the answer. Never thought I'd see it.

---

That's all for now.

Thanks again for being here, and see you in the next one.
