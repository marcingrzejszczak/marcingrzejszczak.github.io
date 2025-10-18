---
title: "Micrometer Docs Generator -  a Hidden Gem"
summary: "In this blog post we'll look into Micrometer's hidden gem - Micrometer Docs Generator"
date: 2025-10-18

authors:
  - admin

tags:
  - Micrometer
  - Micrometer Docs Generator

content_meta:
  trending: false
---
TLDR; Micrometer comes with a project called [Micrometer Docs Generator](https://github.com/micrometer-metrics/micrometer-docs-generator) that is capable of generating documentation from your sources. 

<!-- more -->

A lot has been said about [Micrometer](https://micrometer.io) - an observability library for the JVM. It's widely used by the Java community due to it being a core part of the Spring Framework and [Spring Boot](https://docs.spring.io/spring-boot/reference/actuator/observability.html). Other well-known frameworks such as [Quarkus](https://quarkus.io/guides/telemetry-micrometer) also use Micrometer! Not a lot has been said about how Micrometer can help you with your documentation.
 
#### The Problem

When using an observability framework such as Micrometer one creates observations like one below.

```java
String name = SomeEnum.ENUM_ENTRY.name(); // This enum has a handful of entries 
String id = generateRandomText(); // Id is a random String value - it can vary a lot
String operationName = "Name [" + name + "] id [" + id + "]" ; 
ObservationRegistry registry = ObservationRegistry.create();
Observation
        .createNotStarted("calculating.tax", registry)
        .contextualName(operationName)
        .lowCardinalityKeyValue("not.varying.value", name)
        .highCardinalityKeyValue("varying.value", id)
        .observe(this::doSomeWorkHere);
```

In this scenario we're making observability a first class citizen - it lives together with the business code. The `calculating.tax` observation is not designed to be customizable - the name and key-values are fixed. Is it a bad thing? Not really, since we as business developers own this code. If there's any need for customization we will simply change the code.

So is there any problem here? What if I asked you a couple of questions 

- How many observations do you have?
- What are the distributed tracing spans your application produces?
- What are the meters your application produces?

[Micrometer Observation](https://docs.micrometer.io/micrometer/reference/observation.html) works in such a way that out of observations it produces multiple signals. Depending on the setup those can be metrics, traces, logs or anything else that the user sets up via [Observation Handlers](https://docs.micrometer.io/micrometer/reference/observation/components.html#micrometer-observation-handler). 

Assuming that you set up metrics and tracing Observation Handlers - are you able to answer the aforementioned questions? Maybe it would be easier to answer if you documented that?

#### Who Loves Writing Docs?

According to this [StackOverflow article](https://stackoverflow.blog/2024/12/19/developers-hate-documentation-ai-generated-to) (which confirms my own observations from the last 15 years) software developers struggle with writing documentation. I remember around 15 years ago that we had to document our software in Confluence ("the place where the knowledge dies" :tm:) and copy that text over to the Enterprise Architect as a text label :laughing:.

Writing docs using [Markdown](https://en.wikipedia.org/wiki/Markdown) was a big step forward - developers could focus on writing actual text and rendering would happen as a separate process. So what was the problem with Markdown? You weren't able to include sources. That means that whenever your documentation referenced code and the code changed, you needed to manually update the docs. If you forgot to do that your documentation would lie about what it actually does. Example (if `SomeClass` changed then we would need to modify the snippet manually):

```markdown

Some text before including code.

\```java
class SomeClass {

    void hello() {
        // do Sth
    }
}
\```

```

Another tool that changed things around was [Asciidoctor](https://asciidoctor.org/). It allowed you to [include](https://docs.asciidoctor.org/asciidoc/latest/directives/include/) sources. That way you had single source of truth - your code. For example - you could write a test, that showed some usage example of your code, then include part of that test in the output documentation. When the code changed, the processed documentation would change but not the documentation sources. Example:

```asciidoc
:sourcedir: ../src/main/java

Some text before including code.

[source,java]
----
include::{sourcedir}/org/asciidoctor/Asciidoctor.java[]
----
```

Ok, is this the final solution to the problem? Not really, we as developers must write the docs ourselves. What if we could shift things around and tell our code to write the docs instead?

#### Let The Code Write The Docs!

A big eye-opener for me was the fabulous [Spring RestDocs](https://docs.spring.io/spring-restdocs/docs/current/reference/htmlsingle/) project. With RestDocs you write your tests and from the tests the documentation would be generated! Let's look at the following, simplified, example:

```java
@SpringBootTest
@AutoConfigureRestDocs
@AutoConfigureMockMvc
public class ProducerControllerTests {

	@Autowired
	private MockMvc mockMvc;
	
	@Test
	public void should_grant_a_beer_when_person_is_old_enough() throws Exception {
		PersonToCheck personToCheck = new PersonToCheck(34);

		this.mockMvc.perform(MockMvcRequestBuilders.post("/check")
						.contentType(MediaType.APPLICATION_JSON)
						.content(toJson(personToCheck)))
				.andExpect(jsonPath("$.status").value("OK"))
				.andDo(MockMvcRestDocumentation.document("shouldGrantABeerIfOldEnough"));
	}
    
}
```

This test will verify if our Spring REST API works as intended and at the same time produce snippets that document the API. Instead of us writing the documentation we write the tests that are the source of truth and after executing them we get documentation as the output. That's brilliant!

So we've managed to go from manually writing the docs with the code snippets (Markdown), to writing our docs manually and having parts of our code included in the docs (Asciidoctor) to producing the documentation from the tests. Should I finish the article? Have we done everything we could? What if we could create the documentation from production sources and not from test execution?  

#### 