---
title: "TooMuchCoding Newsletter Issue #30"
date: 2026-07-11
lastmod: 2026-07-11
draft: false
description: "Spring Cloud Contract moves to Stubborn.sh, Apple sues OpenAI, CISA adds critical CVEs, and JDK 27 early-access arrives."
tags: ["newsletter"]
---

Hi!

This week has been absolutely wild - in the best and worst ways possible. Spring Cloud Contract is going through a rebrand that I absolutely need to talk about (the name alone is worth the price of admission), Apple decided to sue OpenAI because apparently trade secrets are a thing, and CISA just added some particularly spicy vulnerabilities to the Known Exploited list. Oh, and JDK 27 showed up early like an overeager houseguest. Buckle up.

Grab a hot beverage.

Let's go. I do hope that you'll enjoy the reading!

## This Week's Highlight

### Spring Cloud Contract Transition to StubbornsH

So this happened. Spring Cloud Contract, the beloved consumer-driven contract testing framework that's been part of the Spring ecosystem for years (and the framework that I founded!), is transitioning to something called [Stubborn.sh](https://stubborn.sh). STUBborn - get it? STUB like stubs in testing! HA HA HA! The good news is that this transition opens up new possibilities for contract testing and stub management. The less good news is that your team now needs to figure out migration paths - but don't worry, I'm here to help.

https://spring.io/blog/2026/07/06/spring-cloud-contract-transition-to-stubbornsh

https://toomuchcoding.com/post/2026-07-06-spring-cloud-contract-becomes-stubborn/

**Marcin's comment:** I guess you could say Spring Cloud Contract decided to stop being so *stubborn* about its own name. Sorry, I'll see myself out.

---
## Shameless self-promotion

I'm doing mentoring and consulting for teams that want to improve software architecture, distributed systems, resilience, observability, developer workflows, and generally survive the AI-generated-code era without setting production on fire.

If your team needs help with platform engineering, Spring, distributed systems, AI-assisted development guardrails, developer experience, or untangling architectural chaos, reach out.

https://toomuchcoding.com/consulting

---
## AI

### Friendly Fire Exploit Brief

The AI Now Institute published an analysis of exploits and vulnerabilities affecting AI systems. This isn't theoretical stuff - this is a deep dive into actual attack vectors and the kinds of problems that crop up when you're building systems at scale with machine learning involved. It's the kind of research that should be on every infra and security team's reading list, especially if you're running any AI workloads in production.

https://ainowinstitute.org/publications/friendly-fire-exploit-brief

**Marcin's comment:** Turns out "friendly fire" is way less friendly when it's an AI system doing the shooting.

### Apple Lawsuit Accuses OpenAI of Stealing Trade Secrets

Apple filed a lawsuit against OpenAI alleging a coordinated pattern of theft by former employees who allegedly stole trade secrets to build new AI hardware. This is the kind of lawsuit that makes legal teams reach for their stress balls and coffee simultaneously. The allegations suggest a systematic effort to grab proprietary information, which - if true - is exactly the kind of thing that security teams and IP lawyers have nightmares about.

https://www.cbc.ca/news/world/apple-lawsuit-open-ai-trade-secrets-9.7266470

**Marcin's comment:** So much for that "open" in OpenAI.

---
## Security

### CISA Adds 4 Actively Exploited Adobe, Joomla, and Langflow Flaws to KEV Catalog

CISA just added four vulnerabilities to its Known Exploited Vulnerabilities catalog, including critical flaws in Adobe ColdFusion and Langflow. These aren't theoretical - they're actively being exploited in the wild right now. If you're running any of these systems, this should trigger an immediate security review and a patch plan. The KEV catalog exists for a reason, and when vulnerabilities land there, it means attackers are already weaponizing them.

https://thehackernews.com/2026/07/cisa-adds-4-actively-exploited-adobe.html

**Marcin's comment:** "Actively exploited" is CISA-speak for "your incident response team is about to have a really bad week."

### CVE-2026-0284 PAN-OS: XML Injection Vulnerability in Large Scale VPN (LSVPN)

Palo Alto Networks issued a security advisory for CVE-2026-0284, an XML injection vulnerability in PAN-OS Large Scale VPN functionality. XML injection attacks can be nasty - they're the kind of vulnerability that lets attackers break out of the intended parsing context and inject malicious data directly into your system. If you're running PAN-OS with LSVPN enabled, this is a priority patch.

https://security.paloaltonetworks.com/CVE-2026-0284

**Marcin's comment:** XML injection - because sometimes your parser has poor boundaries and questionable life choices.

---
## JVM

### Java News Roundup: Strict Field Initialization, GlassFish, GraalVM, JReleaser, RefactorFirst

The latest Java News Roundup highlights JEP 539 (Strict Field Initialization) being elevated to Candidate status, along with updates to GraalVM 25.1 and GlassFish. Strict Field Initialization is a big deal - it forces you to initialize fields in constructors or at declaration time, which kills entire classes of null pointer exceptions before they can happen. GraalVM 25.1 continues to ship improvements to the GraalVM runtime and native image compilation.

https://www.infoq.com/news/2026/07/java-news-roundup-jun29-2026/

**Marcin's comment:** NPE prevention: the gift that keeps on giving.

### JDK 27 Early-Access Release Notes

Draft release notes for JDK 27 early-access builds just landed, featuring the removal of the experimental JVM Compiler Interface (JVMCI) and updates to TLS 1.3 algorithms. JVMCI removal signals that the Java team is consolidating around stable APIs, which is honestly refreshing. The TLS updates mean your production systems will get stronger cryptographic defaults without you having to do much of anything.

https://jdk.java.net/27/release-notes

**Marcin's comment:** JVMCI removal: because sometimes experimental features need to go graduate and get a real job.

### actions/setup-java v5.5.0: signature verification, Kona JDK, and Maven fixes

GitHub released actions/setup-java v5.5.0 with cryptographic signature verification for downloaded JDKs, support for Tencent's Kona JDK distribution, and cleaner Maven build logs. Signature verification is security theater done right - it actually prevents tampering with your build artifacts. Kona JDK support means more teams can standardize on proven JDK distributions without GitHub Actions fighting them on it.

https://github.blog/changelog/2026-07-08-setup-java-v5-5-0-signature-verification-kona-jdk-and-maven-fixes/

**Marcin's comment:** Cryptographic signature verification in your GitHub Actions - because "trust me, this JAR is legit" stopped being acceptable years ago.

---
## Spring

### Spring Cloud Contract Becomes Stubborn

I wrote a piece exploring what's actually going on with Spring Cloud Contract and why the move to [Stubborn.sh](https://stubborn.sh) makes sense if you're willing to dig into it. The behavior patterns reveal a lot about why the Spring team made this call and what the testing landscape looks like now.

https://spring.io/blog/2026/07/06/spring-cloud-contract-transition-to-stubbornsh

https://toomuchcoding.com/post/2026-07-06-spring-cloud-contract-becomes-stubborn/

**Marcin's comment:** Sometimes a framework needs to become stubborn to get the job done right.

### This Week in Spring - July 7th, 2026

The July 7th edition of 'This Week in Spring' covers JobRunr being added to Spring Initializr and the migration of Spring Cloud Contract to StubbornsH. JobRunr is a solid addition for teams that need job scheduling and distributed task execution without the operational overhead of Quartz or similar frameworks. The StubbornsH migration news is the centerpiece, and for good reason.

https://spring.io/blog/2026/07/07/this-week-in-spring-july-07-2026

**Marcin's comment:** Spring Initializr keeps getting better at knowing what your team actually needs.

### A Bootiful Podcast: Spring Boot legend Moritz Halbritter on the latest and greatest in Spring Boot 4 and 4.1

Josh Long interviewed Moritz Halbritter on the latest features in Spring Boot 4 and 4.1, with deep dives into type-safe property paths and other advancements. Moritz doesn't do fluff - if he's talking about it, it's because it matters and it probably solves a problem you're actually facing. Type-safe property paths alone are worth the listen because they kill entire categories of configuration errors.

https://spring.io/blog/2026/07/09/a-bootiful-podcast-moritz-halbritter

**Marcin's comment:** Type-safe property paths: because YAML typos in production are a feature nobody asked for.

---
## Observability

### Top 10 Observability Platforms

A new industry comparison evaluated the top observability platforms for 2026, with OpenObserve highlighted for its unified telemetry approach and cost advantages against Datadog and Dynatrace. The observability space is getting crowded and getting cheaper, which is good news for teams that got priced out of the incumbents. It's worth checking where your current platform ranks and whether the cost-per-signal you're paying actually makes sense anymore.

https://openobserve.ai/blog/top-10-observability-platforms/

**Marcin's comment:** Observability platforms are finally competing on cost instead of just on lock-in. What a time to be alive.

---
## TooMuchCoding Blog

### Łukasz Olejnik Tweet

Caught this tweet from Łukasz on X and thought it deserved amplification. "A massive fraud has been detected in scientific infrastructure. Someone is using AI to mass-create fictional authors, publications, and metadata with real DOIs. Such records can enter publication aggregators and contaminate the academic record. An attack on science?"

https://x.com/lukolejnik/status/2074745527225159840?s=46

**Marcin's comment:** AI used to mass-create authors? Who would have thought?

---

That's all for now.

Thanks again for being here, and see you in the next one.
