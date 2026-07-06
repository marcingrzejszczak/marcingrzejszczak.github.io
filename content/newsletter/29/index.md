---
title: "TooMuchCoding Newsletter #29"
date: 2026-07-04
lastmod: 2026-07-04
draft: false
description: "Claude Sonnet 5 lands as Anthropic's most agentic Sonnet yet, Fable 5 returns under cybersecurity limits, plus actively-exploited SharePoint and SimpleHelp CVEs - a quiet holiday week with a couple of loud releases."
tags: ["newsletter"]
---

Hi!

It's holiday season, half the industry is on a beach somewhere, and the news cycle has slowed to a trickle. Honestly, not too much is happening this week - which is a welcome breather. The one thing worth clearing your schedule for: Anthropic shipped Claude Sonnet 5. They also quietly brought Fable 5 back with cybersecurity uses fenced off. Beyond that it's the usual summer mix - a couple of nasty CVEs to patch before you log off, and some JVM and tooling housekeeping.

Grab a cold beverage this time.

Let's go. I do hope that you'll enjoy the reading!

## This Week's Highlight

### Introducing Claude Sonnet 5

Anthropic released Claude Sonnet 5, which they're calling their most agentic Sonnet yet. In their own words:

> Introducing Claude Sonnet 5, our most agentic Sonnet yet. It makes plans, uses tools like browsers and terminals, and runs autonomously at a level that just a few months ago required larger and more expensive models.

The headline is that the agentic capability which used to demand the big, expensive tier has trickled down into the mid-tier Sonnet line. Planning, tool use, terminals, browsers, and long autonomous runs are increasingly the baseline expectation rather than the premium feature. If you're building anything with agents, the economics just shifted in your favour.

https://x.com/claudeai/status/2072017450611142835?s=46

**Marcin's comment:** "Most agentic Sonnet yet" is a phrase that would have sounded like science fiction eighteen months ago and now reads like a routine changelog entry. That's the pace we're living at.

---

## Shameless self-promotion

I'm doing mentoring and consulting for teams that want to improve software architecture, distributed systems, resilience, observability, developer workflows, and generally survive the AI-generated-code era without setting production on fire.

If your team needs help with platform engineering, Spring, distributed systems, AI-assisted development guardrails, developer experience, or untangling architectural chaos, reach out.

https://toomuchcoding.com/consulting

---

## AI

### Anthropic Redeploys Fable 5 with Cybersecurity Uses Limited

Anthropic is bringing back access to Mythos/Fable, this time with cybersecurity uses disabled or limited. As Lukasz Olejnik noted, the announcement also confirms that GPT-5.5 and Kimi K2.7 were able to find the same security vulnerability that triggered the original export controls - meaning the concern was never unique to one lab's model. On top of that, the US Government will now get pre-release access to, and evaluation of, new powerful AI models before they ship.

https://www.anthropic.com/news/redeploying-fable-5

**Marcin's comment:** When multiple independent models all trip over the exact same vulnerability, that stops being a model problem and starts being an "everyone can do this now" problem. Pre-release government evaluation feels like the inevitable next chapter.

### Claude Science, an AI workbench for scientists

Anthropic shipped Claude Science, which is basically an IDE for researchers who are tired of bouncing between sixteen different tools to do genomics and proteomics work. It's got built-in access to compute resources, common research tools bundled in, and - this is the part that actually matters - fully auditable scientific artifacts. The reproducibility angle here isn't throwaway marketing speak, it's a direct answer to the "but how do I trust AI output in peer review" question.

https://www.anthropic.com/news/claude-science-ai-workbench

**Marcin's comment:** Finally, someone's making an AI tool where the audit trail is a feature, not an afterthought. Science moves fast when you're not fighting with your tools.

### How ChatGPT adoption has expanded

OpenAI's new Signals data shows ChatGPT adoption absolutely exploding in Africa and Asia, with users doing more stuff and sending more messages every day. We're past the "chatbot novelty" phase and into "people are actually integrating this into their workflows" territory. Deeper engagement, broader use cases - the kind of adoption curve that suggests this isn't just a fad for the tech crowd anymore.

https://openai.com/index/how-chatgpt-adoption-has-expanded/

**Marcin's comment:** When adoption accelerates in emerging markets, it means the value proposition has crossed over from "interesting toy" to "genuinely useful." That's worth paying attention to.

---

## Security

### CISA Warns of Actively Exploited Microsoft SharePoint Vulnerability

Here's the fun one: CVE-2026-45659 is a high-severity RCE in Microsoft SharePoint Server, it's already being exploited in the wild, and CISA has added it to their Known Exploited Vulnerabilities list with all the urgency that implies. The flaw is a deserialization issue that lets authenticated attackers with basically no privileges run arbitrary code. If you've got SharePoint running, this isn't a "get to it eventually" patch - this is a "this weekend" patch.

https://www.securityweek.com/cisa-warns-of-actively-exploited-microsoft-sharepoint-vulnerability/

**Marcin's comment:** Deserialization bugs are the gift that keeps on giving to attackers. If your SharePoint server isn't patched by Monday, you're not managing risk, you're just hoping.

### CVE-2026-48558: Critical Authentication Bypass Vulnerability in SimpleHelp RMM

SimpleHelp RMM has a critical authentication bypass (CVE-2026-48558) that's being actively exploited for credential theft and custom malware delivery. The root cause is improper validation of OpenID Connect token signatures - which is the kind of mistake that makes security teams reach for their stress balls and coffee simultaneously. CISA's already got it on the catalog with a strict remediation deadline. If you're using SimpleHelp, patch now.

https://arcticwolf.com/resources/blog/cve-2026-48558-critical-authentication-bypass-vulnerability-in-simplehelp-rmm-exploited-for-credential-theft-and-malware-delivery/

**Marcin's comment:** "Improper validation of token signatures" is a polite way of saying "we trusted the thing without verifying it." That's a rough way to learn the hard way.

---

## JVM

### GraalVM 25.1 is here!

Oracle's moved GraalVM to a monthly release cadence and they're not wasting any time. 25.1 brings significantly smaller native images, AI-powered reachability metadata generation, and WebAssembly GC support for polyglot sandboxing. The smaller native images are the headline - that's what matters for container deployments and serverless runtimes. This is the kind of release cadence that feels like what competitive pressure actually looks like.

https://medium.com/graalvm/graalvm-25-1-is-here-13829606982e

**Marcin's comment:** When your JVM alternative starts shipping faster than your JVM, you might want to pay attention. Smaller images and monthly releases - pick your favorite thing to be impressed by.

---

## Observability

### Grafana v13.1

Grafana 13.1 dropped and it's got stuff that actually makes dashboards less miserable - quick filters, data grouping, and copy/paste panel styles across dashboards. That last one doesn't sound revolutionary until you've manually recreated panel styling for the hundredth time. Grafana Assistant also expanded to query eight new data sources directly, which means fewer context switches when you're investigating incidents across your entire stack.

https://grafana.com/docs/grafana/latest/whatsnew/whats-new-in-v13-1/

**Marcin's comment:** Copy/paste panel styles sounds like the most boring feature request that actually saves hours. That's what good observability tools do.

---

That's all for now.

Thanks again for being here, and see you in the next one.
