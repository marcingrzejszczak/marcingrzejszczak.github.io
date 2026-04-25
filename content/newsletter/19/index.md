---
title: "TooMuchCoding Newsletter - Issue #19"
date: 2026-04-25
lastmod: 2026-04-25
draft: false
description: "Weekly newsletter for senior JVM/Java developers covering AI security breaches, zero-day vulnerabilities, JVM updates, Spring releases, and observability innovations."
tags: ["newsletter"]
---

Hi!

This week, we've got more cybersecurity chaos than a ransomware convention. Anthropic's unreleased "Mythos" AI model got breached - because nothing says "frontier AI safety" like unauthorized access to your vulnerability-detection superweapon. Meanwhile, the US government is playing whack-a-mole trying to stop AI model distillation, CISA is handing out zero-day patches like candy, and apparently someone actually thought it was a good idea to leave a weather sensor unguarded at an airport (spoiler: a prediction market trader had thoughts about that). Oh, and the JVM ecosystem is getting post-quantum cryptography, Spring Security is patching vulnerabilities faster than you can say "CVE," and Grafana just launched version 13 with enough observability goodness to make your dashboards weep with joy. I promise this newsletter is more enlightening than it is *alarming* - though I suppose you could say the security section really *breaches* expectations. (I apologize for that pun. I'm aware it's bad. Moving on.)

Grab a hot beverage.

Let's go. I do hope that you'll enjoy the reading!

---

# Shameless self-promotion:

Master the intersection of AI, distributed systems, and chaos engineering. Check out my "Generate, Break, Fix: Distributed Systems in the AI Era" workshop on Maven. 

Use promo code TOOMUCHCODING for $100 off: https://maven.com/toomuchcoding/generate-break-fix-distributed-systems-in-the-ai-era

---

# This Week's Highlight

## Anthropic Investigates Breach of Unreleased 'Mythos' AI Cybersecurity Model

So, Anthropic's crown jewel - a cutting-edge AI model designed to autonomously detect and exploit software vulnerabilities - got breached. The irony is so thick you could spread it on toast. The unauthorized access happened via a third-party vendor environment, because of course it did. This isn't just "oops, someone left the keys in the ignition" territory; this is frontier AI security theater at its finest. When the system designed to find exploits gets itself exploited, you have to wonder who's auditing the auditors. Global security experts are rightfully concerned about what happens when advanced autonomous exploitation capabilities end up in hostile hands.

https://www.cbsnews.com/news/anthropic-investigates-mythos-ai-breach/

**Marcin's comment:** This is what happens when you build a system that can autonomously discover zero-days and then store it in an environment secured by... surprise... a third-party vendor. It's like building a bank vault and leaving the combination on a sticky note taped to the cleaning supplies closet. The real question isn't whether it got breached - it's how long until someone weaponizes it.

---

# AI

## US seeking to halt AI model 'exploitation'

The White House has announced new measures to prevent Chinese AI developers from using leading American models as training data for their own systems - a practice cheerfully called "distillation." They're improving information sharing and detection mechanisms to catch unauthorized model extraction. It's essentially trying to lock down intellectual property in an era where the border between "learning from" and "stealing" gets blurrier every quarter. The government clearly recognizes that letting frontier AI models leak to competitors is roughly equivalent to handing over the nuclear codes, just with more tensor operations.

https://www.taipeitimes.com/News/biz/archives/2026/04/25/2003856190

**Marcin's comment:** Distillation is just "we're training our model on your model's outputs" with extra steps. Good luck policing that when the outputs are literally text or API responses. It's like trying to prevent knowledge transfer by watching who reads the textbooks.

## Rauchg's AI Post

Guillermo Rauch documented a sophisticated security incident where a Vercel employee got compromised through a breach of the Context.ai platform. The attacker group displayed striking operational tempo and deep technical understanding - so much so that Rauch suspects they were significantly accelerated by AI tools. This isn't just about one compromised employee; it's evidence that adversaries are now weaponizing the same AI capabilities that defenders are trying to deploy. When your security incidents are being orchestrated by AI-enhanced threat actors, you've entered a new era of cat-and-mouse dynamics.

https://x.com/rauchg/status/2045995362499076169?s=46

**Marcin's comment:** The delicious irony: we're building AI to defend against attackers, who are now using AI to attack us. It's like arming both sides of a conflict and hoping your side gets smarter faster. Spoiler alert: that's not how arms races work.

## AI Writing: It's Not Just This, It's That

TechCrunch explored the nuances of AI-generated writing and its impact on content creation and editorial workflows. The piece digs into how generative text models are reshaping how we think about writing itself - not just automating it, but fundamentally changing what "writing" means. There's complexity here beyond the binary "AI is good" or "AI is bad" takes that dominate discourse. Understanding these subtleties matters if you're building systems that consume or produce written content.

https://techcrunch.com/2026/04/20/ai-writing-its-not-just-this-its-that-barrons/

**Marcin's comment:** Nuance in AI discourse? In 2026? How refreshingly quaint. But seriously, the distinction between "AI as writing tool" and "AI as writing replacement" is worth understanding, especially when you're deploying these systems in production.

---

# Security

## CISA Puts US Government Agencies on Two-Week Deadline to Patch Microsoft Defender 'BlueHammer' Zero-Day

CISA added CVE-2026-33825 (BlueHammer) to its Known Exploited Vulnerabilities catalog, and federal agencies now have exactly 14 days to patch a severe privilege escalation flaw in Microsoft Defender. Active exploitation in the wild means this isn't theoretical - actual bad actors are weaponizing this right now. A privilege escalation in your security software is roughly equivalent to finding out your security guard is actually working for the thieves. The fact that this is in Defender specifically makes it particularly nasty, since Defender runs at the OS level on Windows systems worldwide.

https://www.techradar.com/pro/security/cisa-puts-us-government-agencies-on-two-week-deadline-to-patch-microsoft-defender-bluehammer-zero-day-exploit

**Marcin's comment:** Privilege escalation in the privilege escalation prevention software. That's not irony; that's just security theater doing a pratfall on stage.

## Nebu Security Tweet

Nebu Security raised an interesting point: AI isn't some new phenomenon for finding severe bugs - it's been finding kernel vulnerabilities in 15-year-old Linux code. This pushes back against the narrative that Anthropic's Mythos is uniquely capable of advanced vulnerability discovery. The broader point is that vulnerability-finding AI capability is more distributed than the headlines suggest. It's less "frontier AI unlocked new superpowers" and more "we finally made the tools work at scale."

https://x.com/nebusecurity/status/2045394811545641403?s=46

**Marcin's comment:** The Mythos hype cycle conveniently forgets that security researchers have been using automated tools to find bugs for decades. We just called them "fuzzing" and "static analysis" before AI made it sexy.

## OSINT Security Discussion

An OSINT researcher documented a vulnerability in Lovable AI that exposed complete source code, database credentials, and customer data. This is the speed-running version of "move fast and break things" where "break things" includes your entire security posture. The incident underscores how rapid AI development cycles often skip the tedious but essential business of security hardening. When you're iterating on product velocity, security review becomes an afterthought - until it becomes a catastrophe.

https://x.com/weezerosint/status/2046170666131669027?s=46

**Marcin's comment:** Exposing your database credentials in a public vulnerability is an achievement unlocked: "We shipped faster than we could think." Not recommended.

## Cyber News Update

The Cyber News account highlighted an MCP (Model Context Protocol) remote code execution vulnerability in Anthropic's ecosystem with massive supply chain implications - millions of downloads affected. MCP is infrastructure for AI agents to interact with tools and systems, so an RCE here means you're potentially giving compromised agents the keys to everything they're supposed to manage. Supply chain attacks through open-source infrastructure are the gift that keeps on giving to adversaries.

https://x.com/the_cyber_news/status/2046426265876594858?s=46

**Marcin's comment:** When your AI agent framework has an RCE vulnerability, you're not just compromising the AI - you're compromising whatever systems the AI is supposed to manage. It's delegation gone wrong.

## OSINT613 Security Tweet

A security researcher documented how a prediction market trader physically manipulated a weather sensor at Paris airport, exploiting Polymarket and demonstrating how blockchain oracles relying on single, unguarded data sources create spectacularly real-world vulnerabilities. This is the beautiful intersection of physical security failure and protocol design failure - one unguarded sensor becomes the single point of failure for millions in market value. It's a reminder that distributed systems fail in hilariously human ways.

https://x.com/osint613/status/2047626047005299131?s=46

**Marcin's comment:** "I'll just walk up to the airport and mess with this sensor that determines outcomes in a system millions rely on." This is what happens when you design oracles assuming physical infrastructure is secure. Spoiler: it's not.

---

# JVM

## Java SE Development Kit 8, Update 491 Release Notes

Oracle released JDK 8 Update 491 with crucial security baselines and IANA time zone data updates. The release includes significant bug fixes and, interestingly, disables runtime support for leap seconds by default - because leap seconds were apparently the kind of "feature" that nobody asked for but everyone had to accommodate. If you're still maintaining JDK 8 systems (and statistically, you probably are), this is your quarterly security patch reminder.

https://www.oracle.com/java/technologies/javase/8u491-relnotes.html

**Marcin's comment:** Leap seconds: the Y2K of temporal systems that actually happened and nobody cared about until it crashed something in production at 3 AM.

## Java SE Development Kit 17.0.19 Release Notes

JDK 17.0.19 arrived with updated time zone data and stricter TLS security policies. Specifically, certificates issued after March 17, 2026, anchored by certain CAs will now be rejected - a deliberate tightening of cryptographic compliance. This is the boring but necessary work of keeping Java secure as the threat landscape evolves. If you're running older versions with less restrictive TLS policies, this should motivate an upgrade.

https://www.oracle.com/java/technologies/javase/17-0-19-relnotes.html

**Marcin's comment:** "Reject certificates from these CAs" is a gentler way of saying "these issuers got compromised or turned out to be compromised years ago and we're finally doing something about it."

## JDK 27 Early-Access Release Notes

The JDK 27 early-access release notes unveil Post-Quantum Hybrid Key Exchange for TLS 1.3, preparing Java for the era when quantum computers actually exist and start breaking RSA. The addition of VM.security_properties diagnostic command also gives operators better visibility into security configuration. This is forward-thinking infrastructure work - making sure Java won't become obsolete the day someone boots a functional quantum computer.

https://jdk.java.net/27/release-notes

**Marcin's comment:** Post-quantum crypto in JDK 27 means Oracle is taking seriously the possibility of "harvest now, decrypt later" attacks. Smart move, even if quantum computers remain mostly theoretical.

---

# Spring

## Spring Boot 3.5.14 available now

Spring Boot 3.5.14 shipped with 48 bug fixes, documentation improvements, and crucial CVE patches - including fixes for timing attacks in DevTools. Timing attacks in DevTools sounds like something only experts would care about until your DevTools endpoint leaks information to anyone who knows how to measure nanoseconds. This is the kind of release where you do the update without asking questions.

https://spring.io/blog/2026/04/23/spring-boot-3-5-14-available-now

**Marcin's comment:** Timing attacks in DevTools: because apparently someone found a way to measure how long Spring takes to serve a request and infer secrets from nanosecond-level timing variations. Cybersecurity remains weird.

## Spring Security 2026.04 Releases - Contains CVE Fixes

The Spring team dropped Spring Security 6.5.10, 7.0.5, and 7.1.0-RC1 to patch multiple newly disclosed CVEs, including user attribute enumeration and servlet path matching misconfigurations. When the security framework itself has security issues, it's worth paying attention. These patches address real vulnerabilities that could let attackers bypass authentication or authorization checks in your application.

https://spring.io/blog/2026/04/21/spring-security-releases

**Marcin's comment:** When your security framework has CVEs, it's a good reminder that "defense in depth" means you should probably not rely exclusively on one layer anyway.

---

# Observability

## Grafana Labs Launches Grafana 13 at GrafanaCON 2026

Grafana Labs unveiled Grafana 13 with emphasis on unified open observability through OpenTelemetry and Prometheus. Dynamic dashboards are now the default, and Grafana Loki received a next-generation architecture overhaul. This is a significant step toward vendor-neutral observability - you're not locked into Grafana's proprietary metrics format; you're building on open standards. For operators managing complex systems, this matters.

https://www.morningstar.com/news/business-wire/20260421839174/grafana-labs-launches-grafana-13-at-grafanacon-2026-makes-open-observability-easier-to-run-at-scale

**Marcin's comment:** Dynamic dashboards by default means you might finally stop staring at static graphs that tell you nothing about what's currently breaking.

That's all for now.

Thanks again for being here, and see you in the next one.