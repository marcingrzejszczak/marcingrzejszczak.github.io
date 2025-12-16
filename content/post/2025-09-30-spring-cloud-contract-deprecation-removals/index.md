---
title: "Spring Cloud Contract 5 drops legacy Gradle contracts folder"
summary: "Spring Cloud Contract 5 will no longer support the fallback src/test/resource/contracts folder for storing contracts."
date: 2025-09-30

authors:
  - me

tags:
  - Spring Cloud Contract
  - Gradle

content_meta:
  trending: false
---
TLDR; Spring Cloud Contract 5 will no longer support the fallback src/test/resource/contracts folder. Check this [PR](https://github.com/spring-cloud/spring-cloud-contract/pull/2307) for more information.

<!-- more -->

Welcome to another article about Spring Cloud Contract 5. Why so many articles recently? Because I finally had some time to work on that plus a major release is coming so there's a window of opportunity to change things right now!

#### The Problem

For the last couple of majors we were migrating the Gradle plugin to have its own source set for contract tests. With that we've provided a default folder for Gradle contract tests - `src/contractTest/resources/contracts`. The old folder structure (`src/test/resources/contracts`) was deprecated and shown warnings.

#### The Solution

With that [PR](https://github.com/spring-cloud/spring-cloud-contract/pull/2307) the old folder structure checking is gone and your contracts will not be read from the `src/test/resources/contracts` location).

#### Conclusion

In this short article I've mentioned the most important breaking change related to code removal for the upcoming Spring Cloud Contract 5 release.
