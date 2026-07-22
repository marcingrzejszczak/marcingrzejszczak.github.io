---
title: "Issue #31 - Suing the Users Won't Fix the Problem"
date: 2026-07-18
lastmod: 2026-07-18
draft: false
description: "xAI sues users over Grok CSAM, Spring Boot 3.x reaches end of life, and OpenAI's GPT-Red tries to patch itself. A wild week in the JVM ecosystem."
tags: ["newsletter"]
---

Hi!

Well, well, well. This week we've got xAI making a legal move that will go down in history as possibly the dumbest PR strategy ever - they're suing users over generated content their own system produced. Meanwhile, Spring is saying goodbye to the entire 3.x branch, OpenAI is teaching GPT to red-team itself, and SharePoint is having another one of those weeks where every patch from 2019 suddenly matters again. Let's dive in before things get even weirder.

Grab a hot beverage.

Let's go. I do hope that you'll enjoy the reading!

## This Week's Highlight

### xAI Suing Users Over Grok CSAM Content

xAI has decided that the best way to address the fact that their Grok AI system generates child sexual abuse material is, you guessed it, to sue the users who surfaced the problem. I guess you could say they're really *escalating* the situation - sorry, I'll see myself out. This isn't a legal defense strategy, it's a scorched-earth public relations disaster wrapped in litigation. When you can't deny the problem anymore, apparently the move is to attack the people who brought it to light. The company is betting that legal threats will be more effective than, I don't know, actually fixing their model to not generate illegal content.

https://arstechnica.com/tech-policy/2026/07/xai-cant-deny-grok-makes-csam-anymore-so-its-suing-users/

**Marcin's comment:** Turns out the best defense is offense - specifically, offense against your customers. *Chef's kiss* to whatever legal team greenlit this one.

---

## Shameless self-promotion

I'm doing mentoring and consulting for teams that want to improve software architecture, distributed systems, resilience, observability, developer workflows, and generally survive the AI-generated-code era without setting production on fire.

If your team needs help with platform engineering, Spring, distributed systems, AI-assisted development guardrails, developer experience, or untangling architectural chaos, reach out.

https://toomuchcoding.com/consulting

---

## AI

### China's Kimi K3 Open Source Model

Report on China's release of Kimi K3, an open-source AI model competing with Anthropic's Opus.

https://www.axios.com/2026/07/17/china-ai-kimi-k3-open-source-anthropic-opus

**Marcin's comment:** "A Chinese moonshot - literally and figuratively — has caught up to models that defined the U.S. frontier just weeks ago, at a substantially lower price." - that's a sentence you don't want to read too often if you're an Anthropic or OpenAI employee...

### Unlocking self-improvement with GPT-Red

OpenAI has introduced GPT-Red, an automated red-teaming system that uses self-play reinforcement learning to discover and patch prompt injection vulnerabilities. Instead of humans manually trying to break things, the model is now teaching itself how to attack itself - which sounds like a therapy session but is actually a solid approach to resilience. The system significantly enhanced the robustness of the new GPT-5.6 Sol model against adversarial attacks. It's the kind of recursive thinking that makes you wonder if we're watching AI quality actually improve in real time, or if we're just moving vulnerabilities sideways.

https://openai.com/index/unlocking-self-improvement-gpt-red/

**Marcin's comment:** Self-play reinforcement learning for security is brilliant. Too bad it doesn't work for suing users.

---

## Security

### CISA warns of multiple SharePoint vulnerabilities under exploitation

The Cybersecurity and Infrastructure Security Agency has alerted organizations about active exploitation of multiple critical vulnerabilities in Microsoft SharePoint Server. Hackers are actively leveraging these flaws to steal machine keys and gain unauthorized access to deploy malware. If SharePoint is running in your infrastructure - and let's be honest, it probably is - you need to patch this right now. It's the kind of mistake that makes security teams reach for their stress balls and coffee simultaneously.

https://www.cybersecuritydive.com/news/cisa-multiple-vulnerabilities-sharepoint-exploitation/825306/

**Marcin's comment:** SharePoint: the gift that keeps on giving security teams nightmares.

---

## JVM

### Maven 3.9.15 Release Notes

Apache Maven released version 3.9.15 to fix a CVE in plexus-utils by upgrading the dependency to version 3.6.1. The update also improves console color output compatibility for systems running Java 26 and higher. If you're running the latest JVM versions, this isn't a luxury - it's a necessity to keep your build output readable and your security posture intact.

https://maven.apache.org/docs/3.9.15/release-notes.html

**Marcin's comment:** Maven keeping up with Java 26. Meanwhile, some teams are still on Java 8.

### Java News Roundup: TornadoVM 5, JHipster, Google ADK, OmniFish Build of Payara, Introducing Vidocq

TornadoVM 5.0.0 has reached GA and introduces a Hybrid API to run Java natively on NVIDIA GPUs. This is the kind of innovation that makes Java feel relevant in the AI and high-performance computing space. Additionally, GraalVM Native Build Tools released a maintenance update that resolved reachability metadata formatting issues. The JVM ecosystem is alive and well, even if it's not always obvious from mainstream tech discourse.

https://www.infoq.com/news/2026/07/java-news-roundup-jul06-2026/

**Marcin's comment:** Running Java on GPUs. Spring Boot on distributed systems. We're living in the good timeline.

---

## Spring

### Spring Boot 3.x Branch Is Now Unsupported

All Spring Boot 3.x branches, wrapping up with version 3.5.16, have officially reached end-of-life as of June 2026. If you're still on 3.x, you're no longer getting free security patches. This isn't a gentle deprecation - it's a hard line. Upgrade to Spring Boot 4.0 or higher if you want to keep your production systems from becoming a security liability.

https://www.danvega.dev/blog/spring-boot-end-of-life

**Marcin's comment:** Spring Boot 3.x had a good run. Time to move on.

### CVE-2026-41706 Detail

A vulnerability in Spring Security's request caching mechanism allows unvalidated absolute URLs to be used as post-login redirect targets. This opens the door to open redirect attacks. The flaw impacts multiple branches spanning the 5.x, 6.x, and 7.x series. If you're running any of those versions, you need to check your patch status immediately.

https://nvd.nist.gov/vuln/detail/CVE-2026-41706

**Marcin's comment:** Open redirects are the security equivalent of leaving your house door unlocked with a note that says "I'm out for coffee."

---

That's all for now.

Thanks again for being here, and see you in the next one.
