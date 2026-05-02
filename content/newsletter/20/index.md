---
title: "Issue #20: Goblins, Authentication Bypasses, and Spring Boot Release Candidates"
date: 2026-05-02
lastmod: 2026-05-02
draft: false
description: "This week: cPanel's CVSS 9.8 authentication bypass being actively exploited, OpenAI's goblin metaphor mystery, Linux privilege escalation in cloud environments, and a flood of Spring release candidates."
tags: ["newsletter"]
---

Hi!

Well, well, well. This week we've got everything: critical vulnerabilities actively being exploited in the wild, OpenAI's GPT models developing an inexplicable obsession with goblins (yes, really), a Linux privilege escalation vulnerability that's already got working exploits floating around, and the Spring ecosystem deciding to dump approximately fifteen release candidates on us simultaneously. It's like the universe is playing a prank on infrastructure teams everywhere. Speaking of pranks, you could say the security teams dealing with that CVE-2026-41940 cPanel bypass are having a *real* "authentication moment"... I'll see myself out. (That one was intentionally terrible, and I own it completely.) If you're running production systems this week, may your incident response channels be well-caffeinated and your backups actually verified.

Grab a hot beverage.

Let's go. I do hope that you'll enjoy the reading!

---

---
# Shameless self-promotion

Master distributed systems resilience and AI-driven architecture with my "Generate, Break, Fix: Distributed Systems in the AI Era" workshop on Maven. 

Use promo code TOOMUCHCODING for $100 off: https://maven.com/toomuchcoding/generate-break-fix-distributed-systems-in-the-ai-era
---

# This Week's Highlight

---

## CVE-2026-41940: cPanel & WHM Authentication Bypass

A CVSS 9.8 critical vulnerability in cPanel and WHM is actively being exploited in the wild right now. The flaw stems from CRLF injection in the session loading process, which allows unauthenticated remote attackers to gain full administrative access to affected systems. This isn't a theoretical "maybe someday this could be bad" situation—CISA and international cyber agencies are already sounding alarm bells because active exploitation campaigns are underway. If you're managing any infrastructure running cPanel or WHM, this needs to move to the top of your patch queue immediately.

https://www.rapid7.com/blog/post/etr-cve-2026-41940-cpanel-whm-authentication-bypass/

**Marcin's comment:** When your authentication bypass has a CVSS score in the high 9s and is currently being weaponized? That's not just a vulnerability—that's a fire alarm someone's actively pulling. Get on this.

# AI

---

## Where the Goblins Came From

OpenAI published a detailed post-mortem on a genuinely bizarre phenomenon: their GPT-5.1 and GPT-5.5 models started inexplicably overusing goblin and gremlin metaphors in their responses. After investigation, researchers traced the root cause back to a leaked reward signal from a discontinued 'Nerdy' personality profile that somehow made its way into the Reinforcement Learning from Human Feedback (RLHF) phase. This discovery highlights just how unpredictable and fragile reward loops can be when scaling language models—a stray signal from an old training run quietly corrupted the entire style distribution. It's a sobering reminder that even with the best intentions, unintended generalization of quirks across billions of parameters is an occupational hazard.

https://openai.com/index/where-the-goblins-came-from/

**Marcin's comment:** So somewhere in the training pipeline, a reward signal basically whispered "goblins are peak comedy" and the entire model said "say no more." This is what happens when your RL feedback loops have *feelings*. At least the goblins were harmless—imagine if that had been something actually problematic.

# Security

---

## CVE-2026-31431: Copy Fail Linux Vulnerability Enables Root Privilege Escalation

Microsoft researchers have detailed a high-severity Linux vulnerability dubbed "Copy Fail" that enables root privilege escalation across cloud environments and Kubernetes workloads. The exploit is already circulating in the wild, which means container orchestration platforms running vulnerable Linux kernels are actively at risk. Organizations relying on Kubernetes or cloud VMs need to prioritize detection and mitigation strategies urgently, as this affects the foundational security model of containerized infrastructure.

https://www.microsoft.com/en-us/security/blog/2026/05/01/cve-2026-31431-copy-fail-vulnerability-enables-linux-root-privilege-escalation/

**Marcin's comment:** When a privilege escalation exploit is already in the wild and affects Kubernetes, "urgent" becomes "why wasn't this done yesterday?" Time to test those kernel updates in staging before the weekend.

## ClickUp Data Exposure: Hardcoded SDK Token Reveals 959 Customer Email Addresses

Security researchers disclosed a massive data leak in ClickUp stemming from a hardcoded Split.io SDK token left in the production JavaScript bundle. The exposure compromised 959 customer email addresses and exposed internal feature flags, creating a textbook example of how credential management can fail at deployment time. This incident serves as a stark reminder that even well-intentioned platforms can suffer catastrophic information disclosure through a single oversight in the build pipeline.

https://x.com/weezerosint/status/2048662702957134199?s=46

**Marcin's comment:** Hardcoded tokens in production JavaScript. That's not an oversight—that's security archaeology waiting to happen. Always, *always* scan your build artifacts for secrets before pushing to production.

## Signal App: iOS Notification Bug Patch Restores End-to-End Encryption Guarantees

Signal's latest update addressed a critical iOS notification bug discovered by Apple where deleted messages could be recovered via preserved device notifications. This patch reinforces Signal's end-to-end encryption guarantees by eliminating a subtle attack vector that could have undermined their security model. The fix is a good reminder that encrypted messaging requires vigilance across the entire stack, not just the cryptographic core.

https://x.com/signalapp/status/2048866663580246302?s=46

**Marcin's comment:** Even encrypted messaging apps have to think about notification queues. Deleting a message that lives on in a notification? That's the kind of subtle threat model issue that keeps security engineers awake at night.

# JVM

---

## GraalVM Decouples from OpenJDK Release Cycle: The End of Metropolis

Oracle has formally decoupled GraalVM's release schedule from the core OpenJDK cycle, marking the end of the "Java-on-Java" Metropolis project. This strategic shift allows GraalVM to evolve independently while JDK 27 preparations continue on their own timeline. The change reflects growing recognition that the native image compiler and polyglot capabilities have matured enough to warrant their own innovation cadence.

https://www.jvm-weekly.com/p/the-rest-of-the-story-april-edition

**Marcin's comment:** Metropolis is officially over. GraalVM gets to be its own thing now. Whether that's liberation or fragmentation depends on how well the coordination works post-split.

## JDK 27 Finalized Release Schedule and Primitive Types in Patterns

The JDK 27 release schedule is now finalized, targeting General Availability for September 14, 2026. JEP 532 (Primitive Types in Patterns) has been promoted to "Proposed to Target," bringing pattern matching one step closer to handling primitive types directly. Oracle's April 2026 Critical Patch Update also addressed various CVEs across multiple Java versions, so check your patching roadmap.

https://www.infoq.com/news/2026/04/java-news-roundup-apr20-2026/

**Marcin's comment:** Primitive types in patterns would be *chef's kiss* for pattern matching completeness. We're finally getting close to treating primitives and objects symmetrically in match expressions.

# Spring

---

## Spring Boot 4.1.0-RC1 and Friends: The Great Release Candidate Wave of April 2026

The final week of April saw an absolutely massive wave of release candidates across the Spring ecosystem. Spring Boot 4.1.0-RC1 introduced support for OpenTelemetry SDK environment variables and LazyConnectionDataSourceProxy for connection pool optimization, while Spring Modulith 2.1.0 added the @ModuleSlicing annotation for streamlined integration testing. Spring Security, Integration, and AMQP all released their own RCs simultaneously, which either means coordination is excellent or chaos is imminent.

https://www.infoq.com/news/2026/04/spring-news-roundup-apr20-2026/

**Marcin's comment:** When the entire Spring portfolio drops RCs at once, it's either a well-orchestrated symphony or a comedy of release notes. The OpenTelemetry environment variable support in Boot 4.1 is solid though—that's going to make container deployments cleaner.

## Spring Boot 4.0.6: Bug Fixes, Dependency Upgrades, and CVE Patches

Spring Boot 4.0.6 landed on Maven Central with 65 bug fixes and critical dependency upgrades, including patches for CVE-2026-40970 and CVE-2026-40971. Those CVEs involved SSL bundle auto-configurations inadvertently disabling TLS hostname verification for Elasticsearch and RabbitMQ—a subtle but dangerous misconfig that could have silently compromised encrypted communications. If you're running Spring Boot 4.0.x, this release deserves immediate attention.

https://spring.io/blog/2026/04/23/spring-boot-4-0-6-available-now

**Marcin's comment:** SSL bundle autoconfig silently disabling hostname verification? That's the kind of bug that makes security teams question everything about defaults. Good catch by the Spring team, and good reminder to always verify your TLS settings explicitly.

## Spring Boot EOL: The 3.4 Branch Is Gone

The Spring Boot 3.4 branch reached end-of-life on December 31, 2025, so if you're still running it, the migration clock is ticking. Enterprise teams should be moving to the current stable Spring Boot 4.0.x branch, which requires Java 17 and introduces stable API versioning for HTTP endpoints. HeroDevs has published a definitive support lifecycle guide if you need to justify the upgrade to stakeholders.

https://www.herodevs.com/blog-posts/spring-boot-versions-eol-dates-and-latest-releases-april-2026

**Marcin's comment:** Spring Boot 3.4 EOL means your legacy 3.x deployments are living on borrowed time. The sooner you plan that 4.0 migration, the fewer surprises you'll encounter when support actually ends.

# Observability

---

## Prometheus 3.11.3: Security Patches for OAuth and Remote Read Vulnerabilities

Prometheus 3.11.3 shipped on April 27 with critical security fixes addressing an AzureAD remote write vulnerability where OAuth client secrets were exposed in plaintext, and a remote-read denial-of-service vector related to snappy-compressed requests. These patches resolve real attack vectors that could have compromised authentication tokens or crashed monitoring infrastructure. If you're running Prometheus in production, especially with remote write enabled, upgrade immediately.

https://github.com/prometheus/prometheus/releases

**Marcin's comment:** OAuth secrets in plaintext in remote write configs? That's the kind of misconfiguration that keeps on giving until it doesn't. Patch this yesterday.

## OpenTelemetry Collector v0.151.0: Standardizing Continuous Production Profiling

The OpenTelemetry project updated its Collector documentation for v0.151.0, outlining the strategic shift toward standardizing continuous production profiling alongside metrics, logs, and traces. The release emphasizes mixed stability levels of core components and internal telemetry configuration using Prometheus interfaces. This move signals that the observability community is finally treating profiling as a first-class citizen alongside traditional telemetry streams.

https://opentelemetry.io/docs/collector/

**Marcin's comment:** When profiling becomes part of the standard OpenTelemetry pipeline, you know the ecosystem is maturing. No more "profiling is a special case"—it's just another signal now.

## Datadog Agent Evolution: From Telemetry Collector to AI Execution Layer

Datadog detailed the transformation of their Agent from a passive telemetry collector into an AI-driven, programmable edge platform with the introduction of the "Private Action Runner." The new capability enables the agent to serve as a secure execution layer, natively integrating with OpenTelemetry standards and allowing real-time telemetry control via Remote Configuration. This represents a fundamental shift in how observability platforms can orchestrate intelligent responses to system events.

https://events.datadoghq.com/summits/datadog-summit-bengaluru/agenda/beyond-telemetry-the-evolution-of-the-datadog-agent-into-an-ai-execution-layer/

**Marcin's comment:** When your monitoring agent becomes an AI-powered action executor, the line between observability and control plane starts to blur. Interesting times ahead for edge intelligence in infrastructure.

---

That's all for now.

Thanks again for being here, and see you in the next one.