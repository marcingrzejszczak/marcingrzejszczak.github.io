---
title: "Spring Cloud Contract 5 (finally) migrates to spring.cloud.contract property prefix"
summary: "Spring Cloud Contract 5 will no longer support the `stubrunner` prefix for properties - you need to migrate to `spring.cloud.contract.stubrunner`"
date: 2025-10-01

authors:
  - me

tags:
  - Spring Cloud Contract

content_meta:
  trending: false
---
TLDR; Spring Cloud Contract 5 will no longer support the `stubrunner` prefix for properties - you need to migrate to `spring.cloud.contract.stubrunner`. Check this [PR](https://github.com/spring-cloud/spring-cloud-contract/pull/2309) for more information.

<!-- more -->

#### The Problem

Since the very beginning of the project, when it was still called Accurest, the Spring properties to configure Accurest started with `stubrunner`. To ease the migration process we have left the original property prefix as it was so Spring Cloud Contract was the only project that did not have the proper `spring.cloud` prefix. Now it's about time to change that!

#### The Solution

With that [PR](https://github.com/spring-cloud/spring-cloud-contract/pull/2309) we're migrating all properties from the `stubrunner` prefix to `spring.cloud.contract.stubrunner`. The migration should be very simple - plain find and replace.
