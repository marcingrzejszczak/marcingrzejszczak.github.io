---
title: "Issue #25 - When AI Builds Itself"
date: 2026-06-06
lastmod: 2026-06-06
draft: false
description: "Recursive self-improvement, HTTP/2 bombs, JDK 27 rampdown, and why your observability stack needs to get smarter."
tags: ["newsletter"]
---

Hi!

This week the AI gods decided to show off a bit - Anthropic dropped a paper on recursive self-improvement that should honestly make your security teams reach for their stress balls and coffee simultaneously. Meanwhile, the infrastructure world is having its own crisis moment with something delightfully named the "HTTP/2 Bomb," Java's shipping G1 as default, and observability just went full AI with autonomous agents investigating your production incidents. It's the kind of week where you wonder if humans are still in charge or if we just think we are.

Grab a hot beverage.

Let's go. I do hope that you'll enjoy the reading!

## This Week's Highlight

### When AI Builds Itself: Progress Toward Recursive Self-Improvement

Anthropic published a landmark paper detailing how their AI models are increasingly used to autonomously develop and test newer systems, shipping code exponentially faster. The research suggests the industry is accelerating toward "recursive self-improvement," presenting both massive technological opportunities and profound safety challenges regarding human oversight. This isn't science fiction anymore - it's happening in production, and the implications are genuinely wild. We're essentially watching AI systems bootstrap themselves into faster development cycles, which is either the greatest productivity hack ever or the thing that makes security teams question their entire career choices.

https://www.anthropic.com/institute/recursive-self-improvement

**Marcin's comment:** I guess you could say we're witnessing a *compile-time* acceleration toward *runtime* chaos. Sorry, I'll see myself out.

---

## Shameless self-promotion

I'm doing mentoring and consulting for teams that want to improve software architecture, distributed systems, resilience, observability, developer workflows, and generally survive the AI-generated-code era without setting production on fire.

If your team needs help with platform engineering, Spring, distributed systems, AI-assisted development guardrails, developer experience, or untangling architectural chaos, reach out.

https://toomuchcoding.com/consulting

---

## AI

### Canada Launches $2 Billion 'AI for All' National Strategy

Prime Minister Mark Carney officially unveiled Canada's highly anticipated national AI strategy, aiming to boost business adoption to 60 percent and build sovereign supercomputing infrastructure by 2031. The comprehensive plan promises to create 250,000 jobs and includes robust investments in public AI literacy, safety certification programs, and regulatory guardrails. It's Canada's way of saying "we're not going to be left behind," which is refreshing in a world where AI adoption sometimes feels like a race with no finish line.

https://www.cbc.ca/news/politics/carney-ai-strategy-9.7223236

**Marcin's comment:** Great idea - invest in AI safety certification programs right when half the industry is shipping untested AI features on Fridays.

---

## Security

### Critical 'HTTP/2 Bomb' DoS Vulnerability (CVE-2026-49975) Disclosed

Cybersecurity researchers disclosed a highly disruptive remote denial-of-service vulnerability dubbed the "HTTP/2 Bomb," which affects default configurations of major web servers including NGINX, Apache, and Envoy. The exploit combines an HPACK compression bomb with slow-flow control windows to rapidly exhaust server memory, rendering targets inaccessible. It's the kind of vulnerability that makes you appreciate how much infrastructure depends on obscure protocol details that nobody fully understands until they explode.

https://www.imperva.com/blog/imperva-customers-protected-against-cve-2026-49975-http-2-bomb-dos/

**Marcin's comment:** Nothing says "robust protocol design" like a compression algorithm that can be weaponized to kill your infrastructure.

### CISA Adds Exploited Magento RCE Flaw (CVE-2026-45247) to KEV Catalog

CISA officially added CVE-2026-45247 to its Known Exploited Vulnerabilities catalog after observing active, in-the-wild exploitation targeting federal and commercial enterprises. This critical PHP object injection flaw resides in the Mirasvit Full Page Cache Warmer extension for Magento 2, enabling unauthenticated attackers to achieve remote code execution via crafted cookie payloads. The fact that it's being actively exploited means your Magento instances are already scanning in the dark.

https://www.cisa.gov/news-events/alerts/2026/06/03/cisa-adds-one-known-exploited-vulnerability-catalog

**Marcin's comment:** A caching extension with RCE? That's one way to ensure your application is "fast" - by not running at all.

### Protestware and Prompt Injection in Qwik 1.10.0

Analysis of a security incident involving protestware in an open-source project and prompt injection vulnerabilities. This one sits at the intersection of maintainer burnout and the new frontier of supply chain attacks, where code isn't just compromised - it's compromised with a *message*.

https://snyk.io/blog/protestware-open-source-maintainer-qwik-1-10-0-prompt-injection/

**Marcin's comment:** When your open-source contribution becomes a political statement, that's a red flag with a bullhorn.

---

## JVM

### JDK 27 Enters Rampdown Phase One

The Java Development Kit (JDK) 27 officially entered Rampdown Phase One on June 4, 2026, freezing the feature set for its upcoming September general availability. Finalized core enhancements include the integration of post-quantum hybrid key exchange for TLS 1.3 to mitigate future cryptographic attacks, alongside making G1 the default garbage collector in all environments. Java's finally saying "okay, maybe G1 works pretty well," which is only what we've been seeing in production for the last decade.

https://openjdk.org/projects/jdk/27/

**Marcin's comment:** G1 as the default. Mark your calendars - it's not revolutionary, but it's overdue.

### Java News Roundup: Koog 1.0 GA and Endive Wasm Runtime

JetBrains launched the stable 1.0.0 release of Koog, its open-source framework designed to build AI agents utilizing Kotlin and Java. Simultaneously, the Bytecode Alliance introduced Endive, an innovative JVM-native WebAssembly (Wasm) runtime that securely executes Wasm modules without relying on JNI or platform-specific native libraries. So now you can run Wasm on the JVM directly, which means one more reason to argue about sandboxing strategies at your next architecture review.

https://www.infoq.com/news/2026/06/java-news-roundup-may25-2026/

**Marcin's comment:** AI agents in Kotlin - because sometimes you want your self-improving code to have more concise syntax while it replaces you.

---

## Spring

### Spring and Security in the Times of AI

The Spring development team highlighted that the integration of generative AI within security research is rapidly accelerating the discovery and reporting of vulnerabilities. To maintain regulatory compliance and operational security, they advise enterprises to leverage automated dependency upgrade tools to handle the increasing cadence of necessary framework patches. What they're essentially saying is: "buckle up, patches are coming faster now, and you can't keep up manually."

https://spring.io/blog/2026/06/01/spring_and_security_in_the_times_of_ai/

**Marcin's comment:** AI-powered vulnerability discovery means your security team's hair is going to turn gray faster than your Spring patch cadence.

---

## Observability

### OpenTelemetry Launches 'Blueprints' Initiative

The OpenTelemetry project launched its "Blueprints" initiative to provide prescriptive guidance, architectural patterns, and reference implementations for deploying observability systems at enterprise scale. This framework is specifically designed to reduce the cognitive load and operational sprawl that platform teams face when adopting telemetry standards across complex Kubernetes and cloud-native environments. Basically, someone finally realized that "here's a standard" isn't the same as "here's how to actually implement it without losing your mind."

https://www.infoq.com/news/2026/06/opentelemetry-blueprints-launch/

**Marcin's comment:** A blueprint for blueprints - we've gone full meta on observability.

### Dash0 Announces General Availability of Agent0 for the AI Era

Dash0 shifted its platform focus toward "Observability for the AI Era" with the general availability release of Agent0, an autonomous production AI. By natively integrating with OpenTelemetry signals, Agent0 can automatically investigate anomalies, trace root causes to specific code commits, and autonomously draft pull requests, transforming the traditionally reactive monitoring paradigm. Your monitoring system is now smarter than you, and it's drafting fixes while you're still reading the alerts.

https://www.dash0.com/blog/observability-for-the-ai-era-starts-here-agent0-is-ga

**Marcin's comment:** An AI that investigates your production incidents and submits PRs - either this solves everything or your main branch becomes a warzone.

---

That's all for now.

Thanks again for being here, and see you in the next one.