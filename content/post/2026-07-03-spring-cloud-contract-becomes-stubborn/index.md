---
title: "Spring Cloud Contract Is Coming Home: Introducing Stubborn Contract"
summary: "Spring Cloud Contract is transitioning out of the Spring Cloud portfolio. I'm taking it forward as Stubborn Contract."
date: 2026-07-06

authors:
  - me

tags:
  - Spring Cloud Contract
  - Stubborn

content_meta:
  trending: false
---

TLDR; Spring Cloud Contract is transitioning out of the Spring Cloud portfolio. I'm taking the project forward under the [Stubborn](https://stubborn.sh) organization, as **Stubborn Contract**.

<!-- more -->

I created Spring Cloud Contract back in 2016 (and it's predecessor [Accurest](https://github.com/Codearte/accurest) 2 years earlier - 2014). Consumer-driven contract testing wasn't a mainstream idea in the Spring world at the time, and I spent years convincing teams that "if your contract tests pass, you can deploy with confidence" was worth building a whole tool around. It became one of the projects I'm proudest of, and I've been leading it ever since.

Today the Spring team and I are announcing that Spring Cloud Contract is moving to a new home. You can read [the official announcement on the Spring blog](https://spring.io/blog/2026/07/06/spring-cloud-contract-transition-to-stubbornsh) for their side of the story, and here's mine.

#### What's happening

Spring Cloud Contract is transitioning out of the Spring Cloud portfolio. Going forward, the project continues its development under the [Stubborn](https://stubborn.sh) organization, as the official continuation of Spring Cloud Contract, led by me.

This isn't a fork, and it isn't a rewrite. It's the same project, the same maintainer, and the same philosophy: contract tests as a first-class way to keep producers and consumers honest. What changes is where it lives and who it's funded by.

#### What this means if you're a current user

Spring Cloud Contract will no longer ship as part of future Spring Cloud release trains, and it's being pulled from the release trains that currently include it too. That's a real change, not a formality, so here's the practical guidance:

- **Nothing breaks today.** Keep using your current Spring Cloud Contract version while you plan.
- **Start planning your move to Stubborn Contract.** That's where fixes, features, and support are headed from here.
- **Watch [the Transition Guide](https://stubborn.sh/transition-guide)** for the exact dates and the migration steps (new Maven coordinates, package rename from `org.springframework.cloud` to `sh.stubborn`) as they're finalized. I'd rather publish real instructions once they're ready than guess at a date now.

#### What stays the same

The contract-testing approach you already know: the DSL, the verifier, the stub runner, HTTP and messaging support, all of it carries over. The project stays Apache 2.0 licensed and open source. If you've built your testing strategy around Spring Cloud Contract, that investment isn't going away, it's just getting a new home and, I hope, more attention than a portfolio project could give it.

#### Why this is the right move

Spring Cloud Contract is a testing framework that has never received the proper attention it deserved. Under Stubborn, it gets to be a first-class project again, with a roadmap, a broker for governing contracts across services and branches, and a funding model ([Enterprise Partnerships](https://stubborn.sh/enterprise)) that doesn't depend on me finding a free weekend. You can read more about how that's structured on the [Governance](https://stubborn.sh/governance) and [Roadmap](https://stubborn.sh/roadmap) pages.

I want to say a genuine thank you to the Spring team here. The relationship has been excellent throughout this whole process, and I'm grateful for how much room Spring Cloud Contract was given to grow over the years. It's rare for a transition like this to happen with this much goodwill on both sides. For sure [being part of the Spring team for almost a decade](https://spring.io/authors/marcin-grzejszczak) helped with the whole process.

#### What's next

If you're currently relying on Spring Cloud Contract in production, go read the [Transition Guide](https://stubborn.sh/transition-guide) and bookmark it. If your organization depends on it and wants a say in what gets built next, or dedicated support while you migrate, take a look at [Enterprise Partnerships](https://stubborn.sh/enterprise).

Everything else, the demo, the broker, the screenshots, is still at [stubborn.sh](https://stubborn.sh/broker/) if you haven't seen it yet.

More soon.
