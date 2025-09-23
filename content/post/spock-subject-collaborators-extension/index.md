---
title: "Spock Subject Collaborators Extension"
summary: "Hi! I'm really happy to say that I've just released a new version 1.0.1 of the Spock Subject Collaborators Extension ."
date: 2014-12-17

# Featured image
# Place an image named `featured.jpg/png` in this page's folder and customize its options here.
image:
  caption: 'Image credit: [**Unsplash**](https://unsplash.com)'

authors:
  - admin

tags:
  - Blog

content_meta:
  trending: false
---
Hi!
I'm really happy to say that I've just released a new version 1.0.1 of the
[Spock Subject Collaborators Extension](https://github.com/marcingrzejszczak/spock-subjects-collaborators-extension)
.
The changelog is as follows:
[]()
## 1.0.1
Bug fixes:
[#3](https://github.com/marcingrzejszczak/spock-subjects-collaborators-extension/issues/3)
Make plugin compatible with Spock 1.0.0-SNAPSHOT
## 1.0.0
New features:
[#1](https://github.com/marcingrzejszczak/spock-subjects-collaborators-extension/issues/1)
Inject superclass fields - now you can inject fields to your superclass
As you can see now you'll be able to use this extension together with Spock in version 1.0.0 (assuming that nothing will change until then).
## How to get it?
### For Maven:
Add JCenter repository:
```xml
<repositories>
  <repository>
    <snapshots>
      <enabled>false</enabled>
    </snapshots>
    <id>central</id>
    <name>bintray</name>
    <url>https://jcenter.bintray.com</url>
  </repository>
</repositories>
```
Add dependency:
```xml
<dependency>
  <groupId>com.blogspot.toomuchcoding</groupId>
  <artifactId>spock-subjects-collaborators-extension</artifactId>
  <version>1.0.1</version>
  <scope>test</scope>
</dependency>
```
### For Gradle:
Add JCenter repository:
```
repositories {    jcenter()}
```
Add dependency:
```groovy
dependencies {
  testCompile 'com.blogspot.toomuchcoding: spock-subjects-collaborators-extension: 1.0.1'
}
```
## How to use it?
Below you have an example of usage:
```java
package com.blogspot.toomuchcoding.spock.subjcollabsimport spock.lang.Specificationimport com.blogspot.toomuchcoding.spock.subjcollabs.Collaboratorimport com.blogspot.toomuchcoding.spock.subjcollabs.Subjectclass ConstructorInjectionSpec extends Specification {
    public static final String TEST_METHOD_1 = "Test method 1"    SomeOtherClass someOtherClassNotToBeInjected = Mock()    @Collaborator    SomeOtherClass someOtherClass = Mock()    @Subject    SomeClass systemUnderTest    def "should inject collaborator into subject"() {
        given:            someOtherClass.someMethod() >> TEST_METHOD_1        when:            String firstResult = systemUnderTest.someOtherClass.someMethod()        then:            firstResult == TEST_METHOD_1            systemUnderTest.someOtherClass == someOtherClass
}
    class SomeClass {
        SomeOtherClass someOtherClass        SomeClass(SomeOtherClass someOtherClass) {
            this.someOtherClass = someOtherClass
}

}
    class SomeOtherClass {
        String someMethod() {
            "Some other class"
}

}

}
```
## Disclaimer
Remember that if you're using this extension as a way to hack your way through an awful design of your application then you should do your best to fix your code in the first place! You've been warned ;)
