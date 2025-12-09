---
title: "Spring Cloud Contract 5.0.1 released - upgrade!"
summary: "Spring Cloud Contract 5.0.1 is released with some critical bug fixes. Please upgrade!"
date: 2025-12-09

authors:
  - admin

tags:
  - Spring Cloud Contract

content_meta:
  trending: false
---
{{% callout note %}}
I'm very grateful to my employer [HeroDevs](https://www.herodevs.com/) that I'm allowed to work on OSS in any spare time that I have. At [HeroDevs](https://www.herodevs.com/) we ♥ Open Source! 
{{% /callout %}}

TLDR; Spring Cloud Contract 5.0.1 is released with some critical bug fixes. Please upgrade!

<!-- more -->

#### The Problem

With the release of Spring Cloud Contract 5.0.0 we've bumped the prerequiste of Maven version to be [3.9](https://github.com/spring-cloud/spring-cloud-contract/commit/addaa7bb0e98e68530b36aeb6247cbdfedbb7c94#diff-3ef7f7188ab0b58ede89e7f366df5fce29b9f6c5511bd43e017cdda7e993935e). Two big problems appeared after the release

- [Maven plugin fails when converting resources](https://github.com/spring-cloud/spring-cloud-contract/issues/2361)
- [Exception occurred while trying to fetch the stubs](https://github.com/spring-cloud/spring-cloud-contract/issues/2362)

The first one was an [easy fix](https://github.com/spring-cloud/spring-cloud-contract/pull/2367) and IMO not great coding assumption by Maven (why assume null lists instead of an empty one).

The second one was super tricky. I honestly didn't understand why the classes are not on the classpath. They had to be there!!11!!!

Well... not really because of this [Maven issue](https://issues.apache.org/jira/browse/MNG-7471). It turns out that Maven is filtering out the classes from the ClassLoader unless you have the components injected by Maven.  

#### The Solution

The first issue got fixed [here](https://github.com/spring-cloud/spring-cloud-contract/pull/2367).

The second one is more interested in got fixed [here](https://github.com/spring-cloud/spring-cloud-contract/pull/2376).

The release of [Spring Cloud Contract 5.0.1](https://github.com/spring-cloud/spring-cloud-contract/releases/tag/v5.0.1) is in Maven Central already so if you're using Maven Plugin please be sure that you upgrade to the latest version.