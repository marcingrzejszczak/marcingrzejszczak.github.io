---
title: "Springs Primary Annotation In Action"
summary: "Spring is a framework that never stops to amaze me. It's because of the fact that it offers plenty of different solutions that allow us, developers, to complete our tasks without writing millions of lines of code."
date: 2013-12-12

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
Spring is a framework that never stops to amaze me. It's because of the fact that it offers plenty of different solutions that allow us, developers, to complete our tasks without writing millions of lines of code. Instead we are able to do the same in a much more readable, standardized manner. In this post I will try to describe one of its features that most likely is well known to all of you but in my opinion its importance is undervalued. The feature that I'll be talking about is the
[@Primary](https://docs.spring.io/spring/docs/3.2.5.RELEASE/javadoc-api/org/springframework/context/annotation/Primary.html)
annotation.
[]()
## The problem
On a couple of projects that I was working on we have come accross a common business problem - we had a point of entry to a more complex logic - some container, that would gather the results of several other processors into a single output (something like map-filter-reduce functions from the functional programming). To some extent it resembled the
[Composite](https://en.wikipedia.org/wiki/Composite_pattern)
pattern. Putting it all together our approach was as follows:
1. We had a container that had an autowired list of processors implementing a common interface
2. Our container implemented the same interface as the elements of the autowired list
3. We wanted the client class that would use the container to have this whole processing work transparent - he is interesed only in the result
4. The processors have some logic (predicate) basing on which a processor is applicable to the current set of input data
5. The results of the processing were then combined into a list and then reduced to a single output
There are numerous ways of dealing with this issue - I'll present one that uses Spring with the
@Primary
annotation.
## The solution
Let's start with defining how our use case will fit to the aforementioned preconditions. Our set of data is a Person class that looks as follows:
**Person.java**
```java
package com.blogspot.toomuchcoding.person.domain;
public final class Person {
 private final String name;
private final int age;
private final boolean stupid;
public Person(String name, int age, boolean stupid) {
  this.name = name;
this.age = age;
this.stupid = stupid;
}
 public String getName() {
  return name;
}
 public int getAge() {
  return age;
}
 public boolean isStupid() {
  return stupid;
}

}
```
Nothing out of the ordinary. Now let us define the contract:
**PersonProcessingService.java**
```java
package com.blogspot.toomuchcoding.person.service;
import com.blogspot.toomuchcoding.person.domain.Person;
public interface PersonProcessingService {
 boolean isApplicableFor(Person person);
String process(Person person);
}
```
As stated in the preconditions each implementaiton of the PersonProcessingService has to define two points of the contract :
1. whether it is applicable for the current Person
2. how it processess a Person.
Now let's take a look at some of the Processors that we have - I'll not post the code here cause it's pointless - you can check out the code later on
[Github](https://github.com/marcingrzejszczak/too-much-coding/tree/master/Spring/Primary)
or on
[Bitbucket](https://bitbucket.org/gregorin1987/too-much-coding/src/6c9b91a33f7d40032ff7c650d17bf60bd8c34625/Spring/Primary/?at=default)
. We have the following @Component annotated implementations of PersonProcessingService:
- AgePersonProcessingService
- IntelligencePersonProcessingService
- NamePersonProcessingService
The logic is fairly simple. Now our container of PersonProcessingServices would want to iterate for a given Person over the processors, check if the current processor is applicable (filter) and if that is the case add the String that is a result of processing of a Person to the list of responses (map - a function converting a Person to a String) and finaly join those responses by a comma (reduce). Let's check it out how it's done:
**PersonProcessingServiceContainer.java**
```java
package com.blogspot.toomuchcoding.person.service;
import java.util.ArrayList;
import java.util.List;
import org.apache.commons.lang.StringUtils;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Primary;
import org.springframework.stereotype.Component;
import com.blogspot.toomuchcoding.person.domain.Person;
@Component@Primaryclass PersonProcessingServiceContainer implements PersonProcessingService {
 private static final Logger LOGGER = LoggerFactory.getLogger(PersonProcessingServiceContainer.class);
@Autowired private List<PersonProcessingService> personProcessingServices = new ArrayList<PersonProcessingService>();
@Override public boolean isApplicableFor(Person person) {
  return person != null;
}
 @Override public String process(Person person) {
  List<String> output = new ArrayList<String>();
for(PersonProcessingService personProcessingService : personProcessingServices) {
   if(personProcessingService.isApplicableFor(person)) {
    output.add(personProcessingService.process(person));
}

}
  String result = StringUtils.join(output, ",");
LOGGER.info(result);
return result;
}
 public List<PersonProcessingService> getPersonProcessingServices() {
  return personProcessingServices;
}

}
```
As you can see we have a container that is annotated with @Primary which means that if an implementation of the PersonProcessingService will have to be injected then Spring will pick the PersonProcessingServiceContainer to be injected. The cool thing is that we have an autowired list of PersonProcessingServices which means that all other implementations of that interface will get autowired there (the container will not autowire itself to the list!).
Now let's check out the
[Spock tests](https://code.google.com/p/spock/)
that prove that I'm not telling any lies. If you aren't using Spock already in your project then you should move it straight away :)
**PersonProcessingServiceContainerIntegrationSpec.groovy**
```java
package com.blogspot.toomuchcoding.person.serviceimport com.blogspot.toomuchcoding.configuration.SpringConfigurationimport com.blogspot.toomuchcoding.person.domain.Personimport org.springframework.beans.factory.annotation.Autowiredimport org.springframework.test.context.ContextConfigurationimport spock.lang.Specificationimport spock.lang.Unrollimport static org.hamcrest.CoreMatchers.notNullValue@ContextConfiguration(classes = [SpringConfiguration])class PersonProcessingServiceContainerIntegrationSpec extends Specification {
    @Autowired    PersonProcessingService personProcessingService        def "should autowire container even though there are many implementations of service"() {
               expect:             personProcessingService instanceof PersonProcessingServiceContainer
}
        def "the autowired container should not have itself in the list of autowired services"() {
               expect:             personProcessingService instanceof PersonProcessingServiceContainer        and:            !(personProcessingService as PersonProcessingServiceContainer).personProcessingServices.findResult {
                it instanceof PersonProcessingServiceContainer
}

}
        def "should not be applicable for processing if a person doesn't exist"() {
        given:            Person person = null        expect:            !personProcessingService.isApplicableFor(person)
}
        def "should return an empty result for a person not applicable for anything"() {
        given:            Person person = new Person("", 17, false)        when:            def result = personProcessingService.process(person)        then:            result notNullValue()            result.isEmpty()
}
    @Unroll("For name [#name], age [#age] and being stupid [#stupid] the result should contain keywords #keywords")    def "should perform different processing depending on input"() {
        given:            Person person = new Person(name, age, stupid)        when:            def result = personProcessingService.process(person)                then:            keywords.every {
                result.contains(it)
}
        where:            name  | age | stupid || keywords            "jan" | 20  | true   || ['NAME', 'AGE', 'STUPID']            ""    | 20  | true   || ['AGE', 'STUPID']            ""    | 20  | false  || ['AGE']            null  | 17  | true   || ['STUPID']            "jan" | 17  | true   || ['NAME']
}

}
```
The tests are pretty straight forward:
1. We prove that the autowired field is in fact our container - the PersonProcessingServiceContainer.
2. Then we show that we can't find an object in the collection of autowired implementations of the PersonProcessingService, that is of PersonProcessingServiceContainer type
3. In the next two tests we prove that the logic behind our processors is working
4. Last but not least is the Spock's finest - the where clause that allows us create beautiful paramterized tests.
## Per module feature
Imagine the situation in which you have an implementation of the interface that is defined in your core module.
```
@Componentclass CoreModuleClass implements SomeInterface {...}
```
What if you decide in your other module that has the dependence to the core module that you don't want to use this CoreModuleClass and want to have some custom logic wherever the SomeInterface is autowired? Well - use @Primary!
```
@Component@Primaryclass CountryModuleClass implements SomeInterface {...}
```
In that way you are sure that wherever the SomeInterface has to be autowired it will be your CountryModuleClass that will be injected in the field.
## Conclusion
In this post you could see how to
- use the @Primary annotation to create a composite like container of interface implementations
- use the @Primary annotation to provide a per module implementation of the interface that will take precedence over other @Components in terms of autowiring
- write cool Spock tests :)
## The code
You can find the code presented here on
[Too Much Coding's Github repository](https://github.com/marcingrzejszczak/too-much-coding/tree/master/Spring/Primary)
or on
[Too Much Coding's Bitbucket repository](https://bitbucket.org/gregorin1987/too-much-coding/src/6c9b91a33f7d40032ff7c650d17bf60bd8c34625/Spring/Primary/?at=default)
.
