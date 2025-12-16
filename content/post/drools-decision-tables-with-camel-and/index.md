---
title: "Drools Decision Tables With Camel And"
summary: "Hi! As I've shown it in my previous post JBoss Drools are a very useful rules engine . The only problem is that creating the rules in the Rule language might be pretty complicated for a non-technical person."
date: 2013-02-03

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
Hi!
As I've shown it in my previous post
[JBoss Drools are a very useful rules engine](https://toomuchcoding.com/2013/01/drools-integration-with-spring-vs.html)
. The only problem is that creating the rules in the Rule language might be pretty complicated for a non-technical person. That's why one can provide an easy way for creating business rules - decision tables created in a spreadsheet!
In the following example I will show you a really complicated business rule example converted to a decision table in a spreadsheet. As a backend we will have Drools, Camel and Spring.
[]()
To begin with let us take a look at our imaginary business problem. Let us assume that we are running a business that focuses on selling products (either Medical or Electronic). We are shipping our products to several countries (PL, USA, GER, SWE, UK, ESP) and depending on the country there are different law regulations concerning the buyer's age. In some countries you can buy products when you are younger than in others. What is more depending on the country from which the buyer and the product comes from and on the quantity of products, the buyer might get a discount. As you can see there is a substantial number of conditions needed to be fullfield in this scenario (imagine the number of ifs needed to program this :P ).
Another problem would be the business side (as usual). Anybody who has been working on a project knows how fast the requirements are changing. If one entered all the rules in the code he would have to redeploy the software each time the requirements changed. That's why it is a good practice to divide the business logic from the code itself. Anyway, let's go back to our example.
To begin with let us take a look at the spreadsheets (before that it is worth taking a look at the
[JBoss website with precise description of how the decision table should look like](https://docs.jboss.org/drools/release/5.2.0.Final/drools-expert-docs/html/ch06.html)
):
The point of entry of our program is the first spreadsheet that checks if the given user should be granted with the possibility of buying a product (it will be better if you download the spreadsheets and play with them from Too Much Coding's repository at Bitbucket:
[user_table.xls](https://bitbucket.org/gregorin1987/too-much-coding/src/eeda79f74a4af30091490ab3507879254540e118/Drools/Decision%20table/src/main/resources/rules/user_table.xls?at=default)
and
[product_table.xls](https://bitbucket.org/gregorin1987/too-much-coding/src/eeda79f74a4af30091490ab3507879254540e118/Drools/Decision%20table/src/main/resources/rules/product_table.xls?at=default)
, or Github
[ user_table.xls ](https://github.com/marcingrzejszczak/too-much-coding/blob/master/Drools/Decision%20table/src/main/resources/rules/user_table.xls?raw=true)
and
[product_table.xls](https://github.com/marcingrzejszczak/too-much-coding/blob/master/Drools/Decision%20table/src/main/resources/rules/product_table.xls?raw=true)
):
**user_table.xls (tables worksheet)**
[![](https://1.bp.blogspot.com/-J5-tXPECwJI/UQ7TmutXq5I/AAAAAAAAAzU/3c9KrRFaZ5w/s320/user_table.png)](https://1.bp.blogspot.com/-J5-tXPECwJI/UQ7TmutXq5I/AAAAAAAAAzU/3c9KrRFaZ5w/s1600/user_table.png)
Once the user has been approved he might get a discount:
**product_table.xls (tables worksheet)**
[![](https://2.bp.blogspot.com/-v-fAUhcn4HI/UQ69XrrT06I/AAAAAAAAAy0/8fU1nropIhU/s320/product_table.png)](https://2.bp.blogspot.com/-v-fAUhcn4HI/UQ69XrrT06I/AAAAAAAAAy0/8fU1nropIhU/s1600/product_table.png)
**product_table.xls (lists worksheet)**
[![](https://4.bp.blogspot.com/-_MkDqjqjuDU/UQ69mEuCy5I/AAAAAAAAAy8/u09czbpift0/s320/user_table_lists.png)](https://4.bp.blogspot.com/-_MkDqjqjuDU/UQ69mEuCy5I/AAAAAAAAAy8/u09czbpift0/s1600/user_table_lists.png)
As you can see in the images the business problem is quite complex. Each row represents a rule, and each column represents a condition.
[Do you remember the rules syntax from my recent post?](https://toomuchcoding.com/2013/01/drools-integration-with-spring-vs.html)
So you would understand the hidden part of the spreadsheet that is right above the first visible row:
[![](https://2.bp.blogspot.com/--f9FvvQaAhM/UQ6-gcczcQI/AAAAAAAAAzM/GxGmoV6s3OE/s320/product_table_header.png)](https://2.bp.blogspot.com/--f9FvvQaAhM/UQ6-gcczcQI/AAAAAAAAAzM/GxGmoV6s3OE/s1600/product_table_header.png)
The rows from 2 to 6 represent some fixed configuration values such as rule set, imports (
[ you've already seen that in my recent post](https://toomuchcoding.com/2013/01/drools-integration-with-spring-vs.html)
) and functions. Next in row number 7 you can find the name of the RuleTable. Then in row number 8 you have in our scenario either a CONDITION or an ACTION - so in other words either the LHS or rhe RHS respectively. Row number 9 is both representation of types presented in the condition and the binding to a variable. In row number 10 we have the exact LHS condition. Row number 11 shows the label of columns. From row number 12 we have the rules one by one. You can find the spreadsheets in the sources.
Now let's take a look at the code. Let's start with taking a look at the schemas defining the Product and the User.
**Person.xsd**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<xsd:schema xmlns:xsd="https://www.w3.org/2001/XMLSchema">
  <xsd:include schemaLocation="user.xsd"/>
  <xsd:element name="Product">
    <xsd:complexType>
      <xsd:sequence>
        <xsd:element name="Name" type="xsd:string"/>
        <xsd:element name="Type" type="ProductType"/>
        <xsd:element name="Price" type="xsd:double"/>
        <xsd:element name="CountryOfOrigin" type="CountryType"/>
        <xsd:element name="AdditionalInfo" type="xsd:string"/>
        <xsd:element name="Quantity" type="xsd:int"/>
      </xsd:sequence>
    </xsd:complexType>
  </xsd:element>
  <xsd:simpleType name="ProductType">
    <xsd:restriction base="xsd:string">
      <xsd:enumeration value="MEDICAL"/>
      <xsd:enumeration value="ELECTRONIC"/>
    </xsd:restriction>
  </xsd:simpleType>
</xsd:schema>
```
**User.xsd**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<xsd:schema xmlns:xsd="https://www.w3.org/2001/XMLSchema">
  <xsd:include schemaLocation="product.xsd"/>
  <xsd:element name="User">
    <xsd:complexType>
      <xsd:sequence>
        <xsd:element name="UserName" type="xsd:string"/>
        <xsd:element name="UserAge" type="xsd:int"/>
        <xsd:element name="UserCountry" type="CountryType"/>
        <xsd:element name="Decision" type="DecisionType"/>
        <xsd:element name="DecisionDescription" type="xsd:string"/>
      </xsd:sequence>
    </xsd:complexType>
  </xsd:element>
  <xsd:simpleType name="CountryType">
    <xsd:restriction base="xsd:string">
      <xsd:enumeration value="PL"/>
      <xsd:enumeration value="USA"/>
      <xsd:enumeration value="GER"/>
      <xsd:enumeration value="SWE"/>
      <xsd:enumeration value="UK"/>
      <xsd:enumeration value="ESP"/>
    </xsd:restriction>
  </xsd:simpleType>
  <xsd:simpleType name="DecisionType">
    <xsd:restriction base="xsd:string">
      <xsd:enumeration value="ACCEPTED"/>
      <xsd:enumeration value="REJECTED"/>
    </xsd:restriction>
  </xsd:simpleType>
</xsd:schema>
```
Due to the fact that we are using maven we may use a plugin that will convert the XSD into Java classes.
part of the
**pom.xml  **
```xml
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
        <packageName>pl.grzejszczak.marcin.drools.decisiontable.model</packageName>
        <schemaDirectory>${project.basedir}/src/main/resources/xsd</schemaDirectory>
      </configuration>
    </plugin>
  </plugins>
</build>
```
Thanks to this plugin we have our generated by JAXB classes in the
**pl.grzejszczak.marcin.decisiontable.model**
package.
Now off to the drools-context.xml file where we've defined all the necessary beans as far as Drools are concerned:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="https://www.springframework.org/schema/beans" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:drools="https://drools.org/schema/drools-spring" xsi:schemaLocation="https://www.springframework.org/schema/beans https://www.springframework.org/schema/beans/spring-beans-3.0.xsd https://drools.org/schema/drools-spring https://drools.org/schema/drools-spring.xsd">
  <!-- Grid Node identifier that is registered in the CamelContext -->
  <drools:grid-node id="node1"/>
  <drools:kbase id="productsKBase" node="node1">
    <drools:resources>
      <drools:resource type="DTABLE" source="classpath:rules/product_table.xls"/>
    </drools:resources>
  </drools:kbase>
  <drools:ksession id="productsKSession" name="productsKSession" type="stateless" kbase="productsKBase" node="node1"/>
  <drools:kbase id="usersKBase" node="node1">
    <drools:resources>
      <drools:resource type="DTABLE" source="classpath:rules/user_table.xls"/>
    </drools:resources>
  </drools:kbase>
  <drools:ksession id="usersKSession" name="usersKSession" type="stateless" kbase="usersKBase" node="node1"/>
</beans>
```
As you can see in comparison to
[the application context from the recent post ](https://toomuchcoding.com/2013/01/drools-integration-with-spring-vs.html)
there are some differences. First instead of passing the DRL file as the resource inside the knowledge base we are providing the Decision table (
DTABLE
). I've decided to pass in two seperate files but you can provide one file with several worksheets and access those worksheets (through the
decisiontable-conf
element). Also there is an additional element called
node
. We have to choose an implementation of the Node interface (Execution, Grid...) for the Camel route to work properly as you will see in a couple of seconds in the Spring application context file.
**applicationContext.xml**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="https://www.springframework.org/schema/beans" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:camel="https://camel.apache.org/schema/spring" xmlns:context="https://www.springframework.org/schema/context" xsi:schemaLocation="https://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context-3.0.xsd https://www.springframework.org/schema/beans https://www.springframework.org/schema/beans/spring-beans-3.0.xsd https://camel.apache.org/schema/spring https://camel.apache.org/schema/spring/camel-spring-2.8.0.xsd">
  <import resource="classpath:drools-context.xml"/>
  <!-- Show Spring where to search for the beans (in which packages) -->
  <context:component-scan base-package="pl.grzejszczak.marcin.drools.decisiontable" />
  <camel:camelContext id="camelContext">
    <camel:route id="acceptanceRoute">
      <camel:from uri="direct:acceptanceRoute"/>
      <camel:to uri="drools:node1/usersKSession"/>
    </camel:route>
    <camel:route id="discountRoute">
      <camel:from uri="direct:discountRoute"/>
      <camel:to uri="drools:node1/productsKSession"/>
    </camel:route>
  </camel:camelContext>
</beans>
```
As you can see in order to access the Drools Camel Component we have to provide the
**node **
through which we will access the proper
** knowledge session**
. We have defined two routes - the first one ends at the Drools component that accesses the users knowledge session and the other the products knowledge session.
We have a ProductService interface implementation called ProductServiceImpl that given an input User and Product objects pass them through the Camel's Producer Template to two Camel routes each ending at the Drools components. The concept behind this product service is that we are first processing the User if he can even buy the software and then we are checking what kind of a discount he would receive. From the service's point of view in fact we are just sending the object out and waiting for the response. Finally having reveived the response we are passing the User and the Product to the Financial Service implementation that will bill the user for the products that he has bought or reject his offer if needed.
**ProductServiceImpl.java**
```java
package pl.grzejszczak.marcin.drools.decisiontable.service;
import org.apache.camel.CamelContext;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;
import pl.grzejszczak.marcin.drools.decisiontable.model.Product;
import pl.grzejszczak.marcin.drools.decisiontable.model.User;
import static com.google.common.collect.Lists.newArrayList;
/** * Created with IntelliJ IDEA. * User: mgrzejszczak * Date: 14.01.13 */@Component("productServiceImpl")public class ProductServiceImpl implements ProductService {
    private static final Logger LOGGER = LoggerFactory.getLogger(ProductServiceImpl.class);
@Autowired    CamelContext camelContext;
@Autowired    FinancialService financialService;
@Override    public void runProductLogic(User user, Product product) {
        LOGGER.debug("Running product logic - first acceptance Route, then discount Route");
camelContext.createProducerTemplate().sendBody("direct:acceptanceRoute", newArrayList(user, product));
camelContext.createProducerTemplate().sendBody("direct:discountRoute", newArrayList(user, product));
financialService.processOrder(user, product);
}

}
```
```

```
Another crucial thing to remember about is that the Camel Drools Component requires the Command object as the input. As you can see, in the body we are sending a list of objects (and these are not Command objects). I did it on purpose since in my opinion it is better not to bind our code to a concrete solution. What if we find out that there is a better solution than Drools? Will we change all the code that we have created or just change the Camel route to point at our new solution? That's why Camel has the TypeConverters. We have our own here as well. First of all let's take a look at the implementation.
**ProductTypeConverter.java**
```java
package pl.grzejszczak.marcin.drools.decisiontable.converter;
import org.apache.camel.Converter;
import org.drools.command.Command;
import org.drools.command.CommandFactory;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import pl.grzejszczak.marcin.drools.decisiontable.model.Product;
import java.util.List;
/** * Created with IntelliJ IDEA. * User: mgrzejszczak * Date: 30.01.13 * Time: 21:42 */@Converterpublic class ProductTypeConverter {
    private static final Logger LOGGER = LoggerFactory.getLogger(ProductTypeConverter.class);
@Converter    public static Command toCommandFromList(List inputList) {
        LOGGER.debug("Executing ProductTypeConverter's toCommandFromList method");
return CommandFactory.newInsertElements(inputList);
}
    @Converter    public static Command toCommand(Product product) {
        LOGGER.debug("Executing ProductTypeConverter's toCommand method");
return CommandFactory.newInsert(product);
}

}
```
There is a good tutorial on TypeConverters on the
[Camel website](https://camel.apache.org/type-converter.html)
- if you needed some more indepth info about it. Anyway, we are annotating our class and the functions used to convert different types into one another. What is important here is that we are showing Camel how to convert a list and a single product to Commands. Due to type erasure this will work regardless of the provided type that is why even though we are giving a list of Product  and User, the toCommandFromList function will get executed.
In addition to this in order for the type converter to work we have to provide the fully quallified name of our class (FQN) in the
**/META-INF/services/org/apache/camel/TypeConverter**
file.
**TypeConverter**
```
pl.grzejszczak.marcin.drools.decisiontable.converter.ProductTypeConverter
```
In order to properly test our functionality one should write quite a few tests that would verify the rules. A pretty good way would be to have input files stored in the test resources folders that are passed to the rule engine and then the result would be compared against the verified output (unfortunately it is rather impossible to make the business side develop such a reference set of outputs). Anyway let's take a look at the unit test that verifies only a few of the rules and the logs that are produced from running those rules:
**ProductServiceImplTest.java**
```java
package pl.grzejszczak.marcin.drools.decisiontable.service.drools;
import org.apache.commons.lang.builder.ReflectionToStringBuilder;
import org.apache.commons.lang.builder.ToStringStyle;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
import pl.grzejszczak.marcin.drools.decisiontable.model.*;
import pl.grzejszczak.marcin.drools.decisiontable.service.ProductService;
import static org.junit.Assert.assertEquals;
import static org.junit.Assert.assertTrue;
/** * Created with IntelliJ IDEA. * User: mgrzejszczak * Date: 03.02.13 * Time: 16:06 */@RunWith(SpringJUnit4ClassRunner.class)@ContextConfiguration("classpath:applicationContext.xml")public class ProductServiceImplTest {
    private static final Logger LOGGER = LoggerFactory.getLogger(ProductServiceImplTest.class);
@Autowired    ProductService objectUnderTest;
@Test    public void testRunProductLogicUserPlUnderageElectronicCountryPL() throws Exception {
        int initialPrice = 1000;
int userAge = 6;
int quantity = 10;
User user = createUser("Smith", CountryType.PL, userAge);
Product product = createProduct("Electronic", initialPrice, CountryType.PL, ProductType.ELECTRONIC, quantity);
printInputs(user, product);
objectUnderTest.runProductLogic(user, product);
printInputs(user, product);
assertTrue(product.getPrice() == initialPrice);
assertEquals(DecisionType.REJECTED, user.getDecision());
}
    @Test    public void testRunProductLogicUserPlHighAgeElectronicCountryPLLowQuantity() throws Exception {
        int initialPrice = 1000;
int userAge = 19;
int quantity = 1;
User user = createUser("Smith", CountryType.PL, userAge);
Product product = createProduct("Electronic", initialPrice, CountryType.PL, ProductType.ELECTRONIC, quantity);
printInputs(user, product);
objectUnderTest.runProductLogic(user, product);
printInputs(user, product);
assertTrue(product.getPrice() == initialPrice);
assertEquals(DecisionType.ACCEPTED, user.getDecision());
}
    @Test    public void testRunProductLogicUserPlHighAgeElectronicCountryPLHighQuantity() throws Exception {
        int initialPrice = 1000;
int userAge = 19;
int quantity = 8;
User user = createUser("Smith", CountryType.PL, userAge);
Product product = createProduct("Electronic", initialPrice, CountryType.PL, ProductType.ELECTRONIC, quantity);
printInputs(user, product);
objectUnderTest.runProductLogic(user, product);
printInputs(user, product);
double expectedDiscount = 0.1;
assertTrue(product.getPrice() == initialPrice * (1 - expectedDiscount));
assertEquals(DecisionType.ACCEPTED, user.getDecision());
}
    @Test    public void testRunProductLogicUserUsaLowAgeElectronicCountryPLHighQuantity() throws Exception {
        int initialPrice = 1000;
int userAge = 19;
int quantity = 8;
User user = createUser("Smith", CountryType.USA, userAge);
Product product = createProduct("Electronic", initialPrice, CountryType.PL, ProductType.ELECTRONIC, quantity);
printInputs(user, product);
objectUnderTest.runProductLogic(user, product);
printInputs(user, product);
assertTrue(product.getPrice() == initialPrice);
assertEquals(DecisionType.REJECTED, user.getDecision());
}
    @Test    public void testRunProductLogicUserUsaHighAgeMedicalCountrySWELowQuantity() throws Exception {
        int initialPrice = 1000;
int userAge = 22;
int quantity = 4;
User user = createUser("Smith", CountryType.USA, userAge);
Product product = createProduct("Some name", initialPrice, CountryType.SWE, ProductType.MEDICAL, quantity);
printInputs(user, product);
objectUnderTest.runProductLogic(user, product);
printInputs(user, product);
assertTrue(product.getPrice() == initialPrice);
assertEquals(DecisionType.ACCEPTED, user.getDecision());
}
    @Test    public void testRunProductLogicUserUsaHighAgeMedicalCountrySWEHighQuantity() throws Exception {
        int initialPrice = 1000;
int userAge = 22;
int quantity = 8;
User user = createUser("Smith", CountryType.USA, userAge);
Product product = createProduct("Some name", initialPrice, CountryType.SWE, ProductType.MEDICAL, quantity);
printInputs(user, product);
objectUnderTest.runProductLogic(user, product);
printInputs(user, product);
double expectedDiscount = 0.25;
assertTrue(product.getPrice() == initialPrice * (1 - expectedDiscount));
assertEquals(DecisionType.ACCEPTED, user.getDecision());
}
    private void printInputs(User user, Product product) {
        LOGGER.debug(ReflectionToStringBuilder.reflectionToString(user, ToStringStyle.MULTI_LINE_STYLE));
LOGGER.debug(ReflectionToStringBuilder.reflectionToString(product, ToStringStyle.MULTI_LINE_STYLE));
}
    private User createUser(String name, CountryType countryType, int userAge) {
        User user = new User();
user.setUserName(name);
user.setUserCountry(countryType);
user.setUserAge(userAge);
return user;
}
    private Product createProduct(String name, double price, CountryType countryOfOrigin, ProductType productType, int quantity) {
        Product product = new Product();
product.setPrice(price);
product.setCountryOfOrigin(countryOfOrigin);
product.setName(name);
product.setType(productType);
product.setQuantity(quantity);
return product;
}

}
```
Of course the log.debugs in the tests are totally redundant but I wanted you to quickly see that the rules are operational :) Sorry for the length of the logs but I wrote a few tests to show different combinations of rules (in fact it's better too have too many logs than the other way round :) )
```groovy
pl.grzejszczak.marcin.drools.decisiontable.service.drools.ProductServiceImplTest: 150 pl.grzejszczak.marcin.drools.decisiontable.model.User@1d48043[  userName=Smith  userAge=6  userCountry=PL  decision=<null>  decisionDescription=<null>]pl.grzejszczak.marcin.drools.decisiontable.service.drools.ProductServiceImplTest: 151 pl.grzejszczak.marcin.drools.decisiontable.model.Product@1e8f2a0[  name=Electronic  type=ELECTRONIC  price=1000.0  countryOfOrigin=PL  additionalInfo=<null>  quantity=10]pl.grzejszczak.marcin.drools.decisiontable.service.ProductServiceImpl: 31 Running product logic - first acceptance Route,
then discount Routepl.grzejszczak.marcin.drools.decisiontable.converter.ProductTypeConverter: 25 Executing ProductTypeConverter's toCommandFromList methodpl.grzejszczak.marcin.drools.decisiontable.service.ProductService: 8 Sorry,
according to your age (< 18) and country (PL) you can't buy this productpl.grzejszczak.marcin.drools.decisiontable.converter.ProductTypeConverter: 25 Executing ProductTypeConverter's toCommandFromList methodpl.grzejszczak.marcin.drools.decisiontable.service.FinancialServiceImpl: 29 Sorry,
user has been rejected...pl.grzejszczak.marcin.drools.decisiontable.service.drools.ProductServiceImplTest: 150 pl.grzejszczak.marcin.drools.decisiontable.model.User@1d48043[  userName=Smith  userAge=6  userCountry=PL  decision=REJECTED  decisionDescription=Sorry,
according to your age (< 18) and country (PL) you can't buy this product]pl.grzejszczak.marcin.drools.decisiontable.service.drools.ProductServiceImplTest: 151 pl.grzejszczak.marcin.drools.decisiontable.model.Product@1e8f2a0[  name=Electronic  type=ELECTRONIC  price=1000.0  countryOfOrigin=PL  additionalInfo=<null>  quantity=10]pl.grzejszczak.marcin.drools.decisiontable.service.drools.ProductServiceImplTest: 150 pl.grzejszczak.marcin.drools.decisiontable.model.User@b28f30[  userName=Smith  userAge=19  userCountry=PL  decision=<null>  decisionDescription=<null>]pl.grzejszczak.marcin.drools.decisiontable.service.drools.ProductServiceImplTest: 151 pl.grzejszczak.marcin.drools.decisiontable.model.Product@d6a0e0[  name=Electronic  type=ELECTRONIC  price=1000.0  countryOfOrigin=PL  additionalInfo=<null>  quantity=1]pl.grzejszczak.marcin.drools.decisiontable.service.ProductServiceImpl: 31 Running product logic - first acceptance Route,
then discount Routepl.grzejszczak.marcin.drools.decisiontable.converter.ProductTypeConverter: 25 Executing ProductTypeConverter's toCommandFromList methodpl.grzejszczak.marcin.drools.decisiontable.service.ProductService: 8 Congratulations,
you have successfully bought the productpl.grzejszczak.marcin.drools.decisiontable.converter.ProductTypeConverter: 25 Executing ProductTypeConverter's toCommandFromList methodpl.grzejszczak.marcin.drools.decisiontable.service.ProductService: 8 Sorry,
no discount will be granted.pl.grzejszczak.marcin.drools.decisiontable.service.FinancialServiceImpl: 25 User has been approved - processing the order...pl.grzejszczak.marcin.drools.decisiontable.service.drools.ProductServiceImplTest: 150 pl.grzejszczak.marcin.drools.decisiontable.model.User@b28f30[  userName=Smith  userAge=19  userCountry=PL  decision=ACCEPTED  decisionDescription=Congratulations,
you have successfully bought the product]pl.grzejszczak.marcin.drools.decisiontable.service.drools.ProductServiceImplTest: 151 pl.grzejszczak.marcin.drools.decisiontable.model.Product@d6a0e0[  name=Electronic  type=ELECTRONIC  price=1000.0  countryOfOrigin=PL  additionalInfo=Sorry,
no discount will be granted.  quantity=1]pl.grzejszczak.marcin.drools.decisiontable.service.drools.ProductServiceImplTest: 150 pl.grzejszczak.marcin.drools.decisiontable.model.User@14510ac[  userName=Smith  userAge=19  userCountry=PL  decision=<null>  decisionDescription=<null>]pl.grzejszczak.marcin.drools.decisiontable.service.drools.ProductServiceImplTest: 151 pl.grzejszczak.marcin.drools.decisiontable.model.Product@1499616[  name=Electronic  type=ELECTRONIC  price=1000.0  countryOfOrigin=PL  additionalInfo=<null>  quantity=8]pl.grzejszczak.marcin.drools.decisiontable.service.ProductServiceImpl: 31 Running product logic - first acceptance Route,
then discount Routepl.grzejszczak.marcin.drools.decisiontable.converter.ProductTypeConverter: 25 Executing ProductTypeConverter's toCommandFromList methodpl.grzejszczak.marcin.drools.decisiontable.service.ProductService: 8 Congratulations,
you have successfully bought the productpl.grzejszczak.marcin.drools.decisiontable.converter.ProductTypeConverter: 25 Executing ProductTypeConverter's toCommandFromList methodpl.grzejszczak.marcin.drools.decisiontable.service.ProductService: 8 Congratulations - you've been granted a 10% discount!pl.grzejszczak.marcin.drools.decisiontable.service.FinancialServiceImpl: 25 User has been approved - processing the order...pl.grzejszczak.marcin.drools.decisiontable.service.drools.ProductServiceImplTest: 150 pl.grzejszczak.marcin.drools.decisiontable.model.User@14510ac[  userName=Smith  userAge=19  userCountry=PL  decision=ACCEPTED  decisionDescription=Congratulations,
you have successfully bought the product]pl.grzejszczak.marcin.drools.decisiontable.service.drools.ProductServiceImplTest: 151 pl.grzejszczak.marcin.drools.decisiontable.model.Product@1499616[  name=Electronic  type=ELECTRONIC  price=900.0  countryOfOrigin=PL  additionalInfo=Congratulations - you've been granted a 10% discount!  quantity=8]pl.grzejszczak.marcin.drools.decisiontable.service.drools.ProductServiceImplTest: 150 pl.grzejszczak.marcin.drools.decisiontable.model.User@17667bd[  userName=Smith  userAge=19  userCountry=USA  decision=<null>  decisionDescription=<null>]pl.grzejszczak.marcin.drools.decisiontable.service.drools.ProductServiceImplTest: 151 pl.grzejszczak.marcin.drools.decisiontable.model.Product@ad9f5d[  name=Electronic  type=ELECTRONIC  price=1000.0  countryOfOrigin=PL  additionalInfo=<null>  quantity=8]pl.grzejszczak.marcin.drools.decisiontable.service.ProductServiceImpl: 31 Running product logic - first acceptance Route,
then discount Routepl.grzejszczak.marcin.drools.decisiontable.converter.ProductTypeConverter: 25 Executing ProductTypeConverter's toCommandFromList methodpl.grzejszczak.marcin.drools.decisiontable.service.ProductService: 8 Sorry,
according to your age (< 18) and country (USA) you can't buy this productpl.grzejszczak.marcin.drools.decisiontable.converter.ProductTypeConverter: 25 Executing ProductTypeConverter's toCommandFromList methodpl.grzejszczak.marcin.drools.decisiontable.service.FinancialServiceImpl: 29 Sorry,
user has been rejected...pl.grzejszczak.marcin.drools.decisiontable.service.drools.ProductServiceImplTest: 150 pl.grzejszczak.marcin.drools.decisiontable.model.User@17667bd[  userName=Smith  userAge=19  userCountry=USA  decision=REJECTED  decisionDescription=Sorry,
according to your age (< 18) and country (USA) you can't buy this product]pl.grzejszczak.marcin.drools.decisiontable.service.drools.ProductServiceImplTest: 151 pl.grzejszczak.marcin.drools.decisiontable.model.Product@ad9f5d[  name=Electronic  type=ELECTRONIC  price=1000.0  countryOfOrigin=PL  additionalInfo=<null>  quantity=8]pl.grzejszczak.marcin.drools.decisiontable.service.drools.ProductServiceImplTest: 150 pl.grzejszczak.marcin.drools.decisiontable.model.User@9ff588[  userName=Smith  userAge=22  userCountry=USA  decision=<null>  decisionDescription=<null>]pl.grzejszczak.marcin.drools.decisiontable.service.drools.ProductServiceImplTest: 151 pl.grzejszczak.marcin.drools.decisiontable.model.Product@1b0d2d0[  name=Some name  type=MEDICAL  price=1000.0  countryOfOrigin=SWE  additionalInfo=<null>  quantity=4]pl.grzejszczak.marcin.drools.decisiontable.service.ProductServiceImpl: 31 Running product logic - first acceptance Route,
then discount Routepl.grzejszczak.marcin.drools.decisiontable.converter.ProductTypeConverter: 25 Executing ProductTypeConverter's toCommandFromList methodpl.grzejszczak.marcin.drools.decisiontable.service.ProductService: 8 Congratulations,
you have successfully bought the productpl.grzejszczak.marcin.drools.decisiontable.converter.ProductTypeConverter: 25 Executing ProductTypeConverter's toCommandFromList methodpl.grzejszczak.marcin.drools.decisiontable.service.FinancialServiceImpl: 25 User has been approved - processing the order...pl.grzejszczak.marcin.drools.decisiontable.service.drools.ProductServiceImplTest: 150 pl.grzejszczak.marcin.drools.decisiontable.model.User@9ff588[  userName=Smith  userAge=22  userCountry=USA  decision=ACCEPTED  decisionDescription=Congratulations,
you have successfully bought the product]pl.grzejszczak.marcin.drools.decisiontable.service.drools.ProductServiceImplTest: 151 pl.grzejszczak.marcin.drools.decisiontable.model.Product@1b0d2d0[  name=Some name  type=MEDICAL  price=1000.0  countryOfOrigin=SWE  additionalInfo=<null>  quantity=4]pl.grzejszczak.marcin.drools.decisiontable.service.drools.ProductServiceImplTest: 150 pl.grzejszczak.marcin.drools.decisiontable.model.User@1b27882[  userName=Smith  userAge=22  userCountry=USA  decision=<null>  decisionDescription=<null>]pl.grzejszczak.marcin.drools.decisiontable.service.drools.ProductServiceImplTest: 151 pl.grzejszczak.marcin.drools.decisiontable.model.Product@5b84b[  name=Some name  type=MEDICAL  price=1000.0  countryOfOrigin=SWE  additionalInfo=<null>  quantity=8]pl.grzejszczak.marcin.drools.decisiontable.service.ProductServiceImpl: 31 Running product logic - first acceptance Route,
then discount Routepl.grzejszczak.marcin.drools.decisiontable.converter.ProductTypeConverter: 25 Executing ProductTypeConverter's toCommandFromList methodpl.grzejszczak.marcin.drools.decisiontable.service.ProductService: 8 Congratulations,
you have successfully bought the productpl.grzejszczak.marcin.drools.decisiontable.converter.ProductTypeConverter: 25 Executing ProductTypeConverter's toCommandFromList methodpl.grzejszczak.marcin.drools.decisiontable.service.ProductService: 8 Congratulations,
you are granted a discountpl.grzejszczak.marcin.drools.decisiontable.service.FinancialServiceImpl: 25 User has been approved - processing the order...pl.grzejszczak.marcin.drools.decisiontable.service.drools.ProductServiceImplTest: 150 pl.grzejszczak.marcin.drools.decisiontable.model.User@1b27882[  userName=Smith  userAge=22  userCountry=USA  decision=ACCEPTED  decisionDescription=Congratulations,
you have successfully bought the product]pl.grzejszczak.marcin.drools.decisiontable.service.drools.ProductServiceImplTest: 151 pl.grzejszczak.marcin.drools.decisiontable.model.Product@5b84b[  name=Some name  type=MEDICAL  price=750.0  countryOfOrigin=SWE  additionalInfo=Congratulations,
you are granted a discount  quantity=8]
```
In this post I've presented how you can push some of your developing work to your BA by giving him a tool which he can be able to work woth - the Decision Tables in a spreadsheet. What is more now you will now how to integrate Drools with Camel. Hopefully you will see how you can simplify (thus minimize the cost of implementing and supporting) the implementation of business rules bearing in mind how prone to changes they are. I hope that this example will even better illustrate how difficult it would be to implement all the business rules in Java than in the
[previous post about Drools.](https://toomuchcoding.com/2013/01/drools-integration-with-spring-vs.html)
If you have any experience with Drools in terms of decision tables, integration with Spring and Camel please feel free to leave a comment - let's have a discussion on that :)
All the code is available at Too Much Coding repository at
[Bitbucket ](https://bitbucket.org/gregorin1987/too-much-coding/src/eeda79f74a4af30091490ab3507879254540e118/Drools/Decision%20table?at=default)
and
[GitHub](https://github.com/marcingrzejszczak/too-much-coding/tree/master/Drools/Decision%20table)
.
Cheers!
