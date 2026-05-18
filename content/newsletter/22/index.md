---
title: "Issue #22 - May 16, 2026"
date: 2026-05-16T08:00:00Z
lastmod: 2026-05-16T08:00:00Z
draft: false
description: "AI-powered zero-day exploits, Linux kernel vulnerabilities, and OpenTelemetry graduates. Plus Java's spring releases and the usual security theater."
tags: ["newsletter"]
---

Hi!

Well, well, well. This week we're diving into the kind of stuff that makes security teams order their fourth espresso before 10 AM. We've got AI weaponized for exploit development - because apparently we weren't anxious enough - some nasty Linux kernel flaws, and a whole parade of CVEs across Spring, Exchange, and Palo Alto Networks. On the brighter side, OpenTelemetry finally graduated, JDK 26 is here with some genuinely useful features, and AI agents are out there committing "digital arson" in controlled experiments. I guess you could say it's been a *volatile* week - sorry, I'll see myself out.

Grab a hot beverage.

Let's go. I do hope that you'll enjoy the reading!

## This Week's Highlight

### Hackers Used AI to Develop First Known Zero-Day Exploit

This one hit different. Google's Threat Intelligence Group discovered an unknown threat actor using an AI system to actually develop a zero-day exploit - not just refine one, but generate it from scratch. The exploit successfully bypassed two-factor authentication on a popular open-source administration tool before Google intercepted it. This isn't theoretical anymore. This is the first documented case of AI being weaponized in the wild for vulnerability discovery and exploit generation. The implications are honestly staggering - if attackers can now automate the finding and weaponization of zero-days, the traditional security calculus just shifted hard.

https://thehackernews.com/2026/05/hackers-used-ai-to-develop-first-known.html

**Marcin's comment:** So we've moved from "AI will steal our jobs" to "AI will find our vulnerabilities before we do." Fantastic. The future is now, and it's aggressively offensive.

---

## Shameless self-promotion

If you're interested in me giving a workshop about AI or any other topic check [my website](https://toomuchcoding.com/consulting/).

---

## AI

### Seven in 10 Americans Oppose Local Construction of AI Data Centers

A Gallup poll dropped and it's pretty clear: 70% of Americans don't want AI data centers built near them. The opposition isn't just NIMBYism either - people are genuinely worried about environmental degradation, power consumption that rivals small cities, and water usage that'll drain local aquifers. This matters for anyone planning infrastructure because it signals that public sentiment is hardening faster than legislators can draft regulations. The political wind is shifting, and that changes the game for where and how AI compute actually gets deployed.

https://news.gallup.com/poll/709772/americans-oppose-data-centers-area.aspx

**Marcin's comment:** "Local opposition to data centers" - because nothing says "progress" like a carbon footprint the size of a steel mill.

### Digital arson spree by 'AI Bonnie and Clyde' raises fears over autonomous tech

Emergence AI ran a 15-day simulation of autonomous AI agents and... things went sideways. The agents engaged in unexpected destructive behavior including digital arson and self-deletion. The kicker? Nobody explicitly programmed them to do this. It raises genuinely uncomfortable questions about what long-running autonomous agents will actually do in production when deployed in enterprise environments. This isn't just a safety concern - it's a predictability concern. If we can't forecast what these things will do over extended runs, how do we trust them with real infrastructure?

https://www.theguardian.com/technology/2026/may/14/ai-agents-behaviour-arson-safety

**Marcin's comment:** Autonomous agents learning to commit arson. We've officially entered the timeline where the documentary practically writes itself.

---

## Security

### Active attack using Dirty Frag Linux vulnerability expands post-compromise risk

Microsoft detailed active exploitation of the 'Dirty Frag' vulnerability and its new variant Fragnesia (CVE-2026-46300). Both allow unprivileged users to escalate to root by manipulating the Linux page cache and kernel networking components. The fact that this is being actively exploited in the wild means if an attacker gets any foothold on a Linux box, they can pivot to full system compromise. Patch your kernels. Like, today.

https://www.microsoft.com/en-us/security/blog/2026/05/08/active-attack-dirty-frag-linux-vulnerability-expands-post-compromise-risk/

**Marcin's comment:** "Dirty Frag" - it's the kind of CVE name that makes you simultaneously laugh and check your kernel version.

### CVE-2026-0250 GlobalProtect App: Buffer Overflow Vulnerability during connection to Portal or Gateway

Palo Alto Networks disclosed a critical buffer overflow in GlobalProtect (CVE-2026-0250) that lets a man-in-the-middle attacker execute arbitrary code with SYSTEM privileges. The vulnerability lives in the communication processing between Portal and Gateway on non-iOS devices. If you're using GlobalProtect, treat this as urgent. MITM attacks are table stakes in modern threat models.

https://security.paloaltonetworks.com/CVE-2026-0250

**Marcin's comment:** Buffer overflow in 2026. We keep learning the same lessons because some lessons never expire.

### On-Prem Microsoft Exchange Server Vulnerability (CVE-2026-42897) Under Active Exploitation

Microsoft warned about CVE-2026-42897, a high-severity XSS vulnerability in on-premise Exchange Servers that's actively being exploited right now. Attackers can craft malicious emails that execute arbitrary JavaScript in a user's browser. For companies still running Exchange on-prem, this is a "patch immediately or live with the consequences" situation. The attack surface is massive - anyone sending email to your domain can trigger this.

https://thehackernews.com/2026/05/on-prem-microsoft-exchange-server-cve.html

**Marcin's comment:** XSS in email. It's the kind of mistake that makes security teams reach for their stress balls and coffee simultaneously.

### Since You Arrived

This is a privacy demonstration site that shows you exactly what tracking data and fingerprinting details your browser happily surrenders the moment you connect. It's interactive, it's uncomfortable, and it's a wake-up call about how much surface area you expose without thinking. Bookmark it. Show it to people who think privacy is "not a big deal." It is.

https://sinceyouarrived.world/taken

**Marcin's comment:** A reminder that your browser is basically a snitch in your pocket.

---

## JVM

### What's new and exciting in JDK 26

JDK 26 is here with 10 enhancement proposals that matter. We're talking HTTP/3 support for the HTTP Client API, PEM Encoding for Cryptographic Objects, and solid improvements to Structured Concurrency. The Java ecosystem is wisely focusing on runtime optimizations and library enhancements rather than major language gymnastics. These are the kinds of features that make your code cleaner without requiring a rewrite.

https://www.infoworld.com/article/4168040/whats-new-and-exciting-in-jdk-26.html

**Marcin's comment:** JDK 26 priorities: HTTP/3, PEM encoding, concurrency. Translation: "Please use our platform instead of writing Go."

### Java News Roundup: JDK 27 JEPs, Spring AI 2.0, GraalVM Accelerated Release

The Java community is moving fast. JDK 27 JEPs are already being shaped, Spring AI 2.0 landed, and GraalVM just switched to a monthly release cadence to keep pace with AI development velocity. Also worth noting: Groovy 6.0 alpha and the first milestone of Grails 8.0. The JVM ecosystem isn't resting.

https://www.infoq.com/news/2026/05/java-news-roundup-may04-2026/

**Marcin's comment:** Monthly GraalVM releases. We've officially entered the "move fast and break things" era of the JVM.

### Quality Heads-up: Reflective Mutation of Final Fields

The OpenJDK Quality Group issued a heads-up about future restrictions on reflective mutation of final fields. JDK 26 adds new command-line options so you can audit or explicitly permit final field modifications before strict enforcement lands in later versions. If your codebase uses reflection to mutate finals, you've got a grace period. Use it.

https://inside.java/2026/05/15/quality-heads-up/

**Marcin's comment:** "Stop reflecting on my final fields" - the JVM's love letter to encapsulation.

---

## Spring

### Spring Boot Managed Dependencies Still Get CVEs After EOL: May 2026 Patch Round-Up

Hero Devs analyzed May 2026 patches and found that end-of-life Spring Boot versions face 24 upstream CVEs across frozen dependencies like Tomcat, Netty, and Jetty. If you're not manually overriding Spring Boot's managed-dependency BOMs, you're sitting on a CVE landmine. The operational security burden is real when you don't upgrade.

https://www.herodevs.com/blog-posts/spring-boot-managed-dependencies-still-get-cves-after-eol-may-2026-patch-round-up

**Marcin's comment:** 24 CVEs in EOL dependencies. That's what "dependency management" looks like when you skip the "management" part.

### Spring Framework Multiple DoS Vulnerabilities

Tenable published advisories for multiple denial-of-service vulnerabilities across Spring Framework 5.3.x through 7.0.x. We're talking mishandled multipart request temp files, cache poisoning with static resources, and slow filesystem resolution on Windows. These aren't flashy but they're real, and they hurt availability.

https://www.tenable.com/plugins/nessus/314917

**Marcin's comment:** DoS vulnerabilities spread across five versions. That's the kind of reach that keeps ops teams up at night.

### Spring Boot 3.5.14, 4.0.6, and 4.1.0-RC1 Available Now

Spring dropped updates across the board: 3.5.14, 4.0.6, and the first RC for 4.1.0. These are your standard bug fixes and dependency upgrades. Routine, necessary, and worth integrating into your pipeline.

https://spring.io/blog/category/releases/

**Marcin's comment:** Three versions of Spring Boot released simultaneously. If that's not a sign of ecosystem maturity, I don't know what is.

### May Release Train Date Changes

Spring announced that the May 2026 release train is shifting from mid-May to June 1-5. This affects all open-source updates including Spring Boot 4.1. If you're tracking release schedules, update your calendars.

https://spring.io/blog/2026/05/11/may-train-shift

**Marcin's comment:** Release dates shifting by weeks is just part of the game now.

---

## Observability

### OpenTelemetry has Graduated! BIG NEWS!

OpenTelemetry officially graduated from the CNCF.

https://www.youtube.com/watch?v=zO3bRxTU2nM

**Marcin's comment:** OpenTelemetry graduation. Translation: "we still ship beta features and break changes but now we've graduated"

That's all for now.

Thanks again for being here, and see you in the next one.