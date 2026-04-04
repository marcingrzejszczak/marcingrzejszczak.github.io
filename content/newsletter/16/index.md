---
title: "Issue #16"
date: 2026-04-04
lastmod: 2026-04-04
draft: false
description: "Claude Code leaked in NPM, Spring Cloud 2025.0.2 released, Database Observability goes GA, and more from the JVM ecosystem."
tags: ["newsletter"]
---

Hi!

Well, well, well. This week we've got Anthropic accidentally playing "show and tell" with Claude Code's entire 500,000-line source repository, Spring Cloud dropping a fresh release that apparently beats performance optimization with a simple upgrade (I guess you could say that's a *compile-time* solution to a runtime problem - sorry, I'll see myself out), and Grafana finally making Database Observability generally available.

Grab a hot beverage and let's go!

I do hope that you'll enjoy the reading!

---

## Shameless self-promotion

Master distributed systems resilience in the age of AI agents. Over 40 students have already joined! Check out my "Generate, Break, Fix: Distributed Systems in the AI Era" workshop on Maven. Use promo code TOOMUCHCODING for $100 off (the $200 is gone 🤷‍♀️): https://maven.com/toomuchcoding/generate-break-fix-distributed-systems-in-the-ai-era

---

# TooMuchCoding

## New newsletter distribution

Setting the newsletter content in MailerLite by drag and drop is a nightmare. I endured 15 weeks but enough is enough. 

I'm a programmer, I can do markdown, I can do asciidoc. It always renders the way I want to! The WYSWIG never does!!!

This is why starting from today I'm going to publish the newsletter on [LinkedIn](https://www.linkedin.com/newsletters/toomuchcoding-newsletter-7433627782582214656) and here. [MailerLite](/subscribe) will have just an introduction and a link here.

## Corporate workshops

I've successfully confirmed a group of around 40 students for a course similar to the one on Maven. If your company would like to run a "Generate, Break, Fix" workshop for your team - I'm open to the conversation. Just reach out via [contact](/contact) or [LinkedIn](https://www.linkedin.com/in/marcin-grzejszczak-15565119/).

## GOTO interview

I had this great privilege of being interviewed by my very good friend Jakub Pilimon about my "carrer". If you want to check it out click [here](https://youtu.be/D-yp3sB8cu0?is=8Ir-BDsf8FsehvZ9) to see the YouTube video.

---

# This Week's Highlight

## Claude Code source code accidentally leaked in NPM package

Anthropic had what you might call a "source map incident." The full 500,000-line source code for Claude Code CLI got exposed through a source map file bundled in an NPM package. The good news: no customer data or underlying model weights were compromised. The bad news: internal architecture and future AI model plans are now public knowledge. It's the kind of mistake that makes security teams reach for their stress balls and coffee simultaneously.

https://www.bleepingcomputer.com/news/artificial-intelligence/claude-code-source-code-accidentally-leaked-in-npm-package/

**Marcin's comment:** This is what happens when you forget that source maps are basically a roadmap to your entire codebase. At least they didn't accidentally push their `.env` file with hardcoded API keys - *that* would have been a real tragedy. The lesson here: check your build artifacts before shipping, folks.

---

# AI

## Claude Code Source Leak Reveals Anthropic's Plans

The leaked code provides an unprecedented look into how Anthropic structures its tooling and where the company is headed with future AI development. This isn't just about what Claude Code does today; it's a window into the company's technical strategy and architectural decisions. Security researchers and competitors have already started analyzing the leak for insights into model capabilities and planned features.

https://arstechnica.com/ai/2026/04/heres-what-that-claude-code-source-leak-reveals-about-anthropics-plans/

**Marcin's comment:** Nothing says "competitive advantage" quite like open-sourcing your internal strategy. Though honestly, for large AI companies, this is more of an embarrassment than a catastrophe. The architecture is probably less interesting than the execution anyway.

## Coding Agents

AI-powered coding agents are becoming increasingly sophisticated, capable of handling complex software development tasks with minimal human intervention. These agents can write, test, and debug code with surprising competence, though they still struggle with the kind of contextual reasoning that experienced developers bring. This technology is fundamentally changing how teams think about code generation and software architecture.

https://www.infoq.com/presentations/coding-agents/

**Marcin's comment:** We're watching the birth of the "code monkey" in digital form. Except these monkeys don't get tired, don't ask for raises, and occasionally hallucinate entire function implementations. What could possibly go wrong?

---

# Security

## Telegram 0-Click Vulnerability Detected

Italy's cybersecurity authority has identified a zero-click vulnerability in Telegram that could allow attackers to execute code without any user interaction. Zero-click vulnerabilities are particularly nasty because they require no social engineering or user involvement - an attacker simply sends a malicious message and boom, you're compromised. This discovery underscores why even the most security-conscious platforms need continuous auditing.

https://www.acn.gov.it/portale/w/telegram-rilevata-vulnerabilita-0-click

**Marcin's comment:** Zero-click vulnerabilities are the "I didn't even have to try" category of exploits. They're what keeps security engineers awake at 3 AM, staring at their ceiling wondering if their messaging app is about to become a botnet node.

## OpenAI Codex Command Injection Vulnerability

A command injection vulnerability was discovered in OpenAI's Codex that could potentially expose GitHub tokens and other sensitive credentials. This is the kind of bug that makes security researchers lose sleep - a code generation tool that can be tricked into running arbitrary commands. The vulnerability highlights the risks of trusting AI-generated code without rigorous sandboxing and validation.

https://www.beyondtrust.com/blog/entry/openai-codex-command-injection-vulnerability-github-token

**Marcin's comment:** Remember when AI-generated code was supposed to make our lives easier? Turns out it can also make attackers' lives easier. The real lesson: don't blindly execute code suggestions from any tool, AI or otherwise.

## ghostsurf: From NTLM Relay to Browser Session Hijacking

Security researchers at SpecterOps detail an attack chain that moves from traditional NTLM relay attacks all the way to full browser session hijacking. This demonstrates how seemingly isolated vulnerabilities can be chained together into a devastating attack path that completely compromises user sessions. The research shows why understanding the full attack surface is critical to proper defense.

https://specterops.io/blog/2026/04/02/ghostsurf-from-ntlm-relay-to-browser-session-hijacking/

**Marcin's comment:** NTLM relay to browser hijacking is like watching a master class in "how to ruin someone's day." It's a beautiful attack chain in the most terrible way possible. The kind of thing that makes you want to re-evaluate every authentication mechanism your org uses.

---

# JVM

## Java News Roundup: GraalVM Build Tools, EclipseLink, Spring Milestones, Open Liberty, Quarkus

The JVM ecosystem continues its relentless march forward with GraalVM Native Build Tools reaching GA at 1.0.0 and JDK 27 entering early-access with build 15. Spring ecosystem projects are rolling out milestone releases, while GlassFish and Open Liberty are getting major maintenance updates. It's the kind of week where you realize the Java platform never actually sleeps - it just keeps getting better in the background.

https://www.infoq.com/news/2026/03/java-news-roundup-mar23-2026/

**Marcin's comment:** GraalVM Native Build Tools hitting 1.0.0 is like watching a side project graduate to production status. The fact that we're getting closer to truly native Java experiences means less memory overhead and faster startup times - which, let's be honest, is what we've all wanted since 1995.

## JDK 27 Early-Access Release Notes

JDK 27 is bringing post-quantum cryptography support with ML-KEM and ML-DSA private key encodings in PKCS #8 format. This forward-thinking approach acknowledges the reality that quantum computers might eventually break current cryptographic standards. While quantum computing is still in its infancy, Java is already preparing the foundation for a post-quantum world.

https://jdk.java.net/27/release-notes

**Marcin's comment:** Post-quantum cryptography support in JDK 27 is basically Java saying "we've got problems from the future, so let's fix them now." It's refreshingly paranoid in the best way.

---

# Spring

## Spring Cloud 2025.0.2 (aka Northfields) Has Been Released

Spring Cloud 2025.0.2 is now GA and built on Spring Boot 3.5.13, bringing significant upgrades to sub-projects including Spring Cloud Netflix and Kubernetes support. A CVE patch for Spring Cloud Config is also included, so if you're still on older versions, now's a good time to think about upgrading. The release represents months of work from the team to keep the Spring Cloud ecosystem modern and secure.

https://spring.io/blog/2026/04/02/spring-cloud-2025-0-2-aka-northfields-has-been-released

**Marcin's comment:** "Northfields" is a fun codename, though I'm pretty sure nobody on the Spring team is thinking about fields right now except for the ones in their data structures.

## We tried every performance trick. A Spring Boot upgrade beat them all.

A team documented their journey through months of performance optimization - caching strategies, database tuning, algorithmic improvements - only to discover that upgrading to a newer Spring Boot version delivered better results than all their manual efforts combined. Lower CPU usage, reduced latency, and improved throughput came from a simple version bump. It's a humbling reminder that sometimes the best optimization is letting experts do what they do.

https://medium.com/javarevisited/we-tried-every-performance-trick-spring-boot-upgrade-beat-them-all-67a9665d5c00

**Marcin's comment:** This is the software engineering equivalent of trying every home remedy for a cold only to have the doctor prescribe antibiotics that actually work. Turns out the Spring team knows something about performance. Who knew?

## Grails isn't done yet, Part 2: EOL Spring Boot and what comes next

Grails developers need to pay attention: the framework's ties to older Spring Boot versions mean legacy applications are sitting on increasingly unsupported dependencies. This article serves as a warning that staying on outdated Grails releases leaves you vulnerable and unsupported. The Grails team is working to move forward, but users need to actively upgrade or face the consequences.

https://foojay.io/today/grails-isnt-done-yet-part-2-eol-spring-boot-and-what-comes-next/

**Marcin's comment:** If you're still running Grails on EOL Spring Boot versions, you're not living on the edge - you're just behind it. Time to upgrade before support disappears entirely.

---

# Observability

## Database Observability now Generally Available

Grafana Labs has released Database Observability for MySQL and PostgreSQL as a generally available feature. Teams can now identify slow queries and analyze database performance directly within Grafana Cloud using Grafana Alloy. This integration removes friction from the database monitoring workflow and provides visibility into one of the most critical infrastructure components.

https://grafana.com/whats-new/2026-04-02-database-observability-now-generally-available/

**Marcin's comment:** Finally, a tool that recognizes that databases are part of your observability problem, not just the thing sitting behind your application hoping nobody notices when it gets slow. This is the kind of feature that makes ops teams actually smile.

## Sustaining OpenTelemetry: moving from dependency management to stewardship

Bloomberg and the CNCF are launching a mentorship cohort focused on OpenTelemetry sustainability. The program trains new contributors and reduces the operational burden on maintainers of a project that's become critical infrastructure for observability. This is important work because OpenTelemetry doesn't maintain itself - it needs active stewardship.

https://www.cncf.io/blog/2026/03/31/sustaining-opentelemetry-moving-from-dependency-management-to-stewardship/

**Marcin's comment:** OpenTelemetry has become so important that the entire industry is now realizing it needs proper funding and stewardship. Translation: we all benefited from open source for years and now we're finally acknowledging the maintainers deserve support.

## Erste Digital transforms observability with OpenTelemetry and a unified platform

Erste Digital documented their migration from multiple observability tools to Grafana Cloud and OpenTelemetry, reducing tool sprawl and improving incident response capabilities. In a highly regulated environment like banking, consolidation and consistency are not just nice-to-haves - they're requirements. This case study shows how modern observability platforms can simplify compliance and reduce operational overhead.

https://www.youtube.com/watch?v=8gIzJbUE5no

**Marcin's comment:** When a bank switches to a unified observability platform and incident response improves, you know you're onto something. Turns out having one source of truth beats having seventeen dashboards in seventeen different systems.

---

That's all for now.

Thanks again for being here, and see you in the next one.