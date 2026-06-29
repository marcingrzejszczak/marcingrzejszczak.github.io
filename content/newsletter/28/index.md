---
title: "TooMuchCoding Newsletter #28"
date: 2026-06-27T09:00:00Z
lastmod: 2026-06-27T09:00:00Z
draft: false
description: "JDK 27 defaults to G1GC, Spring 3.5.x hits EOL, Google loses more AI talent to Anthropic, and Cisco zero-days are in the wild. Plus the usual security chaos."
tags: ["newsletter"]
---

Hi!

This week we've got some solid Java news with JDK 27 finally making G1GC the default (only took a decade of "we'll get to it"), Spring 3.5.x waving goodbye, Google hemorrhaging AI talent to Anthropic like a sieve, and Cisco discovering that zero-days are a lot more fun when you're not the one hunting them. Oh, and CISA's basically asking federal agencies to run faster because there's nothing like external pressure to motivate a good patch Tuesday. I guess you could say the security landscape is really "catching" people's attention - sorry, I'll see myself out.

Grab a hot beverage.

Let's go. I do hope that you'll enjoy the reading!

## This Week's Highlight

### Łukasz Olejnik Tweet

Łukasz Olejnik shared some sharp thoughts on X this week. His perspective on the current state of tech and AI developments is worth your attention if you're thinking critically about where this industry is heading.

https://x.com/lukolejnik/status/2069166739934367890?s=46

**Marcin's comment:** Always good to see folks who aren't afraid to call out the hype cycle.

---

## Shameless self-promotion

I'm doing mentoring and consulting for teams that want to improve software architecture, distributed systems, resilience, observability, developer workflows, and generally survive the AI-generated-code era without setting production on fire.

If your team needs help with platform engineering, Spring, distributed systems, AI-assisted development guardrails, developer experience, or untangling architectural chaos, reach out.

https://toomuchcoding.com/consulting

---

## AI

### Google poised to lose two more high-profile AI staffers to Anthropic

Jonas Adler and Alexander Pritzel, both key contributors to Google's Gemini AI model, are reportedly heading for the exits and landing at Anthropic. This isn't just a couple of engineers jumping ship - these are the folks who know exactly how Google's training pipelines work, how they optimize models at scale, and where the secret sauce lives. The talent war in AI is getting genuinely vicious, and pre-IPO startups are dangling fat equity grants that make Google's compensation packages look quaint.

https://www.latimes.com/business/story/2026-06-24/google-poised-to-lose-two-more-high-profile-ai-staffers-to-anthropic

**Marcin's comment:** Google's like that friend who throws great parties but everyone's leaving early because there's a cooler party next door with better drinks.

---

## Security

### Security Discussion on X

Łukasz Olejnik has been posting some sharp takes on the current security landscape. It's the kind of thoughtful analysis you should be reading if you're responsible for security decisions anywhere in your org.

https://x.com/lukolejnik/status/2068336485091184976?s=46

**Marcin's comment:** Security discourse on X: where overconfidence meets real vulnerability data.

### Microsoft Security Intelligence

Microsoft's security intelligence team flagged some important threats this week. If you're running Microsoft infrastructure, this is one of those "you should probably read this" posts rather than skipping it.

https://x.com/msftsecintel/status/2070225815803949436?s=46

**Marcin's comment:** Windows defender detected: newsletter subscriber reading his email.

### CISA Adds Two Known Exploited Vulnerabilities to Catalog

CISA just added two vulnerabilities to the Known Exploited Vulnerabilities list - PTC Windchill and Cisco Unified Communications Manager. That means federal agencies are now required to patch these, yesterday if possible. Both of these are the kind of flaws that attackers are actively using in the wild, so if you're running either of these systems anywhere remotely important, your remediation window just became "now". The post-exploitation control implications here are pretty grim - once someone's in, they can own the whole box.

https://www.cisa.gov/news-events/alerts/2026/06/25/cisa-adds-two-known-exploited-vulnerabilities-catalog

**Marcin's comment:** CISA to your team: "Remember when we said patch Tuesday? We meant right now, no really, right now."

### Zero-day exploitation in Cisco Catalyst SD-WAN Manager

Mandiant researchers caught a threat actor actively exploiting CVE-2026-20245, a privilege escalation zero-day in Cisco's SD-WAN infrastructure. The attacker leveraged admin credentials to upload a malicious payload and escalated all the way to root. This is the kind of attack that makes security teams reach for their stress balls and coffee simultaneously - you've got administrative credentials compromised, and suddenly the attacker owns your entire infrastructure layer. It's the privilege escalation nightmare scenario playing out in real time.

https://cloud.google.com/blog/topics/threat-intelligence/zero-day-exploitation-cisco-catalyst-sd-wan-manager

**Marcin's comment:** Admin accounts: because sometimes you want to give attackers a head start before they even break in.

---

## JVM

### JDK 27 Early-Access Release Notes

JDK 27 is officially making G1GC the default collector across the board. This is the move that should have happened years ago - G1's been battle-tested in production for over a decade, and having it as the default saves everyone from the "which GC should I actually use" conversation. They're also finally yanking JVMCI out as experimental, so if you've been relying on that interface for JIT compilation customization, now's the time to migrate away or take on the maintenance burden yourself.

https://jdk.java.net/27/release-notes

**Marcin's comment:** G1GC as default: finally giving JVM developers one less decision to make at 3am when something melts down.

---

## Spring

### Spring Boot 3.5.16 available now

Spring Boot 3.5.16 just dropped, and here's the kicker - this is the final Open Source Software release for the 3.5.x line. After this, you're either moving to 4.0.x or 4.1.x for continued community support, or you're stuck on an unsupported version. This release brings three dependency upgrades, but the real story is the deprecation clock ticking down. If your team's got anything on 3.5.x, start planning your migration now because support expiration doesn't care about your release schedule.

https://spring.io/blog/2026/06/25/spring-boot-3-5-16-available-now

**Marcin's comment:** Spring Boot 3.5.x EOL: the universe's way of forcing you to do that upgrade you've been procrastinating on.

### What You Need To Know: Spring Framework's End-Of-Life Dates

Spring Framework 6.2.x is hitting end-of-life on June 30, 2026 - that's basically right now. Only 7.0.x stays under active community support, and here's where it gets gnarly: a lot of teams have versions pinned transitively through Spring Boot dependencies, meaning you could be running an unsupported version without even realizing it. You need to audit your dependency tree, figure out what you're actually running, and if you're on 6.2.x anywhere, start planning your move to 7.0.x.

https://www.herodevs.com/blog-posts/what-you-need-to-know-spring-frameworks-end-of-life-dates

**Marcin's comment:** Transitive dependencies: the gift that keeps on giving, especially when it's giving you unsupported versions.

---

## Observability

### Stop Training Blind: Scaling AI with the New OpenTelemetry-Based TPU AI Telemetry Collector Agent

Google Cloud just released an OpenTelemetry-based telemetry collector agent for monitoring TPU training at scale. The beautiful part here is that it standardizes metrics collection for AI workloads and routes high-fidelity hardware data to either Google Cloud Monitoring or your own self-hosted Prometheus setup with basically zero performance overhead. If you're training large models on TPUs and currently flying blind, this is the infrastructure layer that finally lets you see what's actually happening.

https://discuss.google.dev/t/stop-training-blind-scaling-ai-with-the-new-opentelemetry-based-tpu-ai-telemetry-collector-agent/375210

**Marcin's comment:** OpenTelemetry for TPU training: because "I don't know why my model training is slow" shouldn't be an acceptable answer anymore.

---

That's all for now.

Thanks again for being here, and see you in the next one.
