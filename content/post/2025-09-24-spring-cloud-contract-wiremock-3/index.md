---
title: "Spring Cloud Contract, WireMock 3"
summary: "Spring Cloud Contract 5 (finally) supports WireMock 3 "
date: 2025-09-24

authors:
  - me

tags:
  - Spring Cloud Contract
  - WireMock
  - Spring Boot
  - Spring Cloud

content_meta:
  trending: false
---
Spring Cloud Contract 5 (finally) will support [WireMock](https://wiremock.org/) 3. Check this [PR](https://github.com/spring-cloud/spring-cloud-contract/pull/2287) for more information.

Even though [I'm not in the Spring team anymore](/post/2025-09-23-new-blog-layout/), I still want to work with OSS! That's why whenever I had time recently, I've decided to work on the WireMock 3 upgrade for Spring Cloud Contract.

<!-- more -->

# I was there... 3 000 years ago...

![Elrond saying that he was there 3000 years ago](https://media1.tenor.com/m/0FJbp1RGsF0AAAAd/elrond-lotr.gif "I was there 3 000 years ago, when you asked for WireMock 3 support")

WireMock 3 has been out for quite some time now, and Spring Cloud Contract gets the upgrade only now? Why?

That's because of the philosophy behind Spring Framework. Ever wondered why this framework has been so successful for so long? Through countless minor and patch versions, we did everything in our power to ensure backward compatibility and not breaking changes. Whenever we did major changes, we wanted to ensure that you will be able to upgrade as painlessly as possible. 

Upgrading WireMock 2 to WireMock 3 is a breaking change - we couldn't have done it in a patch / minor release. With Spring Cloud Contract 5 finally a window of opportunity opened!

# Enjoy!

The [PR](https://github.com/spring-cloud/spring-cloud-contract/pull/2287) got merged, and the [next milestone release](https://github.com/spring-cloud/spring-cloud-contract/milestone/126) of Spring Cloud Contract will be out soon. Please check it out, and as usual, provide feedback!