---
title: "TooMuchCoding Newsletter - Issue #27"
date: 2026-06-20
lastmod: 2026-06-20
draft: false
description: "Weekly newsletter for senior JVM/Java developers. This week: government blocks AI models, FortiBleed hits 86k devices, and Project Valhalla finally ships."
tags: ["newsletter"]
---

Hi!

Well, well, well. This week we've got the U.S. government deciding that some AI models are just *too good* to share globally, over 86,000 Fortinet devices getting absolutely dunked on by credential-wielding attackers, and - get this - Project Valhalla finally shipped after what feels like a decade of waiting. Plus Spring Boot 4.1 landed with some shiny new toys, and the observability world is collectively deciding that self-hosted Prometheus is getting too expensive to maintain. Let me grab my coffee before we dive into this mess.

Grab a hot beverage.

Let's go. I do hope that you'll enjoy the reading!

## This Week's Highlight

### Government Blocks Anthropic's Latest AI Model

The U.S. government just pulled the nuclear option on Anthropic's Fable 5 and Mythos 5 models, issuing an unprecedented export control directive that blocks global access for foreign nationals. Here's the kicker - Anthropic literally couldn't figure out how to reliably separate users by nationality without turning their entire infrastructure into a geopolitical sorting machine, so they just... shut the whole thing down worldwide. This is the first time the government has used export controls to directly halt a commercial AI model from operating, and it's throwing a spotlight on a genuinely terrifying risk: what happens when your entire product depends on access to a single AI service that can get yanked away by a regulatory decision?

https://alliantglobal.com/insights/government-blocks-anthropics-latest-ai-model/

**Marcin's comment:** I guess you could say the government found the *off switch* to global AI ambitions.

---

## Shameless self-promotion

I'm doing mentoring and consulting for teams that want to improve software architecture, distributed systems, resilience, observability, developer workflows, and generally survive the AI-generated-code era without setting production on fire.

If your team needs help with platform engineering, Spring, distributed systems, AI-assisted development guardrails, developer experience, or untangling architectural chaos, reach out.

https://toomuchcoding.com/consulting

---

## AI

### Department of Energy Generative Artificial Intelligence Policy

The U.S. Department of Energy released its foundational Generative AI Policy, which is basically their attempt to make sure AI tools don't accidentally help build weapons or leak classified materials while simultaneously unlocking scientific breakthroughs. The framework covers secure deployment, national security considerations, and integrity standards across their research ecosystem. It's the kind of thing that makes sense on paper - "let's use AI responsibly" - but executing it across a massive government organization is where things get spicy.

https://www.energy.gov/cio/articles/department-energy-generative-artificial-intelligence-policy

**Marcin's comment:** Nothing says "trust us" like a government AI policy written during a global AI arms race.

---

## Security

### CISA Warns Fortinet Customers as FortiBleed Hits 86,644 FortiGate Devices

A campaign called "FortiBleed" (because apparently we're naming malware after medical conditions now) has compromised over 86,000 internet-accessible Fortinet FortiGate appliances. Russian-speaking threat actors are using leaked credentials - both generic ones and organization-specific ones - rather than bothering with brute force, which tells you that credential dumps are flying around like confetti. The damage is concentrated in telecom, government, and education sectors, which are basically the places you'd least want compromised FortiGates sitting around.

https://thehackernews.com/2026/06/cisa-warns-fortinet-customers-as.html

**Marcin's comment:** It's the kind of mistake that makes security teams reach for their stress balls and coffee simultaneously.

### Splunk vulnerability (CVE-2026-20253) exploited

CISA added CVE-2026-20253 to its Known Exploited Vulnerabilities catalog - a critical remote code execution flaw in Splunk Enterprise that lets unauthenticated attackers invoke file operations via the PostgreSQL sidecar service endpoint. No authentication required. Full system compromise possible. The fact that this is actively being exploited in the wild means you should have already patched this if you're running Splunk, and if you haven't, well... let's just say your incident response team is about to get very familiar with each other.

https://www.helpnetsecurity.com/2026/06/19/splunk-vulnerability-cve-2026-20253-exploited/

**Marcin's comment:** Unauthenticated RCE via a sidecar service - it's like leaving your front door open and the back door open and then wondering why people are stealing your coffee maker.

### CISA mandates structured, prioritized updates for vulnerability management

CISA just issued Binding Operational Directive 26-04, which basically says "stop using CVSS scores as your vulnerability management religion" and replaced it with dynamic risk criteria. Federal agencies now have to account for public exposure, CISA KEV categorization, and automated exploitation capabilities when deciding what gets patched in the magical three-day remediation window. It's a pragmatic move - CVSS has always been kind of a blunt instrument - but it also means your vulnerability management process just got more complicated.

https://businessinsights.bitdefender.com/cisa-mandates-structured-prioritized-updates-vulnerability-management

**Marcin's comment:** Three days to patch a zero-day is ambitious, like planning to run a marathon after eating an entire pizza.

---

## JVM

### Project Valhalla Arrives: Value Classes Ship in JDK 28 After a Decade of Work

It finally happened. After more than a decade of development, Project Valhalla's JEP 401 (Value Classes and Objects) landed in the OpenJDK mainline as a preview feature targeting JDK 28. We're talking a 197,000-line commit that fundamentally rewires how Java allocates and manages memory. Value classes will give you the density and efficiency of primitive types without the pain of boxing, which is... honestly kind of a big deal for performance-sensitive code. I was speechless when I saw this finally merge - the JVM landscape is about to get interesting.

https://www.developersdigest.tech/blog/project-valhalla-jdk-28-value-classes

**Marcin's comment:** A decade to ship primitives that aren't primitive - welcome to the Java community.

### Java News Roundup: June 8th, 2026

The weekly Java roundup brings the general availability of the A2A Java SDK 1.0, which lets you run agentic applications on the JVM. There's also fresh early-access builds for JDK 27 and JDK 28, and progress continues toward Jakarta EE 12 milestone releases. It's a solid week for the ecosystem if you're tracking platform velocity.

https://www.infoq.com/news/2026/06/java-news-roundup-jun08-2026/

**Marcin's comment:** A2A stands for "Agent-to-Agent," which sounds way cooler than "code that runs code."

---

## Spring

### Spring Boot 4.1 Adds gRPC Auto-Configuration, SSRF Mitigation, and Kotlin 2.3 Support

Broadcom dropped Spring Boot 4.1, an incremental release built on Spring Framework 7.0.x that requires JDK 17 as baseline. The headliners are robust Spring gRPC auto-configuration (finally, built-in support), improved OpenTelemetry integration, and automatic Micrometer context propagation across threads for async methods. If you're building microservices or distributed systems, the observability improvements alone are worth the upgrade.

https://www.infoq.com/news/2026/06/spring-boot-4-1/

**Marcin's comment:** gRPC auto-configuration means less boilerplate and fewer "why isn't my gRPC service responding?" debugging sessions.

---

## Observability

### Grafana wins AI customers as observability shifts cloud

Grafana Labs reported that fast-scaling AI companies like 7AI, TeamSystem, and Zama are standardizing on Grafana Cloud instead of managing their own observability stacks. The pattern is clear: as AI workloads get more complex, teams are ditching self-hosted monitoring because the operational overhead of managing your own observability platform while also building AI products is just... not happening. About half of surveyed organizations are making this exact move away from self-hosted open-source tools toward managed platforms, mostly because controlling data ingestion costs becomes impossible once your traces and metrics explode.

https://itbrief.co.uk/story/grafana-wins-ai-customers-as-observability-shifts-cloud

**Marcin's comment:** Self-hosting your observability stack is great until your observability stack costs more than your actual infrastructure.

### Datadog shops AI incident management needs platform engineers

At DASH 2026, Datadog unveiled AI-assisted incident response tools - auto-tagging for security vulnerabilities, autonomous incident context engines, the works. But here's what the practitioners in the room actually emphasized: solid human-led platform engineering and structured incident response ecosystems are still the foundation. AI can make your incident response faster, but it can't replace the discipline of actually building systems that don't blow up every time you deploy.

https://www.techtarget.com/searchitoperations/news/366644443/Datadog-shops-AI-incident-management-needs-platform-engineers

**Marcin's comment:** AI incident management is great until you realize your incident response process was chaos to begin with.

---

That's all for now.

Thanks again for being here, and see you in the next one.
