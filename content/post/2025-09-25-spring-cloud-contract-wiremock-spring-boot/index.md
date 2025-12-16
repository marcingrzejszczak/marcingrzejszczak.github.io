---
title: "Spring Cloud Contract WireMock vs WireMock 3"
summary: "Spring Cloud Contract 5 drops support for @AutoConfigureWireMock in favour of the official WireMock 3 + Spring Boot support "
date: 2025-09-25

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
TLDR; Spring Cloud Contract 5 drops support for `@AutoConfigureWireMock` in favor of the official [WireMock + Spring Boot integration](https://github.com/wiremock/wiremock-spring-boot). Check this [PR](https://github.com/spring-cloud/spring-cloud-contract/pull/2289) for more information.

<!-- more -->

# The Problem

I have to admit that writing the original `@AutoConfigureWireMock` was a very pleasant experience. However, making it work well with Spring context caching - that's a completely different story 😆

If you're not familiar with this feature of Spring Cloud Contract you can [check the docs](https://docs.spring.io/spring-cloud-contract/docs/current/reference/html/project-features.html#features-wiremock). Let me give you a quick example:

```java
@SpringBootTest
@AutoConfigureWireMock(stubs="classpath:/stubs")
public class WiremockImportApplicationTests {

    @Autowired
    private Service service;

    @Test
    public void contextLoads() throws Exception {
        assertThat(this.service.go()).isEqualTo("Hello World!");
    }

}
```

This will start a WireMock server on a random port and import all stubs from the `/stubs` directory from the classpath.

So what is the biggest problem that we can face here? Spring context caching... Imagine that you have another test that imports the same stubs. If the Spring configuration is the same, then the context is being returned from the cache. If you want to do some verification on the WireMock server, then you will have a problem because that WireMock server was already started and could have processed some requests. 

# The Solution?

The solution to this problem was to introduce a Spring `TestExecutionListener` that would take into account whether the WireMock port was fixed or random. For fixed WireMock port the test would be marked as dirty (as if you marked it as `@DirtiesContext`). In the random port case we would reset the WireMock server. You can check the listener code [here](https://github.com/spring-cloud/spring-cloud-contract/blob/v4.3.0/spring-cloud-contract-wiremock/src/main/java/org/springframework/cloud/contract/wiremock/WireMockTestExecutionListener.java). 

# Ok... So What's the Problem Now?

Spring Cloud Contract is a Spring Cloud project even though it has nothing to do with the cloud per se. It was more logical to put that support in Spring Boot, but the Boot team had already enough on their plate. When I moved the original [Accurest](https://github.com/Codearte/accurest) project to Spring Cloud portfolio, I was part of the Cloud team, so it made logical sense to put the project in our portfolio. Years have passed and more and more questions have been asked about the WireMock support in non-Spring Cloud projects.

Another problem was the lack of time to invest in development of the project. There were other priorities like working on [Micrometer Observation](https://docs.micrometer.io/micrometer/reference/observation.html) and [Micrometer Tracing](https://docs.micrometer.io/tracing/reference/).

# The Ultimate Solution?

The ultimate solution emerged by itself - the official [WireMock + Spring Boot integration](https://github.com/wiremock/wiremock-spring-boot) maintained by our friends at WireMock. Since that's a Spring Boot-based project, it can be used without Spring Cloud.

With Spring Cloud Contract 5 you will need to migrate to the official WireMock + Spring Boot integration. Check [their docs](https://wiremock.org/docs/spring-boot/) for more information.

Below you can find an example of how to migrate from `@AutoConfigureWireMock` to the official WireMock + Spring Boot integration for the previous scenario.

```java
@EnableWireMock({
  @ConfigureWireMock(
      name = "fs-client",
      port = 0,
      filesUnderClasspath = "/stubs")
})
@SpringBootTest
public class WiremockImportApplicationTests {

    @Autowired
    private Service service;

    @Test
    public void contextLoads() throws Exception {
        assertThat(this.service.go()).isEqualTo("Hello World!");
    }

}
```

# Conclusion

In this article I've tried to explain the problem with `@AutoConfigureWireMock` and the solution that was introduced in Spring Cloud Contract 5. It seems that the migration from `@AutoConfigureWireMock` to the official WireMock + Spring Boot integration should be quite straightforward (famous last words).

As usual, in case of any questions or feedback, please don't hesitate to reach out either on GitHub or in the comments below (I'm testing out the GitHub discussions as a form of feedback mechanism).