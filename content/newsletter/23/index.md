---
title: "Issue #23"
date: 2026-05-23
lastmod: 2026-05-23
draft: false
description: "BitLocker gets bypassed, GraalVM accelerates, AI anxiety grows, and Spring Boot's migration chaos continues. Another week in the wild."
tags: ["newsletter"]
---

Hi!

Well, well, well. We've had quite the week. Someone found a way to bypass BitLocker that would make security teams reach for their stress balls and coffee simultaneously, GraalVM decided six-month releases were too quaint, governments are finally realizing AI disruption might actually impact humans, and Spring Boot 4 is here to remind everyone that migrations are a rite of passage in the Java world. Let's dive in.

Grab a hot beverage.

Let's go. I do hope that you'll enjoy the reading!

## This Week's Highlight

### Workers face growing 'automation anxiety' as tech layoffs surge, AI adoption accelerates

The flip side of all this AI enthusiasm: entry-level and junior developers are watching the job market shrink. Corporate AI investment is actively reducing hiring volume for the roles that used to be your training ground. It's not hysteria - it's a real labor market shift happening in real time. Whether you're a junior yourself or you care about the next generation of developers, this is the kind of trend that matters.

https://www.foxbusiness.com/economy/workers-face-growing-automation-anxiety-as-tech-layoffs-surge-ai-adoption-accelerates

**Marcin's comment:** The same companies automating away junior roles will be shocked when there's nobody experienced left to hire in five years. It's almost like you need a pyramid to build a pyramid.

---

## Shameless self-promotion

I'm doing mentoring and consulting for teams that want to improve software architecture, distributed systems, resilience, observability, developer workflows, and generally survive the AI-generated-code era without setting production on fire.

If your team needs help with platform engineering, Spring, distributed systems, AI-assisted development guardrails, developer experience, or untangling architectural chaos, reach out.

https://toomuchcoding.com/consulting

---

## AI

### Governor Newsom signs first-of-its-kind executive order to prepare workers and businesses for potential AI disruption

California's governor signed an executive order directing state agencies to study how AI impacts the labor market and to develop mitigation strategies. We're talking severance standards, workforce training programs, and even worker ownership models. This is the kind of forward-thinking policy that probably should have arrived five years ago, but I'll take it now. It's a signal that governments are finally asking the uncomfortable questions about displacement before it becomes a catastrophe.

https://www.gov.ca.gov/2026/05/21/governor-newsom-signs-first-of-its-kind-executive-order-to-prepare-workers-and-businesses-for-potential-ai-disruption/

**Marcin's comment:** California is planning for the future while most companies are still debugging the present. Classic.

### Bristol Myers deepens AI investment with Anthropic deal

Bristol Myers Squibb is betting heavily on Claude AI, integrating Anthropic's model across drug development, manufacturing, and quality control. Pharma companies moving this aggressively on AI is interesting - they've got the capital, the compliance infrastructure, and frankly the complexity that makes AI genuinely valuable (not just a buzzword generator). When a Fortune 500 company in a heavily regulated industry commits this publicly, it means they've done their homework.

https://www.biopharmadive.com/news/bristol-myers-deepens-ai-investment-anthropic-deal/820697/

**Marcin's comment:** Pharma companies adopting AI at scale while some software shops are still wondering if it's "real." I guess the medicine cabinet really is where innovation lives now.

---

## Security

### YellowKey: A BitLocker bypass through WinRE

CVE-2026-45585 is a nasty one. Researchers found a way to bypass TPM-only BitLocker encryption if you have physical access to a machine - the attack leverages the Windows Recovery Environment (WinRE) to grant an unrestricted command shell to the encrypted volume. This is the kind of vulnerability that makes you realize "TPM-only" encryption has a single point of failure if your recovery partition isn't locked down. If you're running BitLocker with TPM and Recovery without further hardening, you're living in a false sense of security.

https://www.threatlocker.com/blog/what-yellowkey-and-greenplasma-zero-day-exploits-reveal-about-trusting-native-windows-security

**Marcin's comment:** "BitLocker without full-disk encryption is like padlocking the front door while leaving the back window open." Sorry, I'll see myself out.

### CISA debuts new vulnerability reporting form

CISA rolled out a new online form for researchers and vendors to nominate vulnerabilities for the Known Exploited Vulnerabilities (KEV) catalog. It's supposed to streamline the process of getting active threats into public view so organizations know what to prioritize. The bureaucracy is still there, but at least they're trying to make reporting frictionless. Speed matters when you're dealing with actively exploited flaws.

https://www.meritalk.com/articles/cisa-debuts-new-vulnerability-reporting-form/

**Marcin's comment:** Finally, CISA noticed that web forms from 2003 don't scale. Welcome to modernity.

### CISA Adds Seven Known Exploited Vulnerabilities to Catalog

Seven new actively exploited flaws hit the KEV catalog, mostly historical Microsoft and Adobe vulnerabilities that organizations are still running unpatched. Federal civilian agencies have to prioritize these, but honestly, if you're still vulnerable to old Adobe flaws in 2026, you've got bigger problems than a compliance mandate. This is the kind of "we've told you a thousand times" situation that security teams live in.

https://www.cisa.gov/news-events/alerts/2026/05/20/cisa-adds-seven-known-exploited-vulnerabilities-catalog

**Marcin's comment:** Seven more ways that organizations are getting compromised while waiting for the next budget cycle. Nothing says "fun Friday" like mandatory patching orders.

---

## JVM

### GraalVM switches to monthly releases

Oracle's accelerating GraalVM releases from six months to monthly starting with version 25.1. They're keeping pace with rapid AI development and tooling advances - makes sense, since AI frameworks move faster than traditional enterprise software. You'll get features sooner, but you'll also need to stay vigilant about upgrading. For teams already tracking Graal releases, this is mostly good news. For teams that upgrade once a year, time to rethink your strategy.

https://www.jvm-weekly.com/p/jdk-27-takes-shape-while-graalvm

**Marcin's comment:** GraalVM went from "eventually, you'll get an update" to "surprise, there's a new version." Git commit faster.

### JDK 27 Early-Access Release Notes

JDK 27 is shaping up with some serious security and performance work. Post-Quantum Hybrid Key Exchange for TLS 1.3 is landing (preparing for the day quantum computers actually matter), and preview features like Lazy Constants and Structured Concurrency are maturing. The structured concurrency work is particularly interesting if you're building systems that need to manage millions of Virtual Threads correctly. This isn't flashy, but it's the foundation work that matters for building robust systems.

https://jdk.java.net/27/release-notes

**Marcin's comment:** Java is preparing for quantum computers while half the industry is still debugging synchronization issues. That's called "thinking ahead."

### Security support policy for the Kotlin standard library

JetBrains established an 18-month security support window for the Kotlin standard library with backports to active release lines. This is exactly what enterprise teams need - a clear commitment that critical fixes won't force you to upgrade your entire codebase immediately. If you're running Kotlin in production and needed a reason to feel better about it, here it is. This is the kind of formal policy that enables teams to adopt languages confidently.

https://blog.jetbrains.com/kotlin/2026/05/security-support-policy-for-the-kotlin-standard-library/

**Marcin's comment:** Kotlin finally grew up and wrote down its support policy. Maturity looks good on you.

### KotlinConf 2026 Keynote Highlights

JetBrains unveiled a unified Kotlin Toolchain and expanded backend capabilities at KotlinConf, including experimental gRPC support in kotlinx-rpc and AI-powered similarity search in Exposed. The gRPC support is long overdue if you're doing microservices in Kotlin. The AI similarity search in Exposed is genuinely interesting - building semantic search into the database framework is a smart move for the LLM era.

https://blog.jetbrains.com/kotlin/2026/05/kotlinconf26-keynote-highlights/

**Marcin's comment:** Kotlin is aggressively chasing the modern backend. I respect the hustle.

---

## Spring

### Spring Boot 4 Migration Guide: What Breaks and How to Fix It

Spring Boot 4 requires Java 21 as a minimum, integrates Jakarta EE 11, and drops Undertow support (among other changes). If you're still on Spring Boot 2 or early 3, this is your migration roadmap. The breaking changes are documented, the paths forward are clear, but let's be honest - migrations are never as smooth as the guides promise. Start testing early, plan for surprises, and budget time you think you won't need.

https://medium.com/javarevisited/spring-boot-4-migration-guide-what-breaks-and-how-to-fix-it-60373ca4683e

**Marcin's comment:** Spring Boot 4: Come for the features, stay because you're trapped in dependency hell.

### Spring Office Hours Podcast: S5E16 - May Release Train Shift & What's Coming in Spring Boot 4.1

The May 2026 release train slipped to early June, which means Spring Boot 4.1 is getting extra development time. Enhanced gRPC support and advanced Log4j file rotation strategies are incoming. The gRPC work is significant - Spring is finally making it a first-class citizen. This is the kind of "staying relevant" move that keeps Spring dominant in the ecosystem.

https://spring.io/blog/2026/05/19/spring-office-hours-podcast-S5E16

**Marcin's comment:** Spring slipped a release date to do the features right. Novel concept.

### Core Resilience Features in Spring Framework 7

Spring I/O 2026 featured native resilience patterns baked directly into Spring Framework 7 - RetryTemplate and ConcurrencyLimit are no longer optional extras. They're designed to work efficiently with Virtual Threads, which means you don't have to hack around thread pool exhaustion anymore. If you're building systems at scale, this is exactly the kind of foundation work that prevents 3am pages.

https://www.youtube.com/watch?v=hyHBmYwe-Hk

**Marcin's comment:** Spring finally realized that resilience isn't optional. It's only been twenty years.

### Spring Boot Managed Dependencies Still Get CVEs After EOL

Here's the uncomfortable truth: Spring Boot versions that reached end of life still accumulate critical CVEs from frozen upstream dependencies like Tomcat and Netty. You can't just sit on an EOL version and hope for the best - you're inheriting the security debt. The report emphasizes the operational burden of manually overriding dependency versions just to stay patched. This is a reality check for anyone running old Spring Boot in production.

https://www.herodevs.com/blog-posts/spring-boot-managed-dependencies-still-get-cves-after-eol-may-2026-patch-round-up

**Marcin's comment:** EOL doesn't mean "frozen in security amber" - it means you're now responsible for the security patches yourself. Enjoy!

---

That's all for now.

Thanks again for being here, and see you in the next one.