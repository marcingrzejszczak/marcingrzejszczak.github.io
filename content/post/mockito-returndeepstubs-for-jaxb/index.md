---
title: "Mockito Returndeepstubs For Jaxb"
summary: "Sorry for not having written for some time but I was busy with writing the JBoss Drools Refcard for DZone and I am in the middle of writing a book about Mockito so I don't have too much time left for blogging..."
date: 2013-06-08

# Featured image
# Place an image named `featured.jpg/png` in this page's folder and customize its options here.
image:
  caption: 'Image credit: [**Unsplash**](https://unsplash.com)'

authors:
  - me

tags:
  - Blog

content_meta:
  trending: false
---
Sorry for not having written for some time but I was busy with writing the JBoss Drools Refcard for DZone and I am in the middle of writing a book about Mockito so I don't have too much time left for blogging...
Anyway quite recently on my current project I had an interesting situation regarding unit testing with Mockito and JAXB structures. We have very deeply nested JAXB structures generated from schemas that are provided for us which means that we can't change it in anyway.
[]()
Let's take a look at the project structure:
[![](https://1.bp.blogspot.com/-V5rYljCB5V4/UbN2mTKQ_QI/AAAAAAAABY8/C4WL8Tg-9b8/s320/project_structure.jpg)](https://1.bp.blogspot.com/-V5rYljCB5V4/UbN2mTKQ_QI/AAAAAAAABY8/C4WL8Tg-9b8/s1600/project_structure.jpg)
The project structure is pretty simple - there is a
**Player.xsd**
schema file that thanks to using the
**jaxb2-maven-plugin**
produces the generated JAXB Java classes corresponding to the schema in the
**target/jaxb/**
folder in the appropriate package that is defined in the
**pom.xml**
. Speaking of which let's take a look at the
**pom.xml**
file.
The pom.xml :
```xml
<project xmlns="https://maven.apache.org/POM/4.0.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="https://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.blogspot.toomuchcoding</groupId>
  <artifactId>mockito-deep_stubs</artifactId>
  <version>0.0.1-SNAPSHOT</version>
  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.6</maven.compiler.source>
    <maven.compiler.target>1.6</maven.compiler.target>
  </properties>
  <repositories>
    <repository>
      <id>spring-release</id>
      <url>https://maven.springframework.org/release</url>
    </repository>
    <repository>
      <id>maven-us-nuxeo</id>
      <url>https://maven-us.nuxeo.org/nexus/content/groups/public</url>
    </repository>
  </repositories>
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.10</version>
    </dependency>
    <dependency>
      <groupId>org.mockito</groupId>
      <artifactId>mockito-all</artifactId>
      <version>1.9.5</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
  <build>
    <pluginManagement>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>2.5.1</version>
        </plugin>
      </plugins>
    </pluginManagement>
    <plugins>
      <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>jaxb2-maven-plugin</artifactId>
        <version>1.5</version>
        <executions>
          <execution>
            <id>xjc</id>
            <goals>
              <goal>xjc</goal>
            </goals>
          </execution>
        </executions>
        <configuration>
          <packageName>com.blogspot.toomuchcoding.model</packageName>
          <schemaDirectory>${project.basedir}/src/main/resources/xsd</schemaDirectory>
        </configuration>
      </plugin>
    </plugins>
  </build>
</project>
```
Apart from the previously defined project dependencies, as mentioned previously in the
**jaxb2-maven-plugin**
in the configuration node you can define the
**packageName**
value that defines to which package should the JAXB classes be generated basing on the
**schemaDirectory**
value where the plugin can find the proper schema files.
Speaking of which let's check the
**Player.xsd**
schema file (simillar to the one that was present in the
[Spring JMS automatic message conversion article of mine](https://toomuchcoding.com/2012/11/spring-jms-message-automatic-conversion.html)
):
```xml
<?xml version="1.0" encoding="UTF-8"?>
<xsd:schema xmlns:xsd="https://www.w3.org/2001/XMLSchema">
  <xsd:element name="PlayerDetails">
    <xsd:complexType>
      <xsd:sequence>
        <xsd:element name="Name" type="xsd:string"/>
        <xsd:element name="Surname" type="xsd:string"/>
        <xsd:element name="Position" type="PositionType"/>
        <xsd:element name="Age" type="xsd:int"/>
        <xsd:element name="ClubDetails" type="ClubDetails"/>
      </xsd:sequence>
    </xsd:complexType>
  </xsd:element>
  <xsd:complexType name="ClubDetails">
    <xsd:sequence>
      <xsd:element name="TeamName" type="xsd:string"/>
      <xsd:element name="Country" type="CountryDetails"/>
    </xsd:sequence>
  </xsd:complexType>
  <xsd:complexType name="CountryDetails">
    <xsd:sequence>
      <xsd:element name="CountryName" type="xsd:string"/>
      <xsd:element name="CountryCode" type="CountryCodeDetails"/>
    </xsd:sequence>
  </xsd:complexType>
  <xsd:complexType name="CountryCodeDetails">
    <xsd:sequence>
      <xsd:element name="CountryName" type="xsd:string"/>
      <xsd:element name="CountryCode" type="CountryCodeType"/>
    </xsd:sequence>
  </xsd:complexType>
  <xsd:simpleType name="CountryCodeType">
    <xsd:restriction base="xsd:string">
      <xsd:enumeration value="PL"/>
      <xsd:enumeration value="GER"/>
      <xsd:enumeration value="FRA"/>
      <xsd:enumeration value="ENG"/>
      <xsd:enumeration value="ESP"/>
    </xsd:restriction>
  </xsd:simpleType>
  <xsd:simpleType name="PositionType">
    <xsd:restriction base="xsd:string">
      <xsd:enumeration value="GK"/>
      <xsd:enumeration value="DEF"/>
      <xsd:enumeration value="MID"/>
      <xsd:enumeration value="ATT"/>
    </xsd:restriction>
  </xsd:simpleType>
</xsd:schema>
```
As you can see I'm defining some complex types that even though might have no business sense but you can find such examples in the real life :)
Let's find out how the method that we would like to test looks like. Here we have the
**PlayerServiceImpl**
that implements the
**PlayerService**
interface:
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
We are getting the nested elements from the JAXB generated classes. Although it violates the
[Law of Demeter](https://en.wikipedia.org/wiki/Law_of_Demeter)
it is quite common to call methods of
**structures**
because JAXB generated classes are in fact structures so in fact I fully agree with
[Martin Fowler that it should be called the Suggestion of Demeter](https://martinfowler.com/articles/mocksArentStubs.html)
. Anyway let's see how you could test the method:
```groovy
@Test    public void shouldReturnTrueIfCountryCodeIsTheSame() throws Exception {
  //given        PlayerDetails playerDetails = new PlayerDetails();        ClubDetails clubDetails = new ClubDetails();        CountryDetails countryDetails = new CountryDetails();        CountryCodeDetails countryCodeDetails = new CountryCodeDetails();        playerDetails.setClubDetails(clubDetails);        clubDetails.setCountry(countryDetails);        countryDetails.setCountryCode(countryCodeDetails);        countryCodeDetails.setCountryCode(CountryCodeType.ENG);        //when        boolean playerOfGivenCountry = objectUnderTest.isPlayerOfGivenCountry(playerDetails,
  COUNTRY_CODE_ENG);        //then        assertThat(playerOfGivenCountry,
  is(true));
}
```
The function checks if, once you have the same Country Code, you get a true boolean from the method. The only problem is the amount of sets and instantiations that take place when you want to create the input message. In our projects we have twice as many nested elements so you can only imagine the number of code that we would have to produce to create the input object...
So what can be done to improve this code? Mockito comes to the rescue to together with the
**RETURN_DEEP_STUBS**
default answer to the
**Mockito.mock(...)**
method:
```groovy
@Test    public void shouldReturnTrueIfCountryCodeIsTheSameUsingMockitoReturnDeepStubs() throws Exception {
  //given        PlayerDetails playerDetailsMock = mock(PlayerDetails.class,
  RETURNS_DEEP_STUBS);        CountryCodeType countryCodeType = CountryCodeType.ENG;        when (
  playerDetailsMock.getClubDetails().getCountry().getCountryCode().getCountryCode()).thenReturn(countryCodeType);        //when        boolean playerOfGivenCountry = objectUnderTest.isPlayerOfGivenCountry(playerDetailsMock,
  COUNTRY_CODE_ENG);        //then        assertThat(playerOfGivenCountry,
  is(true));
}
```
So what happened here is that you use the
**Mockito.mock(...)**
method and provide the
**RETURNS_DEEP_STUBS**
answer that will create mocks automatically for you. Mind you that Enums can't be mocked that's why you can't write in the
**Mockito.when(...)**
function
**playerDetailsMock.getClubDetails().getCountry().getCountryCode().getCountryCode().getValue()**
.
Summing it up you can compare the readability of both tests and see how clearer it is to work with JAXB structures by using Mockito
**RETURNS_DEEP_STUBS**
default answer.
Naturally sources for this example are available at
[BitBucket](https://bitbucket.org/gregorin1987/too-much-coding/src/fc1bc010cf16e1f6477391f54e83f8ad446f7608/Unit%20Testing/Mockito%20-%20Deep%20Stubs?at=default)
and
[GitHub](https://github.com/marcingrzejszczak/too-much-coding/tree/master/Unit%20Testing/Mockito%20-%20Deep%20Stubs)
.
