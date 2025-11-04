---
title: "Spock Return Nested Spies Mocks"
summary: "Hi! Some time ago I have written an article about Mockito and using RETURNSDEEPSTUBS when working with JAXB ."
date: 2013-08-06

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
Hi! Some time ago I have written an article about Mockito and using
[RETURNS_DEEP_STUBS when working with JAXB](https://toomuchcoding.com/2013/06/mockito-returndeepstubs-for-jaxb.html)
. Quite recently we have faced a similliar issue with deeply nesetd JAXB and the awesome testing framework written in Groovy called
[Spock](https://code.google.com/p/spock/)
. Natively Spock does not support creating deep stubs or spies so we needed to create a workaround for it and this article will show you how to do it.
[]()
## Project structure
We will be working on the same data structure as in the
[RETURNS_DEEP_STUBS when working with JAXB](https://toomuchcoding.com/2013/06/mockito-returndeepstubs-for-jaxb.html)
article so the project structure will be quite simillar:
[![](https://4.bp.blogspot.com/-3AoyOo8WYuY/UgC3QJ5TuNI/AAAAAAAABkw/YJzt5EXB10s/s320/Spock+Deep+Stubs+project+structure.jpg)](https://4.bp.blogspot.com/-3AoyOo8WYuY/UgC3QJ5TuNI/AAAAAAAABkw/YJzt5EXB10s/s1600/Spock+Deep+Stubs+project+structure.jpg)
As you can see the main difference is such that the tests are present in the
/test/groovy/
folder instead of
/test/java/
folder.
## Extended Spock Specification
In order to use Spock as a testing framework you have to create Groovy test scripts that extend the Spock Specification class. The details of how to use Spock are available
[here](https://code.google.com/p/spock/wiki/SpockBasics)
. In this project I have created an abstract class that extends Specification and adds two additional methods for creating nested Test Doubles (I don't remember if I haven't seen a prototype of this approach somewhere on the internet).
**ExtendedSpockSpecification.groovy**
```java
package com.blogspot.toomuchcoding.spock;
import spock.lang.Specification/** * Created with IntelliJ IDEA. * User: MGrzejszczak * Date: 14.06.13 * Time: 15:26 */abstract class ExtendedSpockSpecification extends Specification {
    /**     * The method creates nested structure of spies for all the elements present in the property parameter. Those spies are set on the input object.     *     * @param object - object on which you want to create nested spies     * @param property - field accessors delimited by a dot - JavaBean convention     * @return Spy of the last object from the property path     */    protected def createNestedSpies(object, String property) {
        def lastObject = object        property.tokenize('.').inject object, {
 obj, prop ->            if (obj[prop] == null) {
                def foundProp = obj.metaClass.properties.find {
 it.name == prop
}
                obj[prop] = Spy(foundProp.type)
}
            lastObject = obj[prop]
}
        lastObject
}
    /**     * The method creates nested structure of mocks for all the elements present in the property parameter. Those mocks are set on the input object.     *     * @param object - object on which you want to create nested mocks     * @param property - field accessors delimited by a dot - JavaBean convention     * @return Mock of the last object from the property path     */    protected def createNestedMocks(object, String property) {
        def lastObject = object        property.tokenize('.').inject object, {
 obj, prop ->            def foundProp = obj.metaClass.properties.find {
 it.name == prop
}
            def mockedProp = Mock(foundProp.type)            lastObject."${
prop
}
" >> mockedProp            lastObject = mockedProp
}
        lastObject
}

}
```
These two methods work in a very simillar manner.
- Assuming that the method's argument
property
looks as follows:
"a.b.c.d"
then the methods tokenize the string by
"."
and iterate over the array -
["a","b","c","d"]
.
- We then iterate over the properties of the [Meta Class](https://groovy.codehaus.org/api/groovy/lang/MetaClass.html) to find the one whose name is equal to
prop
(for example
"a"
).
- If that is the case we then use Spock's Mock/Spy method to create a Test Double of a given class (type).
- Finally we have to bind the mocked nested element to its parent.
- Then we return from the closure the mocked/spied instance and we repeat the process for it
## Class to be tested
Let's take a look at the class to be tested:
**PlayerServiceImpl.java**
```java
package com.blogspot.toomuchcoding.service;
import com.blogspot.toomuchcoding.model.PlayerDetails;
/** * User: mgrzejszczak * Date: 08.06.13 * Time: 19:02 */public class PlayerServiceImpl implements PlayerService {
    @Override    public boolean isPlayerOfGivenCountry(PlayerDetails playerDetails, String country) {
        String countryValue = playerDetails.getClubDetails().getCountry().getCountryCode().getCountryCode().value();
return countryValue.equalsIgnoreCase(country);
}

}
```
## The test class
And now the test class:
**PlayerServiceImplWrittenUsingSpockTest.groovy**
```java
package com.blogspot.toomuchcoding.serviceimport com.blogspot.toomuchcoding.model.*import com.blogspot.toomuchcoding.spock.ExtendedSpockSpecification/** * User: mgrzejszczak * Date: 14.06.13 * Time: 16:06 */class PlayerServiceImplWrittenUsingSpockTest extends ExtendedSpockSpecification {
    public static final String COUNTRY_CODE_ENG = "ENG";
PlayerServiceImpl objectUnderTest    def setup() {
        objectUnderTest = new PlayerServiceImpl()
}
    def "should return true if country code is the same when creating nested structures using groovy"() {
        given:            PlayerDetails playerDetails = new PlayerDetails(                    clubDetails: new ClubDetails(                            country: new CountryDetails(                                    countryCode: new CountryCodeDetails(                                            countryCode: CountryCodeType.ENG                                    )                            )                    )            )        when:            boolean playerIsOfGivenCountry = objectUnderTest.isPlayerOfGivenCountry(playerDetails, COUNTRY_CODE_ENG);
then:            playerIsOfGivenCountry
}
    def "should return true if country code is the same when creating nested structures using spock mocks - requires CGLIB for non interface types"() {
        given:            PlayerDetails playerDetails = Mock()            ClubDetails clubDetails = Mock()            CountryDetails countryDetails = Mock()            CountryCodeDetails countryCodeDetails = Mock()            countryCodeDetails.countryCode >> CountryCodeType.ENG            countryDetails.countryCode >> countryCodeDetails            clubDetails.country >> countryDetails            playerDetails.clubDetails >> clubDetails        when:            boolean playerIsOfGivenCountry = objectUnderTest.isPlayerOfGivenCountry(playerDetails, COUNTRY_CODE_ENG);
then:            playerIsOfGivenCountry
}
    def "should return true if country code is the same using ExtendedSpockSpecification's createNestedMocks"() {
        given:            PlayerDetails playerDetails = Mock()            CountryCodeDetails countryCodeDetails = createNestedMocks(playerDetails, "clubDetails.country.countryCode")            countryCodeDetails.countryCode >> CountryCodeType.ENG        when:            boolean playerIsOfGivenCountry = objectUnderTest.isPlayerOfGivenCountry(playerDetails, COUNTRY_CODE_ENG);
then:            playerIsOfGivenCountry
}
    def "should return false if country code is not the same using ExtendedSpockSpecification createNestedMocks"() {
        given:            PlayerDetails playerDetails = Mock()            CountryCodeDetails countryCodeDetails = createNestedMocks(playerDetails, "clubDetails.country.countryCode")            countryCodeDetails.countryCode >> CountryCodeType.PL        when:            boolean playerIsOfGivenCountry = objectUnderTest.isPlayerOfGivenCountry(playerDetails, COUNTRY_CODE_ENG);
then:            !playerIsOfGivenCountry
}
    def "should return true if country code is the same using ExtendedSpockSpecification's createNestedSpies"() {
        given:            PlayerDetails playerDetails = Spy()            CountryCodeDetails countryCodeDetails = createNestedSpies(playerDetails, "clubDetails.country.countryCode")            countryCodeDetails.countryCode = CountryCodeType.ENG        when:            boolean playerIsOfGivenCountry = objectUnderTest.isPlayerOfGivenCountry(playerDetails, COUNTRY_CODE_ENG);
then:            playerIsOfGivenCountry
}
    def "should return false if country code is not the same using ExtendedSpockSpecification's createNestedSpies"() {
        given:            PlayerDetails playerDetails = Spy()            CountryCodeDetails countryCodeDetails = createNestedSpies(playerDetails, "clubDetails.country.countryCode")            countryCodeDetails.countryCode = CountryCodeType.PL        when:            boolean playerIsOfGivenCountry = objectUnderTest.isPlayerOfGivenCountry(playerDetails, COUNTRY_CODE_ENG);
then:            !playerIsOfGivenCountry
}

}
```
**Let's move through the test methods one by one**
. First I present the code and then have a quick description of the presented snippet.
```groovy
def "should return true if country code is the same when creating nested structures using groovy"() {
  given: PlayerDetails playerDetails = new PlayerDetails(                    clubDetails: new ClubDetails(                            country: new CountryDetails(                                    countryCode: new CountryCodeDetails(                                            countryCode: CountryCodeType.ENG                                    )                            )                    )            )        when: boolean playerIsOfGivenCountry = objectUnderTest.isPlayerOfGivenCountry(playerDetails,
  COUNTRY_CODE_ENG);        then: playerIsOfGivenCountry
}
```
Here you could find the approach of creating nested structures by using the Groovy feature of passing properties to be set in the constructor.
```groovy
def "should return true if country code is the same when creating nested structures using spock mocks - requires CGLIB for non interface types"() {
  given: PlayerDetails playerDetails = Mock()            ClubDetails clubDetails = Mock()            CountryDetails countryDetails = Mock()            CountryCodeDetails countryCodeDetails = Mock()            countryCodeDetails.countryCode >> CountryCodeType.ENG            countryDetails.countryCode >> countryCodeDetails            clubDetails.country >> countryDetails            playerDetails.clubDetails >> clubDetails        when: boolean playerIsOfGivenCountry = objectUnderTest.isPlayerOfGivenCountry(playerDetails,
  COUNTRY_CODE_ENG);        then: playerIsOfGivenCountry
}
```
Here you can find a test that creates mocks using Spock - mind you that you need CGLIB as a dependency when you are mocking non interface types.
```groovy
def "should return true if country code is the same using ExtendedSpockSpecification's createNestedMocks"() {
  given: PlayerDetails playerDetails = Mock()            CountryCodeDetails countryCodeDetails = createNestedMocks(playerDetails,
  "clubDetails.country.countryCode")            countryCodeDetails.countryCode >> CountryCodeType.ENG        when: boolean playerIsOfGivenCountry = objectUnderTest.isPlayerOfGivenCountry(playerDetails,
  COUNTRY_CODE_ENG);        then: playerIsOfGivenCountry
}
```
Here you have an example of creating nested mocks using the createNestedMocks method.
```groovy
def "should return false if country code is not the same using ExtendedSpockSpecification createNestedMocks"() {
  given: PlayerDetails playerDetails = Mock()            CountryCodeDetails countryCodeDetails = createNestedMocks(playerDetails,
  "clubDetails.country.countryCode")            countryCodeDetails.countryCode >> CountryCodeType.PL        when: boolean playerIsOfGivenCountry = objectUnderTest.isPlayerOfGivenCountry(playerDetails,
  COUNTRY_CODE_ENG);        then: !playerIsOfGivenCountry
}
```
An example showing that creating nested mocks using the createNestedMocks method really works - should return false for improper country code.
```groovy
def "should return true if country code is the same using ExtendedSpockSpecification's createNestedSpies"() {
  given: PlayerDetails playerDetails = Spy()            CountryCodeDetails countryCodeDetails = createNestedSpies(playerDetails,
  "clubDetails.country.countryCode")            countryCodeDetails.countryCode = CountryCodeType.ENG        when: boolean playerIsOfGivenCountry = objectUnderTest.isPlayerOfGivenCountry(playerDetails,
  COUNTRY_CODE_ENG);        then: playerIsOfGivenCountry
}
```
Here you have an example of creating nested spies using the createNestedSpies method.
```groovy
def "should return false if country code is not the same using ExtendedSpockSpecification's createNestedSpies"() {
  given: PlayerDetails playerDetails = Spy()            CountryCodeDetails countryCodeDetails = createNestedSpies(playerDetails,
  "clubDetails.country.countryCode")            countryCodeDetails.countryCode = CountryCodeType.PL        when: boolean playerIsOfGivenCountry = objectUnderTest.isPlayerOfGivenCountry(playerDetails,
  COUNTRY_CODE_ENG);        then: !playerIsOfGivenCountry
}
```
An example showing that creating nested spies using the createNestedSpies method really works - should return false for improper country code.
## Summary
In this post I have shown you how you can create nested mocks and spies using Spock. It can be useful especially when you are working with nested structures such as JAXB. Still you have to bear in mind that those structures to some extend violate the Law of Demeter. If you check my previous article about Mockito you would see that:
> We are getting the nested elements from the JAXB generated classes. Although it violates the
> [Law of Demeter](https://en.wikipedia.org/wiki/Law_of_Demeter)
> it is quite common to call methods of
> **structures**
> because JAXB generated classes are in fact structures so in fact I fully agree with
> [Martin Fowler that it should be called the Suggestion of Demeter](https://martinfowler.com/articles/mocksArentStubs.html)
> .
And in case of this example the idea is the same - we are talking about structures so we don't violate the Law of Demeter.
**Advantages**
- With a single method you can mock/spy nested elements
- Code cleaner than creating tons of objects that you then have to manually set
**Disadvantages**
- Your IDE won't help you with providing the property names since the properties are presented as Strings
- You have to set Test Doubles only in the Specification context (and sometimes you want to go outside this scope)
## Sources
As usual the sources are available at
[BitBucket](https://bitbucket.org/gregorin1987/too-much-coding/src/9f0f64d405a0c0b8219043df9b599f60569c1633/Unit%20Testing/Spock%20-%20Deep%20Stubs?at=default)
and
[GitHub](https://github.com/marcingrzejszczak/too-much-coding/tree/master/Unit%20Testing/Spock%20-%20Deep%20Stubs)
.
