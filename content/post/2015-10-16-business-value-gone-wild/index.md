---
title: "Business Value Gone Wild"
summary: "An opinionated look at the 'it's not my problem' mindset and the management frenzy around 'business value'—and how it clashes with engineering quality."
date: 2015-10-16
authors:
  - me
tags:
  - Blog
content_meta:
  trending: false
---

This blog post will not be about microservices, Spring, or any technology that I've already talked about in the [Too much coding blog](https://toomuchcoding.com/). This time it will be my opinion on two subjects:

- the more and more frequent “it’s not my problem” approach in the IT industry running in a corporation;
- the “business value” frenzy of the management.

This article is definitely not a motivational one. Quite frankly, you might get depressed after reading it. Nonetheless, it's better to know how corporate life sometimes really looks rather than get hit in the face.

**TL;DR:** the more you care in a corporate enterprise, the worse for you. Eventually some developers will hate your ideas of quality and standards because they are paid to tap the keys. Your management will fire you for not bringing “business value.” The faster you embrace it, the better for you — you’ll start searching for a new job sooner.

<!--more-->

## Features are not only functionalities

Let's define some facts: IT is paid by the business. Business wants features. IT has to deliver features to gain money. That’s a fact and our reality. Even if you hear from your managers that “cleaning technical debt is a necessity,” what they really think is:

[![Technical debt](https://4.bp.blogspot.com/-htlLzbCyZro/Vh6fjoKG5iI/AAAAAAABILw/_zk7wkxqPoU/s320/technical_debt.jpg)](https://4.bp.blogspot.com/-htlLzbCyZro/Vh6fjoKG5iI/AAAAAAABILw/_zk7wkxqPoU/s1600/technical_debt.jpg)

And actually that’s not bizarre — business has no understanding of the technical aspects of IT work. Here we can discern two types of business people:

- they don’t get technical aspects, but they trust the engineers;
- they don’t care about technical aspects and they won’t listen to any of the programmers’ advice.

If you have the latter business people, then most likely you’re in this situation:

[![Cable mess before](https://3.bp.blogspot.com/-LwS3CWbA0b0/Vh6qyT0FmbI/AAAAAAABIMM/1xY8oezZhFk/s1600/before.jpg)](https://3.bp.blogspot.com/-LwS3CWbA0b0/Vh6qyT0FmbI/AAAAAAABIMM/1xY8oezZhFk/s1600/before.jpg)

…and actually you should be doing such a shift:

[![Cable management after](https://1.bp.blogspot.com/-TCHAm6Q0yT0/Vh6oKJF3mLI/AAAAAAABIMA/pjQoINeQ35c/s320/cable_change.jpg)](https://1.bp.blogspot.com/-TCHAm6Q0yT0/Vh6oKJF3mLI/AAAAAAABIMA/pjQoINeQ35c/s1600/cable_change.jpg)  
*Source: https://www.technalytical.com/2012/04/aesthetical-cable-management-before-and.html*

In order to grow faster. What makes me really surprised is that continuously the business picks the first option — just add more mess to the existing one without thinking of the consequences.

Now for the tricky part. Change the word “business” to “developer” and everything is still valid.

“Delivering a feature” is not only coding some functions in whatever language you are using. It’s not taking a keyboard and pressing the keys to make the functionality work. If this is your approach, then you’re a key tapper. Tapping keys to get things done.

[![Key tapping](https://4.bp.blogspot.com/-N9ueTp3dNhI/Vh6sGeO-OUI/AAAAAAABIMY/ZTgNPN9Pras/s320/dunno.jpg)](https://4.bp.blogspot.com/-N9ueTp3dNhI/Vh6sGeO-OUI/AAAAAAABIMY/ZTgNPN9Pras/s1600/dunno.jpg)

## Programming is more than tapping keys

I hope that nobody feels offended by this term “key tapper.” I’m not trying to be offensive — I’m just describing what I saw in my career. In my opinion there are a couple of different types of IT folks:

- people for whom programming is a passion; they put a lot of energy and effort to make things better;
- IT folks for whom programming isn’t a passion, but still **successfully** put a lot of energy and effort into making things better because they want to be honest and valuable employees (thanks, Michal Szostek);
- people for whom programming is not a passion and they just come to work and tap the keys;
- others who would love to do stuff properly but the business is breathing down their necks to do things in a bad way because the “deadlines are coming”;
- positions where people last by simulating work — they lie, talk a lot, and delegate so there’s an impression of progress.

Regardless of the position, if one doesn’t focus on quality and just taps in the functionality, then:

- even if they provide the business feature, it might badly influence other people (introducing coupling between modules, breaking encapsulation, etc.);
- the functionality might be written in such a way that you end up with a global timeout of the whole system;
- you’re not thinking about company standards (e.g. passing of CorrelationID, for instance), which will break the approaches set in the company — increasing support time;
- writing the next functionality will take more time than the previous one.

Even though it seems common knowledge, you can far too often hear something like this:

> I don’t have time for this — it’s not my problem. I’ve delivered my business feature and this is what I’m paid for. What you’re referring to is not of my interest.

Now imagine that you join a project full of such developers and you’re asked to fix a bug:

[![New guy fixing bugs](https://2.bp.blogspot.com/-c6LCeYh8sHs/Vh7A-E-ZjaI/AAAAAAABIM0/gelqAg9YKU0/s400/new_guy.png)](https://2.bp.blogspot.com/-c6LCeYh8sHs/Vh7A-E-ZjaI/AAAAAAABIM0/gelqAg9YKU0/s1600/new_guy.png)

## Technical changes are not bringing money

We have to educate both the business and the developers: writing features and providing business value is actually a sum of coded and tested functionality **plus** technical advancement. What are those? Code refactoring, introduction of new approaches, and migrations from one way of doing things to another. For example:

- version control system (e.g., SVN → Git);
- build system (e.g., Maven → Gradle);
- UI framework (e.g., Vaadin → AngularJS);
- library versions (e.g., Spring 3.0 → Spring 4.0);
- going from deployment to application servers to embedded servlet containers (e.g., GlassFish → embedded JAR with Jetty).

Why do we want these changes to happen? Because they ease our work and enforce standards. Why are standards important?

*“Pick a plug they said, it’s gonna be easy, they said.”*

[![Plugs](https://4.bp.blogspot.com/-StjBYB5gZOE/Vh7DWYGKYLI/AAAAAAABINI/7027zJf7kN8/s400/plugs.jpg)](https://4.bp.blogspot.com/-StjBYB5gZOE/Vh7DWYGKYLI/AAAAAAABINI/7027zJf7kN8/s1600/plugs.jpg)  
*Source: https://abdulinnewzealand.wordpress.com/2012/12/03/new-things-from-my-visit-to-new-zeland/*

If every team in the company uses different:

- libraries,
- approaches to testing,
- approaches to deployment,
- approaches to running the application,

then you can tell your business that they will pay **a lot** of money for support. The learning curve will be gigantic for newcomers. But hey! It’s better to code a new functionality in the meantime, right?

Seemingly, all developers would like to see the effect of those migrations and standardization. Everybody wants this to happen — but who should actually do it? When asked about this you might hear:

> I don’t have time for this — it’s not my problem. I’ve delivered my business feature and this is what I’m paid for. What you’re referring to is not of my interest.

How can we solve this?

**Stupid idea**

Introduce the following flow of working in IT:

- the “coding team” writes a business feature and pushes it to `master`;
- the “clean code team” rewrites the code according to clean code standards;
- the “technical team” introduces the technical standards for the written piece of code;
- the “migration team” migrates the code from one approach to another.

The outcome of the cooperation could look like this:

[![Bathroom mismatch](https://2.bp.blogspot.com/-8PyO94v8WnQ/Vh7FQYoj_TI/AAAAAAABINU/NfuIHRnzdZQ/s320/bathroom.jpg)](https://2.bp.blogspot.com/-8PyO94v8WnQ/Vh7FQYoj_TI/AAAAAAABINU/NfuIHRnzdZQ/s1600/bathroom.jpg)

**Good idea**

Introduce… caring! Invest time and effort in educating business and developers that you have to take care of code quality. Imagine where your company would be if every programmer focused for **one hour per day** on managing technical debt. If your managers don’t understand the importance of clearing that debt, then you should consider changing jobs — because it’s going to get worse with every single push to the repo.

## You are an engineer!

[![Engineer](https://2.bp.blogspot.com/-pGZjd5My6EU/Vh7LQrT18oI/AAAAAAABINg/ku5r63yr3oY/s320/engineer.jpg)](https://2.bp.blogspot.com/-pGZjd5My6EU/Vh7LQrT18oI/AAAAAAABINg/ku5r63yr3oY/s1600/engineer.jpg)

Developing a feature is not just typing code that compiles and makes the tests pass. Maybe the constant breathing of the project manager on your neck made you forget about this, but you are an **engineer**. Following [Wikipedia](https://en.wikipedia.org/wiki/Engineer):

> An **engineer** is a [professional](https://en.wikipedia.org/wiki/Profession) practitioner of [engineering](https://en.wikipedia.org/wiki/Engineering), concerned with applying [scientific knowledge](https://en.wikipedia.org/wiki/Scientific_knowledge), [mathematics](https://en.wikipedia.org/wiki/Mathematics), and [ingenuity](https://en.wikipedia.org/wiki/Ingenuity) to develop solutions for technical, societal and commercial problems. Engineers design materials, structures, and systems while considering the limitations imposed by practicality, regulation, safety, and cost. The word *engineer* is derived from the [Latin](https://en.wikipedia.org/wiki/Latin) words *ingeniare* (“to contrive, devise”) and *ingenium* (“cleverness”).

So, instead of saying:

> I don’t have time for this — it’s not my problem. I’ve delivered my business feature and this is what I’m paid for. What you’re referring to is not of my interest.

you should consider the technical aspects **before** writing a single line of code. Then you should say:

> My schedule is tight, but I’ll fix the issues you suggested. I understand that delivering business value means writing a feature **and** making technical progress as a company. This is what I’m paid for, and what you’re referring to is part of my duties.

Unfortunately, there is one problem with this approach…

## Are you an engineer that has a say? You’re gonna get fired!

Yes, if you start caring in a corporate enterprise you will eventually get fired. Business prefers people who nod their heads and agree to everything. After some time, quality becomes a burden for management — a cost that doesn’t bring “business value.”

So you will start fighting for quality because this is the very meaning of your programming life: deliver quality software that satisfies the business requirements, while bearing in mind technical consequences. You will defend your developers against the growing pressure from the business to deliver features at a larger pace. The corporate axe will come closer to your neck with every single fight to defend the very meaning of being an engineer.

In the meantime, your fellow developers who don’t agree with your permanent interference in the key tapping — due to buzzwords like “resilience,” “fail-fast,” “latency,” or “tests” — will continue to dislike you. They will constantly show their lack of support for what you’re doing. Their mediocrity and lack of willingness to stand up for what they believe in will allow them to remain in the company for years to come.

Then one day you will have to pack your stuff in a box and be escorted out of the office because you got fired. The reason will be simple: “not delivering business value.”

[![Guillotine](https://4.bp.blogspot.com/-baNDD9nKPtQ/Vh7QTfvgBTI/AAAAAAABIN4/p1xdBPOtkrU/s320/guillotine.gif)](https://4.bp.blogspot.com/-baNDD9nKPtQ/Vh7QTfvgBTI/AAAAAAABIN4/p1xdBPOtkrU/s1600/guillotine.gif)

But… don’t worry! That’s actually good. Someone is doing you a favor. In the long run, you will definitely profit from being fired. You will gain respect because you stood for your values. You will be able to look in the mirror and say that you did everything in your power to do things properly and with high quality.

## Epilogue

Hopefully my apocalyptic vision is too harsh, but that’s what I see when talking to people in the industry. There is a light at the end of the tunnel though (and it’s not a freight train).

There are companies that value good engineers and value quality. If you get fired (or you’re getting close to that), just send your CV there. You might be shocked that the very sense of caring and eagerness to learn drastically boosts your chances of getting hired.

[![The end](https://1.bp.blogspot.com/-oFjV8za2yWM/Vh7M1xlgLRI/AAAAAAABINs/_yxMM4Gp_Vw/s320/the_end.gif)](https://1.bp.blogspot.com/-oFjV8za2yWM/Vh7M1xlgLRI/AAAAAAABINs/_yxMM4Gp_Vw/s1600/the_end.gif)

## Additional reading

- [Living in the age of software fuckery](https://www.reddit.com/r/coding/comments/58ac50/living_in_the_age_of_software_fuckery/) - the original [medium article](https://medium.com/@bryanedds/living-in-the-age-of-software-fuckery-8859f81ca877) is gone
- [Don’t call yourself a programmer](https://www.kalzumeus.com/2011/10/28/dont-call-yourself-a-programmer/)
