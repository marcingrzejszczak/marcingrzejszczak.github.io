---
title: "TooMuchCoding Newsletter - Issue #24"
date: 2026-05-30
lastmod: 2026-05-30
draft: false
description: "Claude Opus 4.8, security breaches, and Gradle's Javamaxxing strategy"
tags: ["newsletter"]
---

Hi!

Well, well, well - looks like it's been quite the week in the Java ecosystem and beyond. We've got Claude Opus 4.8 stealing the AI spotlight while making developers wonder if they should start updating their CVs, the Netherlands playing real-life cybersecurity whack-a-mole with 800 seized servers, and Gradle apparently decided that Java 21 is the new baseline because half-measures are so 2020.If you're still running legacy JVM code, Stripe's got a whole playbook on how to not die trying to upgrade. Let's dive in - I promise the security stories won't keep you up all night, but they might make you reconsider your MFA strategy. Fair warning: there may be a pun or two ahead. I apologize in advance.

Grab a hot beverage.

Let's go. I do hope that you'll enjoy the reading!

## This Week's Highlight

### Claude Opus 4.8

Anthropic dropped Claude Opus 4.8, and they're calling it their most capable generally available model. We're talking serious upgrades in complex reasoning, long-horizon agentic coding, and tool calling - which means this thing can actually think through multi-step problems without forgetting where it started. The prompt cache threshold is lower, so you're not token-bleeding money on repetitive inputs anymore. And here's the kicker: it's already baked into GitHub Copilot with the same pricing as before, which either means Anthropic is confident as heck or they're willing to take a margin hit to own developer mindshare.

https://www.anthropic.com/news/claude-opus-4-8

**Marcin's comment:** So the AI model got better at reasoning and nobody's paying more? I guess you could say that's a pretty *opus* move. Sorry, I'll see myself out.

---

## Shameless self-promotion

I'm doing mentoring and consulting for teams that want to improve software architecture, distributed systems, resilience, observability, developer workflows, and generally survive the AI-generated-code era without setting production on fire.

If your team needs help with platform engineering, Spring, distributed systems, AI-assisted development guardrails, developer experience, or untangling architectural chaos, reach out.

https://toomuchcoding.com/consulting

---

## AI

### OpenAI's Frontier Governance Framework

OpenAI published a Frontier Governance Framework to align its safety and security practices with emerging legal requirements like the EU AI Act and California's Transparency in Frontier AI Act. The framework outlines systemic risk assessment protocols covering cyber offenses, CBRN risks, and harmful manipulation - basically all the cheerful stuff that keeps security teams reaching for their stress balls and coffee simultaneously. This is what happens when regulators finally start asking uncomfortable questions about what happens when you let AI systems operate at scale.

https://openai.com/index/openai-frontier-governance-framework/

**Marcin's comment:** When your AI governance framework needs its own governance framework, you know we've entered a new era of corporate theater.

### Zero Evidence of AI-Related Job Losses

Apollo's analysis suggests there is currently zero evidence of AI-related job losses in the broader market - which is either reassuring or the calm before the storm, depending on your optimism levels. The AI spending boom is actually creating demand for implementation experts and driving up employment and infrastructure costs, so the gig right now is being the person who knows how to wire this stuff together. The real question is whether that honeymoon lasts or if we're just seeing the eye of the hurricane.

https://www.apollo.com/wealth/the-daily-spark/zero-evidence-of-ai-related-job-losses

**Marcin's comment:** No job losses yet - just a growing ecosystem of "AI implementation experts" who are desperately Googling how to use the APIs they just installed.

---

## Security

### Netherlands Seizes 800 Servers, Arrests 2 for Aiding Cyberattacks

Dutch authorities arrested the co-owners of two internet hosting companies and seized over 800 servers in a coordinated operation. These networks were allegedly used by Russian intelligence agencies as a staging ground for cyberattacks and disinformation campaigns across the European Union - so we're talking infrastructure-level stuff, not just some script kiddie running botnets from their mom's basement. This is the kind of operation that shows state-level threats are getting bolder about using criminal proxies to maintain plausible deniability.

https://krebsonsecurity.com/2026/05/netherlands-seizes-800-servers-arrests-2-for-aiding-cyberattacks/

**Marcin's comment:** 800 servers seized. Somewhere, a CISO is updating their incident response plan to include "call the Dutch."

### FBI warns of phishing platform bypassing Microsoft 365 MFA

The FBI issued a warning about Kali365, a phishing-as-a-service platform that allows cybercriminals to bypass multifactor authentication on Microsoft 365. The service leverages device code phishing to access OAuth tokens and establish persistent environment access - which means attackers are basically getting handed the keys to the kingdom through the front door while your MFA sits there looking useless. This is the kind of exploit that makes security teams realize that "something you have" isn't worth much if the something is just another code on a screen.

https://www.cybersecuritydive.com/news/fbi-warns-phishing-platform-microsoft-365/821105/

**Marcin's comment:** MFA bypass platform as a service - because why go through the effort of building your own exploit when you can just subscribe monthly?

### 'Claw Chain' Attack Turns OpenClaw Sandbox Into Launchpad for Full Compromise

Cyera discovered a severe exploit chain named 'Claw Chain' that breaks out of the OpenClaw sandbox and achieves persistent system-level control. The attack exploits four vulnerabilities in sequence to completely bypass the sandbox security model - which is the kind of demonstration that should make anyone relying on "sandboxes" for security have a very serious conversation with their engineering team. Agent sandboxes are supposed to be the last line of defense for untrusted code, and when they fail, the entire trust model collapses.

http://iansresearch.com/resources/all-blogs/post/security-blog/2026/05/26/claw-chain'-attack-turns-openclaw-sandbox-into-launchpad-for-full-compromise

**Marcin's comment:** A sandbox escape chain so clever it probably deserves its own CVE-as-a-service business model.

---

## JVM

### How Gradle Is Javamaxxing

Gradle is aggressively adopting modern JDK releases under a strategy it calls 'Javamaxxing' - which is exactly as blunt as it sounds. By upgrading the daemon JVM minimum requirements to Java 17 for Gradle 9 and Java 21 for Gradle 10, builds benefit from better garbage collection, optimized JIT compilation, and all the performance gains you've been leaving on the table by running Java 11. This is Gradle basically saying: if you want our latest features and performance, you're modernizing whether you like it or not.

https://blog.gradle.org/gradle-is-javamaxxing

**Marcin's comment:** "Javamaxxing" might be the worst name for a technical strategy ever, but it's also the most honest.

### Modern Java at Stripe: Language upgrades as a service

Stripe shared its engineering strategy for migrating large enterprise codebases to modern Java versions through a 'language upgrades as a service' model. They've built tooling and processes to minimize the operational cost of keeping massive legacy JVM codebases updated without turning every engineer into a dependency resolver. If you're running a polyglot organization with thousands of services all stuck on different Java versions, Stripe basically just handed you a blueprint for how to stop that being a nightmare.

https://stripe.dev/blog/modern-java-at-stripe-language-upgrades-as-a-service

**Marcin's comment:** "Language upgrades as a service" - proof that Stripe has enough engineers to turn even version bumping into a managed platform.

### Endive: A JVM native WebAssembly runtime

The Bytecode Alliance announced Endive, a fork of Chicory that acts as a pure Java, zero-dependency WebAssembly runtime. You can now run Wasm securely on the JVM without relying on native FFI code, maintaining all JVM memory and security guarantees - which means you're getting the flexibility of WebAssembly without punching holes in your security model. This is the kind of thing that opens up new deployment patterns for teams running containerized Java applications.

https://github.com/bytecodealliance/endive

**Marcin's comment:** WebAssembly on the JVM without native code - it's like running an emulator inside an emulator, but actually useful.

---

That's all for now.

Thanks again for being here, and see you in the next one.
