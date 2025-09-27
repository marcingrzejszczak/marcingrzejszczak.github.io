---
title: "Spring Cloud Contract 5 drops the spring-cloud-contract-shade module"
summary: "Spring Cloud Contract 5 drops the module that shaded quite a few libraries for the sake of compatibility with Boot, Kotlin, Groovy, Maven and Gradle"
date: 2025-09-27

authors:
  - admin

tags:
  - Spring Cloud Contract
  - WireMock
  - Spring Boot
  - Maven
  - Gradle

content_meta:
  trending: false
---
TLDR; Spring Cloud Contract 5 drops the module that shaded quite a few libraries for the sake of compatibility with Boot, Kotlin, Groovy, Maven and Gradle. Check this [PR](https://github.com/spring-cloud/spring-cloud-contract/pull/2288) for more information.

<!-- more -->

# The Problem

When doing [mentoring sessions](/consulting) I put a lot of effort into teaching how to store architectural decisions. That's why one of the tasks that my mentees have when they write their own applications is to store [ADRs](https://github.com/joelparkerhenderson/architecture-decision-record) (architecture decision records). However, "the shoemaker's son always goes barefoot"... In Spring Cloud Contract (SCC) I have not been storing such decisions 🤦‍♂️.

Why do I write about this? That's because the main reason behind the Spring Cloud Contract shaded module was to ensure compatibility between Spring Boot, Maven, Gradle, Kotlin, Groovy and Spring Cloud Contract. SCC is a Spring Boot project. Spring Boot brings in its own versions of libraries. Spring Cloud Contract has a Maven and Gradle plugin which also depends on Boot. However, Maven and Gradle bring their own versions of libraries. This lead to big compatibility problems...

# Past: The Solution?

Once the compatibility problems occurred I've decided to create a module that would bring the libraries that SCC core depends on in a shaded way. That way, whatever is brought by Maven or Gradle would not interfere with the libraries that SCC core depends on. So far so good! However, the problem was to set up Maven in such a way that it downloads the sources, shades them too and makes them available to the IDE.

It worked quite nicely till the main Java line became JDK17. The shaded libraries would depend on JDK8 classes, and the IDE would not be able to resolve them. It would work from the command line (the shaded sources are not being compiled), there would be problems with the IDE.

I wish I would have written down the ADR for this particular case because after quite a few years the analysis I had done then would have been useful now. Shame on me 😬.

# Present: The Solution?

I've been doing quite a few renovations recently. I even worked as a construction site worker for around 3 months at my own construction site. I learned a lot, especially an exciting and practical approach to dealing with problems - the worker, when asked about the used material that will make it impossible for me to put a curtain rod on the wall, he replied:

> Sir, there will be no curtain rod here...

Following the advice of this sage and implementing it in programming, I've decided to make SCC drop the shaded module entirely. No module? No problem!

Joking aside - I removed the module and added all the necessary dependencies to Maven and Gradle plugins manually. Currently, the library differences between Maven, Gradle, Boot and SCC are manageable. I've also spent some time on fixing most of the [Spring Cloud Contract Samples](https://github.com/spring-cloud-samples/spring-cloud-contract-samples/pull/427) to double-check that everything works fine on actual projects.

# Conclusion

In this article I've explained the reasoning behind setting up the shaded module, the problems it caused and the solution that I've implemented. I do hope that this will help you in the future. 

As usual, in case of any questions or feedback, please don't hesitate to reach out either on GitHub or in the comments below (I'm testing out the GitHub discussions as a form of feedback mechanism).