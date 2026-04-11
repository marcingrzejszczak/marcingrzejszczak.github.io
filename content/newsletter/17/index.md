---
title: "TooMuchCoding Newsletter #17"
date: 2026-04-11
lastmod: 2026-04-11
draft: false
description: "Issue #17: Project Glasswing, Marimo's RCE, and the productivity paradox of AI-assisted development."
tags: ["newsletter"]
---

Hi!

This week, Anthropic decided that the best way to secure software is to give their unreleased Claude Mythos Preview model the ability to find and exploit vulnerabilities before the bad guys do - which is either brilliant or terrifying depending on whether you trust their red-teaming. Meanwhile, a Python notebook framework called Marimo got absolutely decimated by a pre-auth RCE that was exploited in the wild within 10 hours of disclosure, proving once again that "we'll patch it later" is a business strategy, not a security strategy. The observability ecosystem continues its love affair with OpenTelemetry. Oh, and get this - developers using AI tools saw an 18% productivity boost, which means we're not replacing you yet, though I'm sure the algorithms are working on it. (Sorry, I couldn't resist - that joke was absolutely core to my argument, though it definitely needed some GC cycles to be funny.)

Grab a hot beverage.

Let's go. I do hope that you'll enjoy the reading!

---
# *Shameless self-promotion:* 

Master the chaos of distributed systems in an AI-driven world. Check out my "Generate, Break, Fix: Distributed Systems in the AI Era" workshop on Maven. 

Use promo code **TOOMUCHCODING** for **$100** off: 

https://maven.com/toomuchcoding/generate-break-fix-distributed-systems-in-the-ai-era

---

# This Week's Highlight

---

## Anthropic Announces Project Glasswing to Secure Critical Software

Anthropic has partnered with AWS, Google, and Microsoft to launch Project Glasswing, an initiative that grants access to Claude Mythos Preview - an unreleased frontier AI model specifically capable of finding and exploiting software vulnerabilities. The goal is preemptive security: identify and patch critical infrastructure vulnerabilities before attackers weaponize them. This is essentially giving white-hat hackers an AI that's terrifyingly good at breaking things, but with the understanding that they'll tell you where the cracks are first.

https://www.anthropic.com/glasswing

**Marcin's comment:** The irony is delicious. We've been worried about AI being weaponized for years, and now we're weaponizing it defensively. Let's just hope none of these partners accidentally leave the Claude Mythos Preview's API keys in a GitHub commit.

---

# AI

## Om Patel's Caveman Claude Prompt Hack

Om Patel shared a prompt engineering trick that forces Claude to communicate with minimal tokens during agentic web searches, saving up to 75% on API costs. By constraining the model's output verbosity, you get the same functionality with a fraction of the processing overhead - essentially teaching Claude to grunt like a caveman instead of waxing poetic about your requests.

https://x.com/om_patel5/status/2040279104885314001?s=46

**Marcin's comment:** So we're paying less for the same intelligence but with worse grammar. That's either the future of AI optimization or a warning sign that we're all speaking like cavemen soon.

## Bluesky Post by carnage4life: AI Productivity Paradox

Dare Obasanjo posted about a recent study showing an 18% productivity boost for developers using AI tools - a reversal of earlier findings suggesting AI might decrease productivity. The catch? The true boost may be undercounted because experienced developers refuse to participate in studies without their AI assistants, creating a selection bias that skews the results even more in AI's favor.

https://bsky.app/profile/carnage4life.bsky.social/post/3min777vjd22i

**Marcin's comment:** Translation: developers have become so dependent on AI that they can't function in the control group anymore. We're not replacing developers yet, but we're definitely making vanilla development feel like coding with your hands tied behind your back.

## Memory Palace: Open-Source AI Memory System

Milla Jovovich and developer Ben Sigman released MemPalace, an open-source AI memory system that achieves record-breaking scores on the LongMemEval benchmark. The architecture uses a "method of loci" approach to retain perfect conversational memory without relying on an LLM to summarize, meaning you get long-term context without the memory loss that plagues typical retrieval systems.

https://github.com/milla-jovovich/mempalace

**Marcin's comment:** An actress and a developer walk into an AI memory project... and somehow it outperforms the models trained on billions of tokens. The ancient Greeks were onto something with their memory palaces, apparently.

## OpenAI Launches GPT-5.4 with 1M Context Window

OpenAI unveiled GPT-5.4, featuring a staggering 1-million-token context window and the ability to autonomously execute multi-step workflows. The model scored 75% on the OSWorld-V desktop task benchmark, surpassing the human baseline and signaling the shift toward agentic digital coworkers that can handle complex, sequential tasks without human intervention.

https://www.crescendo.ai/news/latest-ai-news-and-updates

**Marcin's comment:** 1 million tokens means it can finally read your entire Slack history without forgetting what you said three messages ago. We're moving from AI assistants to AI coworkers, which is either exciting or time to update your LinkedIn profile.

---

# Security

## Russian GRU Hackers Hijacking TP-Link Routers for DNS Spoofing

Security researcher Lukasz Olejnik detailed a campaign by Russian GRU hackers compromising TP-Link routers to perform DNS spoofing attacks. The attackers modify DNS settings to serve fake login pages for services like Outlook, harvesting credentials with alarming precision. This is a reminder that your router is often the weakest link in your home network security posture.

https://x.com/lukolejnik/status/2042587032128487935?s=46

**Marcin's comment:** Remember when we thought the cloud would fix everything? Turns out the foundation of your network is still a $50 device that you bought five years ago and never patched.

## Root in One Request: Marimo's Critical Pre-Auth RCE (CVE-2026-39987)

A severe pre-authenticated remote code execution vulnerability (CVE-2026-39987) was discovered in the Marimo Python notebook framework and exploited in the wild within 10 hours of disclosure. Attackers can obtain a full interactive shell via a single unauthenticated WebSocket connection, making this a nightmare for anyone running Marimo notebooks in production or sharing them with untrusted users.

https://www.endorlabs.com/learn/root-in-one-request-marimos-critical-pre-auth-rce-cve-2026-39987

**Marcin's comment:** "10 hours" is how long it takes from disclosure to weaponization these days. If you're running Marimo, patch now and ask questions 

---

# JVM

## Java 26: AOT Object Caching with Any GC

Java 26 brings JEP 516, enabling Ahead-of-Time (AOT) object caching with any Garbage Collector, including ZGC. This enhancement significantly boosts application startup times by pre-caching object allocations, reducing the cold-start penalty that has plagued JVM adoption in serverless and containerized environments. It's a solid incremental improvement for the ecosystem's ongoing performance obsession.

https://medium.com/@achrafhasbi/java-26-updates-you-must-know-7dbbdd489a28

**Marcin's comment:** Finally, the JVM is getting serious about startup time. We've only been waiting since 2015 for this to matter for serverless adoption.

---

## JDK 27 Early-Access Release Notes

OpenJDK published draft release notes for JDK 27, highlighting Post-Quantum Hybrid Key Exchange for TLS 1.3 and new security properties to control standard input password reading. The quantum computing threat is finally getting mainstream attention in the Java ecosystem, and the platform is moving to protect itself before quantum breaks everything.

https://jdk.java.net/27/release-notes

**Marcin's comment:** Post-quantum cryptography is now a "nice to have" instead of "paranoid over-engineering." We're living in interesting times.

---

## Java 25 LTS: The Practical Upgrade Point

Java 25 LTS emphasizes steady evolution with improved concurrency defaults and reduced memory footprint. The analysis suggests Java 25 is becoming the practical upgrade point for most enterprise teams, offering stability without the experimental features of earlier releases. For teams still on Java 11 or 17, this is the signal to start planning migrations.

https://www.mimacom.com/blog/learn-whats-new-in-java-25-what-changed-and-why-it-matters?hs_amp=true

**Marcin's comment:** "Practical" is code for "we finally fixed enough bugs that it won't break your production system." Conservative upgrade recommendation from the Java ecosystem is always a good sign.

---

# Spring

## Spring AI 2.0 is Coming May 28

Spring AI 2.0 reaches General Availability on May 28, 2026, requiring Spring Boot 4.0 as a prerequisite. This creates urgency for teams still on Spring Boot 3.5 -- the clock is ticking on EOL, and you'll need the migration done before May 28 to avoid a cliff. Plan now, execute after your coffee break.

https://www.herodevs.com/blog-posts/spring-ai-2-0-is-coming-may-28-here-is-why-that-makes-the-june-30-deadline-more-urgent-not-less

**Marcin's comment:** May 28 is six weeks away. If you're still on Spring Boot 3.5, your upgrade war room is probably already booked.

---

# Observability

## OpenTelemetry eBPF Instrumentation (OBI)

OBI uses eBPF to capture traces and metrics directly from the Linux kernel, providing zero-code observability across Kubernetes environments with minimal overhead. This eliminates the need for code instrumentation libraries, making observability a infrastructure concern rather than an application concern. It's observability without the boilerplate.

https://opentelemetry.io/docs/zero-code/obi/

**Marcin's comment:** eBPF is the gift that keeps giving. First it debugged everything, now it's observing everything. Your kernel is basically a spy at this point.


## OpenTelemetry with Grafana Cloud

Grafana Labs expanded support for OpenTelemetry, offering a unified platform for metrics, logs, traces, and profiles with no vendor lock-in. The cloud platform emphasizes AI-powered full-stack observability, integrating multiple signal types into a single coherent view of your system. It's what everyone's been asking for - one dashboard to rule them all.

https://grafana.com/solutions/opentelemetry/

**Marcin's comment:** "No vendor lock-in" from a vendor is usually corporate speak for "we're confident enough in our product that we don't need to trap you."

---

That's all for now.

Thanks again for being here, and see you in the next one.
