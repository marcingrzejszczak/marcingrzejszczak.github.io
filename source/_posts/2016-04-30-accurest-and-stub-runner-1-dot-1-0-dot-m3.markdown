---
layout: post
title: "Accurest and Stub Runner 1.1.0.M3"
date: 2016-04-30 13:53:44 +0200
comments: true
categories:
categories:
- accurest
- stubrunner
- testing
---

Currently at the Spring Team we're polishing our libraries for the upcoming final release of the Brixton train. It should happen soon :) Until then I'm spending a lot of my after work, free time on [Accurest](/blog/2016/04/25/accurest-docs-updated/) and [Stub Runner](https://toomuchcoding.com/blog/2016/04/06/accurest-stubrunner-released/).

Today's post will be about the new stuff that you will be able to profit from in the upcoming `1.1.0` release of Accurest. Also you can profit from most of these features in the `1.1.0.M3` release.

I'll just quickly go through the features but note that you can read about all of them in more depth in our [documentation ](http://codearte.github.io/accurest).

<!-- more -->

## Name change

AccuREST started as a library used to stub HTTP calls. In the upcoming `1.1.0` release you will be able to stub messaging functionality too. That's why the name changes to Accurest. That's a fantastic name isn't it? ;)

Also since branding is important, now instead of calling `io.codearte.accurest.dsl.GroovyDsl` you can call `io.codearte.accurest.dsl.Accurest` :)

## Messaging support

It took me quite some time to do this but it was worth it :) Several sleepless nights and now you can profit from defining contracts for messaging. In HTTP we had `client`/`stub` side and `server`/`test` side. For messaging we added methods to help discern the differences:

- `publisher` the side for which the tests will be generated
- `consumer` the side for which the messaging endpoints will be stubbed

### Contract

There are 3 use cases from the message `Producer`'s point of view.

- something happens in my application and I'm producing an output message
- someone sends a message to destination (queue/topic), I'm listening to that message and will produce an output message somewhere else
- someone sends a message to destination (queue/topic), I'm listening to that message and will consume it without any message sending

Here you can see examples of contracts for those three situations (you can read more about it in the  [docs](http://codearte.github.io/accurest/#messaging-top-level-elements) ):

#### Output triggered by a method

The output message can be triggered by calling a method (e.g. a Scheduler was started and a message was sent)

```groovy
def dsl = Accurest.make {
        // Human readable description
        description 'Some description'
        // Label by means of which the output message can be triggered
        label 'some_label'
        // input to the contract
        input {
                // the contract will be triggered by a method
                triggeredBy('bookReturnedTriggered()')
        }
        // output message of the contract
        outputMessage {
                // destination to which the output message will be sent
                sentTo('output')
                // the body of the output message
                body('''{ "bookName" : "foo" }''')
                // the headers of the output message
                headers {
                        header('BOOK-NAME', 'foo')
                }
        }
}
```

#### Output triggered by a message

The output message can be triggered by receiving a message.

```groovy
def dsl = GroovyDsl.make {
        description 'Some Description'
        label 'some_label'
        // input is a message
        input {
                // the message was received from this destination
                messageFrom('input')
                // has the following body
                messageBody([
                        bookName: 'foo'
                ])
                // and the following headers
                messageHeaders {
                        header('sample', 'header')
                }
        }
        outputMessage {
                sentTo('output')
                body([
                        bookName: 'foo'
                ])
                headers {
                        header('BOOK-NAME', 'foo')
                }
        }
}
```

#### No output, only input

There can be only input without any output

```groovy
def dsl = GroovyDsl.make {
        description 'Some Description'
        label 'some_label'
        // input is a message
        input {
                // the message was received from this destination
                messageFrom('input')
                // has the following body
                messageBody([
                        bookName: 'foo'
                ])
                // and the following headers
                messageHeaders {
                        header('sample', 'header')
                }
        }
}
```

### Producer side

Here you can see an example of a JUnit generated test for the producer for the input / output scenario:

```
// given:
 AccurestMessage inputMessage = accurestMessaging.create(
  "{\\"bookName\\":\\"foo\\"}"
, headers()
  .header("sample", "header"));

// when:
 accurestMessaging.send(inputMessage, "input");

// then:
 AccurestMessage response = accurestMessaging.receiveMessage("output");
 assertThat(response).isNotNull();
 assertThat(response.getHeader("BOOK-NAME")).isEqualTo("foo");
// and:
 DocumentContext parsedJson = JsonPath.parse(accurestObjectMapper.writeValueAsString(response.getPayload()));
 assertThatJson(parsedJson).field("bookName").isEqualTo("foo");
```

We're sending a message to a destination called `input`. next we're checking if there's a message at the `output` destination. If that's the case
we're checking if that message has proper headers and body.

### Consumer side

It's enough to provide the dependency to proper Stub Runner module (check the next section for more information) and tell it which stubs should be downloaded. Yup, that's it! [Stub Runner will download the stubs and prepare stubbed routes](http://codearte.github.io/accurest/#stub-runner-for-messaging).

Sometimes you'll need to trigger a message somehow in your tests. That's why we've provided the `StubTrigger` interface that you can inject! If you're already familiar with Stub Runner Spring then you could use the `StubFinder` bean to find the URL of your dependency. Now `StubFinder` also extends the `StubTrigger` interface thus you don't have to inject any additional beans in your tests.

There are multiple ways in which you can trigger a message:

#### Trigger by label

```
stubFinder.trigger('return_book_1')
```

#### Trigger by group and artifact ids

```
stubFinder.trigger('io.codearte.accurest.stubs:camelService', 'return_book_1')
```

#### Trigger by artifact id

```
stubFinder.trigger('camelService', 'return_book_1')
```

#### Trigger all messages

```
stubFinder.trigger()
```

### Integrations

We provide the following out of the box integrations:

- Spring Integration
- Spring Cloud Stream
- Apache Camel

Also we provide all the building blocks to provide a custom integration.

Just by providing the proper dependency

```
// for Apache Camel
testCompile "io.codearte.accurest:accurest-messaging-camel:${accurestVersion}"
// for Spring Integration
testCompile "io.codearte.accurest:accurest-messaging-integration:${accurestVersion}"
// for Spring Cloud Stream
testCompile "io.codearte.accurest:accurest-messaging-stream:${accurestVersion}"
```

Your generated tests should just work.

## Stub Runner Boot

I've added a new module of [Stub Runner](http://codearte.github.io/accurest/#stub-runner-boot) that operates on Spring Boot. Assuming that you're using Spring Cloud Stream you can create a project that has 2 dependencies:

```
compile "io.codearte.accurest:stub-runner-boot:${accurestVersion}"
compile "io.codearte.accurest:stub-runner-messaging-stream:${accurestVersion}"
```

Now if you pass the proper [Stub Runner Spring configuration](http://codearte.github.io/accurest/#common-properties-for-junit-and-spring) e.g.:

```
stubrunner.stubs.ids: io.codearte.accurest.stubs:streamService
```

You will have a running app that exposes HTTP endpoints to

- trigger messages
- check the URLs of the registered WireMock stubs

## Accurest Maven Plugin

Mariusz Smykuła has done a fantastic job by adding the [Accurest Maven Plugin](http://codearte.github.io/accurest-maven-plugin/). Now you can add Accurest to your project that runs with Maven. But that's not all since the Maven Plugin allows you to run the Accurest stubs using the `accurest:run` command!

Read the [docs](http://codearte.github.io/accurest-maven-plugin/) to know more!

## Stub Runner changes

### Messaging

With messaging coming as a feature I've added a bunch of messaging modules. You can read more about the [Stub Runner messaging modules here](http://codearte.github.io/accurest/#stub-runner-for-messaging)

### Fixed ports and versions of stubs

Another feature that was missing and is really valuable is that now you can explicitly say that you want a particular dependency to be started at a given port. This feature is available since version `1.0.7` but the stub id has been changed in `1.1.0.M4` so be warned ;)

The ids have changed because now you can provide the desired version of the stub that you want to download.

#### Via properties

Now you can provide the id of a stub like this:

```
groupId:artifactId:version:classifier:port
```

where version, classifier and port are optional.

- If you don’t provide the port then a random one will be picked
- If you don’t provide the classifier then the default one will be taken.
- If you don’t provide the version then the + will be passed and the latest one will be downloaded

Where port means the port of the WireMock server.

So if you provide your dependency like this:

```
stubrunner.stubs.ids: io.codearte.accurest.stubs:streamService:0.0.1-SNAPSHOT:stubs:9090,io.codearte.accurest.stubs:anotherService:+:9095
```

It will make Stub Runner:

- download a stub with groupId: `io.codearte.accurest.stubs`, artifactId: `streamService`, version: `0.0.1-SNAPSHOT`, classifier: `stubs` and register it at port 9090
- download a stub with groupId: `io.codearte.accurest.stubs`, artifactId: `anotherService`, latest version, default classifier (`stubs`) and register it at port 9095


#### Via fluent API

When using the AccurestRule you can add a stub to download and then pass the port for the last downloaded stub.

```
@ClassRule public static AccurestRule rule = new AccurestRule()
                .repoRoot(repoRoot())
                .downloadStub("io.codearte.accurest.stubs", "loanIssuance")
                .withPort(12345)
                .downloadStub("io.codearte.accurest.stubs:fraudDetectionServer:12346");
```

You can see that for this example the following test is valid:

```
then(rule.findStubUrl("loanIssuance")).isEqualTo(URI.create("http://localhost:12345").toURL());
then(rule.findStubUrl("fraudDetectionServer")).isEqualTo(URI.create("http://localhost:12346").toURL());
```

## Technical changes

Apart from features we've done some technical refactoring.

### Grape -> Aether

I've migrated the mechanism used to download dependencies from Groovy Grape to Aether. We had a lot of issues with Grape and Aether works very well for now. That's a backwards incompatible change so if you had some custom Grape configuration then you'll have to port it to Aether.

### Dependencies fixed

We had some problems with explicit and transitive dependencies that got fixed. The Accurest jars should be smaller.

### Summary

- A lot work was done around Accurest and CDC
- Quite soon we'll release the 1.1.0 version
- You can use stubs of your dependencies that communicate over messaging
- You can use fixed ports and versions for your dependencies
- If you like the project star it on [Github](https://github.com/Codearte/accurest) :) That will give us additional boost of energy to spend on coding instead of sleeping ;)

### Links

- [Accurest Github Repository](https://github.com/Codearte/accurest)
- [Accurest Documentation](http://codearte.github.io/accurest)
- [Stub Runner Documentation](http://codearte.github.io/accurest/#stub-runner)
- [Stub Runner Messaging Documentation](http://codearte.github.io/accurest/#stub-runner-for-messaging)
- [Accurest Gitter](https://gitter.im/Codearte/accurest)
- [Accurest Maven Plugin](https://github.com/Codearte/accurest-maven-plugin)
