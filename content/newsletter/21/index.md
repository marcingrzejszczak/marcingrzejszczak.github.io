---
title: "TooMuchCoding Newsletter #21"
date: 2026-05-09
lastmod: 2026-05-09
draft: false
description: "Issue #21: AI regulation, Linux privilege escalation, Maven & Gradle updates, Spring Boot 4.0, and observability innovations."
tags: ["newsletter"]
---

Hi!

This week we're diving into a regulatory plot twist that would make even the most cynical Silicon Valley executive raise an eyebrow: the U.S. government is suddenly interested in AI oversight (who would've thought?). We've also got a delightful nine-year-old Linux vulnerability that's only *now* being actively exploited (proof that patience is a virtue in the cybersecurity world), some shiny new JVM tooling to play with, and Spring Boot 4.0 making promises about virtual threads like they're going out of style. Plus, there's a tale of someone spending six months optimizing a Spring Boot app, only to have one upgrade solve everything overnight - a story that's either inspiring or deeply depressing, depending on your perspective. (I'd say it's a *boot*-iful reminder that sometimes the best optimization is letting someone else do it. Yes, I went there, and yes, I regret it immediately.)

Grab a hot beverage.

Let's go. I do hope that you'll enjoy the reading!

## This Week's Highlight

### Mythos Fallout, U.S. Government Weighs AI Model Regulation

The Trump administration is considering a significant regulatory pivot away from "move fast and break things" toward actual oversight of American AI models. This isn't some abstract policy debate - the concern is concrete: frontier models' capability to discover zero-day vulnerabilities poses genuine cybersecurity risks. A formal government review process before deployment could become the new reality, which means your AI model's deployment checklist might soon include "convince CISA your model won't accidentally nuke the internet."

https://www.lawfaremedia.org/article/mythos-fallout--u.s.-government-weighs-ai-model-regulation

**Marcin's comment:** Finally, someone realized that giving AI models free rein to find security holes without any oversight is precisely how we get into a cyberpunk dystopia. The irony is exquisite: we built AI to be unrestricted, and now we're shocked when it turns out unrestricted AI is... unrestricted. Who could have predicted this?

---

## Shameless self-promotion

Master distributed systems chaos and AI-era failure modes in one intensive workshop. Check out my "Generate, Break, Fix: Distributed Systems in the AI Era" workshop on Maven. 

Use promo code TOOMUCHCODING for $100 off: https://maven.com/toomuchcoding/generate-break-fix-distributed-systems-in-the-ai-era

IMPORTANT: If by the end of next week I will not have a proper amount of registrations then the workshops will be canceled! 

---

## AI

### Brian Armstrong on AI

Coinbase's CEO weighs in on where AI is headed and what it means for the tech ecosystem. This isn't a deep technical dive, but rather a high-level perspective from someone watching the landscape shift in real time.

https://x.com/brian_armstrong/status/2051616759145185723?s=46

**Marcin's comment:** A VC-backed executive discussing AI on Twitter - stop the presses, we've got hot takes incoming. Though to be fair, if anyone's position gives them a front-row seat to AI's financial implications, it's probably a crypto exchange CEO.

---

## Security

### CISA Adds Actively Exploited Linux Root Flaw to KEV Catalog

CVE-2026-31431, affectionately dubbed "Copy Fail," is a nine-year-old local privilege escalation vulnerability in Linux that's *finally* making headlines as actively exploited. The flaw allows unprivileged local users to obtain root access, which means if someone's already got a toehold on your system, they can waltz straight into root privileges. CISA adding it to the Known Exploited Vulnerabilities catalog is the security equivalent of saying "yeah, this is a real problem now" - better late than never.

https://thehackernews.com/2026/05/cisa-adds-actively-exploited-linux-root.html

**Marcin's comment:** A nine-year-old vulnerability that's just now being actively exploited? That's either a testament to Linux security's depth, or a reminder that threat actors sometimes take their sweet time. Either way, patching is not optional anymore.

### Palo Alto Networks Security Advisory: CVE-2026-0300

Palo Alto Networks disclosed a critical buffer overflow (CVE-2026-0300) in the User-ID Authentication Portal component of PAN-OS that allows unauthenticated remote attackers to execute arbitrary code with root privileges. This is the kind of vulnerability that auditors have nightmares about: remote, unauthenticated, and gives you complete system control. If you're running PAN-OS, this is a "drop everything and patch" situation.

https://security.paloaltonetworks.com/CVE-2026-0300

**Marcin's comment:** "Unauthenticated remote code execution with root privileges" is the security vulnerability equivalent of leaving your front door not just unlocked, but actively welcoming attackers inside with a sign that says "shoes optional but please wipe your feet." This one's serious.

### Chrome Silent Nano Install

Chrome's silent installation capabilities are raising eyebrows in the privacy community, with concerns about background installations happening without explicit user consent. It's a reminder that "silent" installs and "user consent" are increasingly becoming opposing forces in the modern web browser wars.

https://www.thatprivacyguy.com/blog/chrome-silent-nano-install/

**Marcin's comment:** Silent installs are the digital equivalent of someone rearranging your furniture while you're asleep. Sure, maybe it's convenient, but did we ask for this? The security community is rightfully concerned.

---

## JVM

### Maven 3.9.15 Release Notes

Apache Maven 3.9.15 is out, bringing dependency upgrades (including a CVE fix from plexus-utils) and improved colored console output for Java 26 and above. It's not earth-shattering, but it's solid, incremental improvement - the kind that makes you appreciate the Maven team's dedication to not breaking your builds.

https://maven.apache.org/docs/3.9.15/release-notes.html

**Marcin's comment:** Console output colored properly on Java 26? Finally, the JVM ecosystem realizes that developers appreciate feedback that doesn't require a colorblind interpretation guide.

### Gradle 9.5.0 Release Notes

Gradle 9.5.0 introduces improved task provenance in error reporting, enhanced JVM compatibility logging for daemons, and type-safe Kotlin accessors for precompiled Settings convention plugins. The provenance improvements alone are worth the upgrade - better error context means faster debugging cycles.

https://docs.gradle.org/current/release-notes.html

**Marcin's comment:** Task provenance in error reporting is developer experience gold. Knowing *why* a task failed and what triggered it is the difference between a five-minute fix and a two-hour investigation.

### Java OpenJDK April 2026 Patch & Security Update

Microsoft's OpenJDK build is bringing time-based heap uncommitting during idle periods, a feature that reduces memory footprint for applications that aren't constantly running at full throttle. This is the kind of JVM tuning that makes operations teams happy - less memory pressure without application-level changes.

https://devblogs.microsoft.com/java/java-openjdk-april-2026-patch-security-update/

**Marcin's comment:** Automatic heap uncommitting during idle periods is proof that the JVM ecosystem is finally taking seriously what everyone already knew: most applications spend more time idle than actually doing work.

### Java JVM Updates and Memory Improvements in Jenkins

The Jenkins team upgraded from Java 17 to Java 21 and saw monthly mean memory usage drop from 34 GB to 28 GB - a 17% reduction without touching a single line of application code. This is what responsible platform upgrades look like: better hardware utilization, improved performance, and minimal risk.

https://www.jenkins.io/blog/2026/05/08/JVM-upgrade/

**Marcin's comment:** A 6 GB monthly memory savings just from upgrading the JVM? If that doesn't convince your team to stop clinging to Java 11, nothing will.

---

## Spring

### Spring Boot 4.0 What's New

Spring Boot 4.0 establishes Java 17 as the baseline while recommending Java 21, enables virtual threads by default, and adopts Jakarta EE 11. It also leverages JSpecify null safety annotations across the framework. This is Spring saying "we're moving forward, and if you're not on at least Java 17, we're not waiting for you."

https://devops-monk.com/tutorials/spring-boot/spring-boot-4-whats-new/

**Marcin's comment:** Virtual threads enabled by default in Spring Boot 4.0 is the framework's way of saying "concurrency problems? Just throw more virtual threads at it." And honestly, for most use cases, that's not bad advice.

### I Spent 6 Months Optimizing a Spring Boot App... Then One Upgrade Made It Faster Overnight

A developer spent half a year hand-tuning a legacy Spring Boot application, only to achieve a 30% throughput increase instantly by migrating to a modern Spring Boot release. The upgrade yielded structural wins - reduced memory allocation, optimized query execution - that manual optimization couldn't touch. Sometimes the best optimization is someone else's optimization.

https://medium.com/javarevisited/i-spent-6-months-optimizing-a-spring-boot-app-then-one-upgrade-made-it-faster-overnight-3b84b6f814d4

**Marcin's comment:** This is either the most inspiring or most soul-crushing story in the entire newsletter, depending on whether you're the person who just upgraded or the person who spent six months hand-tuning queries. Either way, it's a reminder that framework improvements often matter more than micro-optimizations.

### Upgrade Spring Boot 4.0 (Moderne Edition)

Moderne released an automated refactoring recipe for Spring Boot 4.0 migration that handles build files, deprecated APIs, and framework migrations automatically. If you've been avoiding the upgrade because the migration looked tedious, this tool is your excuse to stop procrastinating.

https://docs.moderne.io/user-documentation/recipes/recipe-catalog/java/spring/boot4/upgradespringboot_4_0-moderne-edition/

**Marcin's comment:** Automated refactoring recipes are what we should have invented ten years ago instead of spending all that time arguing about tabs versus spaces. Press the button, let the tool work, and pretend you did it manually.

---

## Observability

### This Month in Datadog - April 2026

Datadog's latest update introduces an MCP Server giving AI agents real-time access to observability telemetry, along with Datadog Experiments for user journey analytics and Bits AI tools for autonomous Cloud SIEM investigations. The observability landscape is officially in "let AI figure it out" territory.

https://www.datadoghq.com/blog/this-month-in-datadog-april-2026/

**Marcin's comment:** AI agents investigating your infrastructure is either the future of DevOps or a cautionary tale about letting algorithms make security decisions without human oversight. Possibly both.

### Grafana Labs Releases Version 4 of Kubernetes Monitoring Helm Chart

Grafana Labs streamlined Kubernetes monitoring with a major update to the Kubernetes Monitoring Helm chart, tackling configuration complexity for large-scale deployments. Version 4 unifies metrics, logs, and traces into a cohesive observability stack, whether you're using Grafana Cloud or self-hosted infrastructure.

https://www.infoq.com/news/2026/05/kubernetes-monitoring-helm/

**Marcin's comment:** A Helm chart that actually simplifies Kubernetes observability is like finding a unicorn in your infrastructure. If this lives up to the hype, it's worth upgrading for.

That's all for now.

Thanks again for being here, and see you in the next one.
