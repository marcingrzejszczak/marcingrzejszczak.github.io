---
title: "Is AI taking my job? Should I become a construction worker?"
summary: "Let's talk about AI and the future of IT - are we as developers redundant?"
date: 2026-01-10

authors:
  - me

tags:
  - AI

content_meta:
  trending: false
---

With the wake of AI tools, especially the ones that are generating code, whether as IT developers we should start looking for jobs in construction remains a very interesting question to ask...
   
<!-- more -->

I've come across [this Gergely Orosz's post](https://blog.pragmaticengineer.com/the-grief-when-ai-writes-most-of-the-code/) from his Pragmatic Engineer blog. For the sake of this blog let me quote his article below:

> I’m coming to terms with the high probability that AI will write most of my code which I ship to prod, going forward. It already does it faster, and with similar results to if I’d typed it out. For languages/frameworks I’m less familiar with, it does a better job than me.
> 
> It feels like something valuable is being taken away, and suddenly. It took a lot of effort to get good at coding and to learn how to write code that works, to read and understand complex code, and to debug and fix when code doesn’t work as it should. I still remember how daunting my first “real” programming class was at university (learning C), how lost I felt on my first job with a complex codebase, and how it took years of practice, learning from other devs, books, and blogs, to get better at the craft. Once you’re pretty good, you have something that’s valuable and easy to validate by writing code that works!
>
> Some of my best memories of building software are about coding. Being “locked in” and balancing several ideas while typing them out, of being in the zone, then compiling the code, running it and seeing that “YES”, it worked as expected!
>
> It’s been a love-hate relationship, to be fair, based on the amount of focus needed to write complex code. Then there’s all the conflicts that time estimates caused: time passes differently when you’re locked in and working on a hard problem.
> 
> Now, all that looks like it will be history.
>
> I wonder if I’ll still get the same sense of satisfaction from the fact that writing complicated code is hard? Yes, AI is convenient, but there’s also a loss.
>
> Or perhaps with AI agents, being “in the zone” will shift to thinking about higher-level problems, while instructing more complex code to be written?

I'm following Gergely on various social media because I'm a big a fan of his thoughts about different topics and how he expresses them. With this post he has literally written down all of my thoughts and fears...

Quite recently I've spent a couple of months on renovating a house - I've literally done construction site work from demolitions to setting up walls, ceilings, doing electricity etc. I've talked about the AI situation with my friend who is a construction worker. I explained it to him in such a way - "_you've been working hard for the past 30 years to master your craft, you know how to measure angles, use construction patches, levels. You have developed expertise in the way you handle construction tools, how to press them to properly apply the material. Now imagine that all of that is gone, you simply sit down on a stool and point with your finger at a swarm of robots who are supposed to do your job. You just need to ensure that the job is done the way you would do it..."_. As you can imagine he wasn't too happy with such a perspective.

Is it really such a bad perspective though? On one hand I do feel some sadness - I do like to code, I do like to produce it and see the outcome (and often be shocked that what I did actually works). On the other hand I have never been so productive as an engineer like when I'm leveraging AI. When I'm looking at the AI coding tools generate code, I can't deny that I'm simply unable to think that fast not to mention produce that much code. So, are we as engineers completely replaceable? Should we start learning a new craft - for example become construction site workers? 

I tried to wrap my head around where to place myself in all of this chaos. For almost a decade I've been maintaining OSS software as part of the Spring Framework and Micrometer teams. We've been the ones telling the industry how things should be done, what the new trends are etc. It seems that changes in IT have increased its velocity by an order of magnitude. Honestly, sometimes I feel that I just can't keep up...

I had a chat with a friend from the IT industry where after having thought things through I came to the conclusion that as engineers our work is still very valid. If we treat the AI-generated code as a river flowing very fast, whose velocity you can't contain, what is in our responsibility is to control its flow. We can define certain invariants, technical and general business strict rules that can organize how the flow looks like. In other words we can finally apply our experience and good practices and focus on solving business problems much faster. 

It was funny when I had a different discussion about an idea of an AI workshop about doing specification first approach to AI code generation. I've spent that whole morning thinking things through, how certain approaches to invariants should be codified and how that would allow to constraint the code generation process. Happy as never before I've decided to share it with a friend and explain to him my revolutionary ideas - of course it turned out that not only they have already been thought of but there are some quite mature solutions out there not to mention existing workshops. I guess I'm late to the party 🤷‍♂️

Another revelation that I had is that something that used to be our asset - code, no longer is one. The price of code is irrelevant with the emerging of AI code generating tools. If you create a startup and then want to sell it, the way your code looks like isn't, in my opinion, that important anymore (as a general rule, because maintainability is still important in big projects). For small projects you can ask AI to remove the whole codebase and in max couple of hours you will get it regenerated from scratch. It seems that our asset now is how we can leverage the tools to write code that solves business  and also technical problems. Our knowledge and experience is far bigger of an asset than how we wrote the code. 

I was asking myself is it a good thing that I might start forgetting how to code things - like literally how to do X in a framework Y. Does it really matter though? Recently I've managed to create a POC of a mobile app using Flutter with AI. Within 30 minutes I was able to see a mobile app in an Android emulator. It's not that I've forgotten how to do anything in Flutter - I completely don't know it! I've never created a mobile app before. Does it matter? Not at all I guess... Can I create the app to try to follow the best practices of the given language / framework? Yes, I can. Why would the framework X or the language that I work with on the daily basis (Java) be any better than e.g. Flutter for that matter? It shouldn't...

I have a moral problem with AI - there was this website where you could check if a model was trained on a given book. It turned out that my _Mockito Cookbook_ book was used to train the model. Have I received any financial gratification for that? No. Neither have other authors. It seems that all of our work has been publicly stolen and nobody cares. Same for all the graphics that you could generate that resemble e.g. some cartoon style. The authors of those pictures didn't get a single dollar for millions of generated graphics. I think this is highly unfair...

AI is here to stay, and it changed everything in the IT industry. Especially with the AI code generating tools - I think there's no going back. We have two options, either ignore them and act as if nothing happened which in my opinion will result in us getting redundant or **embrace them and use them to your best advantage**.

Even though being a construction site worker was a great experience I think I'll postpone the decision to shift my career in that direction indefinitely. I'll just continue learning more about MCP servers, agentic workflows etc. to become an even more productive engineer...

What is your opinion on the topic? Please share a comment below and let's talk about this. I felt much better with my anxiety after a couple of conversations on LinkedIn with AI subject-matter experts and with my friends and colleagues. You're not alone with your fears!

Oh and btw, since this post is getting traction so it's time for some marketing...

My new Java Microservices with Spring  workshops are available for enrollment on [Maven](https://maven.com/toomuchcoding/java-microservices-with-spring). 

The course is battle-proven, I've been doing it for years through the O'Reilly platform, and I've taught hundreds of students. So if you want to learn how to work with microservices, Spring Cloud, Micrometer and more, this is the course for you!

With the promo code **TOOMUCHCODING** you can get **$200** discount on the course! **The offer is valid until January 11th**, so hurry up!
 