---
title: "Drools Integration With Spring Vs"
summary: "Hi! Often in your work you can come across with issues related to business logic."
date: 2013-01-15

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
Often in your work you can come across with issues related to business logic. Let us assume that you have dozens of rules (for the time being in our project we have more than 50 and I used to work on a project where we had hundreds of those rules) that you have to implement, divide into some classes, subclasses, abstractions and of course unit test. This can be difficult and timeconsuming to both write and support. There are many ways of dealing with this problem and I will show you one of them - JBoss Drools.
Drools is a library specifically created for such purposes like implementing rules. As presented in Wikipedia:
> "Drools is a rule engine implementaion based on Charles Forgy's Rete Algorithm tailored for the Java language."
It contains a rule engine that can process rules wirtten using the Drools language (you can also provide rules in Excel spreadsheets! - perfect for Business side to support and maintain).
In the following example we will take a look at the way one can integrate JBoss Drools with Spring and an example of solving a similar problem without Drools.
[]()
Let us assume that we hava a POJO - a product that can represent either a Medical or Electronic product.
**Product.java  **
```java
package pl.grzejszczak.marcin.drools.springintegration.model;
import pl.grzejszczak.marcin.drools.springintegration.enums.TypeEnum;
/** * Created with IntelliJ IDEA. * User: mgrzejszczak * Date: 14.01.13 */public class Product {
    private final TypeEnum typeEnum;
private String productName;
public Product(TypeEnum typeEnum) {
        this.typeEnum = typeEnum;
productName = typeEnum.getSomeOutputString();
}
    public TypeEnum getTypeEnum() {
        return typeEnum;
}
    public String getProductName() {
        return productName;
}
    public void setProductName(String productName) {
        this.productName = productName;
}

}
```
What defines the type of a product is the TypeEnum. It also has an outputString - let's assume that it defines a brand of a product (or whatever you want ;) )
**TypeEnum.java  **
```java
package pl.grzejszczak.marcin.drools.springintegration.enums;
/** * Created with IntelliJ IDEA. * User: mgrzejszczak * Date: 14.01.13 */public enum TypeEnum {
    MEDICAL("medical", "aaabbbccc"), ELECTRONIC("electronic", "cccbbbaaa");
private final String type;
private final String someOutputString;
private TypeEnum(String type, String someOutputString) {
        this.type = type;
this.someOutputString = someOutputString;
}
    public String getType() {
        return type;
}
    public String getSomeOutputString() {
        return someOutputString;
}

}
```
Let's say that the logic behind our rools is such that depending on the type of the enum we want to have some processing done (in our case we will have the same type of processing - converting each 'a' to 'b' in the output string).
**NoRulesProductServiceImpl.java  **
```java
package pl.grzejszczak.marcin.drools.springintegration.service.nondrools;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Component;
import pl.grzejszczak.marcin.drools.springintegration.enums.TypeEnum;
import pl.grzejszczak.marcin.drools.springintegration.model.Product;
import pl.grzejszczak.marcin.drools.springintegration.service.Processor;
import pl.grzejszczak.marcin.drools.springintegration.service.ProductService;
import java.util.List;
import static com.google.common.collect.Lists.newArrayList;
/** * Created with IntelliJ IDEA. * User: mgrzejszczak * Date: 14.01.13 */@Component("NoRulesProductServiceImpl")public class NoRulesProductServiceImpl implements ProductService {
    private static final Logger LOGGER = LoggerFactory.getLogger(NoRulesProductServiceImpl.class);
@Autowired    @Qualifier("ProductProcessingService")    private Processor<List<Product>> productProcessingService;
@Override    public void runProductLogic() {
        LOGGER.debug("Running product logic without Drools");
Product medicalProduct = new Product(TypeEnum.MEDICAL);
Product electronicProduct = new Product(TypeEnum.ELECTRONIC);
LOGGER.debug("Running rules for products...");
productProcessingService.process(newArrayList(medicalProduct, electronicProduct));
LOGGER.debug("...finished running products.");
}

}
```
The ProductProcessingService is itterating over the given products, finds a producer for them and processes them.
**ProductProcessingService.java  **
```java
package pl.grzejszczak.marcin.drools.springintegration.service.nondrools;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Component;
import pl.grzejszczak.marcin.drools.springintegration.factory.ProcessingFactory;
import pl.grzejszczak.marcin.drools.springintegration.model.Product;
import pl.grzejszczak.marcin.drools.springintegration.service.Processor;
import java.util.List;
/** * Created with IntelliJ IDEA. * User: mgrzejszczak * Date: 14.01.13 */@Component("ProductProcessingService")public class ProductProcessingService implements Processor<List<Product>> {
    @Autowired    @Qualifier("NoRulesProcessingFactory")    private ProcessingFactory<Processor, Product> processingFactory;
@Override    public void process(List<Product> input) {
        for(Product product : input) {
            Processor<Product> processor = processingFactory.createProcessingObject(product);
processor.process(product);
}

}

}
```
The ProcessingFactory is an interface that basing on the given input (Product) produces an ouput (Processor) that afterwards does further processing. In our case we have a factory that instead of using a bunch of ifs (imagine that we have more than just two types of products) is using a map that matches a type of product with an implementation of a processor. As you can see we change a sequence of ifs into a single get.
**NoRulesProcessingFactory.java  **
```java
package pl.grzejszczak.marcin.drools.springintegration.factory.nondrools;
import com.google.common.collect.ImmutableMap;
import org.springframework.stereotype.Component;
import pl.grzejszczak.marcin.drools.springintegration.enums.TypeEnum;
import pl.grzejszczak.marcin.drools.springintegration.factory.ProcessingFactory;
import pl.grzejszczak.marcin.drools.springintegration.model.Product;
import pl.grzejszczak.marcin.drools.springintegration.service.nondrools.ElectronicProductProcessingService;
import pl.grzejszczak.marcin.drools.springintegration.service.nondrools.MedicalProductProcessingService;
import pl.grzejszczak.marcin.drools.springintegration.service.Processor;
import java.util.Map;
/** * Created with IntelliJ IDEA. * User: mgrzejszczak * Date: 14.01.13 */@Component("NoRulesProcessingFactory")public class NoRulesProcessingFactory  implements ProcessingFactory<Processor, Product> {
    private static final Map<TypeEnum, Processor> PROCESSOR_MAP = new ImmutableMap.Builder<TypeEnum, Processor>().            put(TypeEnum.MEDICAL, new MedicalProductProcessingService()).            put(TypeEnum.ELECTRONIC, new ElectronicProductProcessingService()).            build();
/**     * By using the map we don't have any ifs     * @param inputObject     * @return     */    @Override    public Processor createProcessingObject(Product inputObject) {
        return PROCESSOR_MAP.get(inputObject.getTypeEnum());
}

}
```
I will present here only one ProcessingService since the other one is exactly the same (I just wanted to show the concept).
**ElectronicProductProcessingService.java  **
```java
package pl.grzejszczak.marcin.drools.springintegration.service.nondrools;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import pl.grzejszczak.marcin.drools.springintegration.enums.TypeEnum;
import pl.grzejszczak.marcin.drools.springintegration.model.Product;
import pl.grzejszczak.marcin.drools.springintegration.service.Processor;
import pl.grzejszczak.marcin.drools.springintegration.utils.SomeUtil;
import static com.google.common.base.Preconditions.checkArgument;
import static java.lang.String.format;
/** * Created with IntelliJ IDEA. * User: mgrzejszczak * Date: 14.01.13 */public class ElectronicProductProcessingService implements Processor<Product> {
    private static final Logger LOGGER = LoggerFactory.getLogger(ElectronicProductProcessingService.class);
@Override    public void process(Product input) {
        checkArgument(TypeEnum.ELECTRONIC.equals(input.getTypeEnum()), "This processing service works only for electronic devices");
checkArgument(!SomeUtil.replaceAWithB(input.getProductName()).equals(input.getProductName()), "The input has already been processed");
LOGGER.debug("Running processing for Electronic Product");
input.setProductName(SomeUtil.replaceAWithB(input.getProductName()));
LOGGER.debug(format("ELECTRONIC rule applied without Drools, product name is now equal to [%s]", input.getProductName()));
}

}
```
As you can see there are quite a few things that need to be tested and supported here. Imagine what would happen if we had 100 of types with more sophisticated rules than merely replacing one letter with the other.  So how can we do it with Drools? Let's start with taking a look at the pom.xml.
**pom.xml  **
```xml
<project xmlns="https://maven.apache.org/POM/4.0.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="https://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>pl.grzejszczak.marcin</groupId>
  <artifactId>drools-spring-integration</artifactId>
  <version>0.0.1-SNAPSHOT</version>
  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.6</maven.compiler.source>
    <maven.compiler.target>1.6</maven.compiler.target>
    <spring.version>3.1.1.RELEASE</spring.version>
  </properties>
  <repositories>
    <repository>
      <id>spring-release</id>
      <url>https://maven.springframework.org/release</url>
    </repository>
  </repositories>
  <dependencies>
    <!-- Spring -->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-core</artifactId>
      <version>${spring.version}</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>${spring.version}</version>
    </dependency>
    <dependency>
      <groupId>com.google.guava</groupId>
      <artifactId>guava</artifactId>
      <version>13.0.1</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-test</artifactId>
      <version>${spring.version}</version>
    </dependency>
    <dependency>
      <groupId>org.drools</groupId>
      <artifactId>drools-spring</artifactId>
      <version>5.4.0.Final</version>
    </dependency>
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-log4j12</artifactId>
      <version>1.6.6</version>
    </dependency>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.10</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
</project>
```
Let's take a look at the applicationContext.xml and the drools-context.xml. As for the first one what we do in fact is just showing where to scan for classes in terms of Spring and where to import the drools context from.
**applicationContext.xml  **
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="https://www.springframework.org/schema/beans" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:context="https://www.springframework.org/schema/context" xsi:schemaLocation="https://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context-3.0.xsd https://www.springframework.org/schema/beans https://www.springframework.org/schema/beans/spring-beans-3.0.xsd">
  <import resource="classpath:drools-context.xml"/>
  <!-- Show Spring where to search for the beans (in which packages) -->
  <context:component-scan base-package="pl.grzejszczak.marcin.drools.springintegration" />
</beans>
```
The context for drools. Take a look at the aliases for productsKSession. By providing alias we are joining two potential knowledge sessions into a single one. A single knowledge session is defined for a single knowledge base. For the knowledge base we are providing the list (in our case just a single resource) of drl files (we could have provided an excel spreadsheet).
**drools-context.xml  **
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="https://www.springframework.org/schema/beans" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:drools="https://drools.org/schema/drools-spring" xsi:schemaLocation="https://www.springframework.org/schema/beans https://www.springframework.org/schema/beans/spring-beans-3.0.xsd https://drools.org/schema/drools-spring https://drools.org/schema/drools-spring.xsd">
  <!-- KNOWLEDGE BASE FOR A GIVEN TYPE -->
  <drools:kbase id="productsKBase">
    <drools:resources>
      <drools:resource type="DRL" source="classpath:rules/products.drl"/>
    </drools:resources>
  </drools:kbase>
  <drools:ksession id="productsKSession" name="productsKSession" type="stateless" kbase="productsKBase"/>
  <alias name="productsKSession" alias="electronicKSession"/>
  <alias name="productsKSession" alias="medicalKSession"/>
</beans>
```
Let's check the drl file.
We define two rules - "MEDICAL rule" and "ELECTRONIC rule". For each case we are checking:
- whether the input object is of Product type
- whether it has typeEnum equal to either Medical or Electronic
- whether it hasn't already had it's productName changed
Then we are addressing the product by means of a variable $product. We are modifying the product using the
modify
keyword (which means that all the rules are rechecked - try removing the condition 'productName != replaceAWithB($product.typeEnum.someOutputString' and you will have an endless loop) by setting a new productName. Take a look at all the imports and imports of functions. You can execute a static function (pl.grzejszczak.marcin.drools.springintegration.utils.SomeUtil.replaceAWithB or org.drools.core.util.StringUtils.isEmpty) by importing it in the drl file.
At the end we are logging that a rule has been applied.
**products.drl  **
```java
package pl.grzejszczak.marcinimport org.slf4j.LoggerFactoryimport pl.grzejszczak.marcin.drools.springintegration.DroolsSpringimport pl.grzejszczak.marcin.drools.springintegration.model.Productimport pl.grzejszczak.marcin.drools.springintegration.enums.TypeEnumimport function pl.grzejszczak.marcin.drools.springintegration.utils.SomeUtil.replaceAWithBimport function org.drools.core.util.StringUtils.isEmptyrule "MEDICAL rule" dialect "mvel"when     $product : Product( typeEnum == TypeEnum.MEDICAL, productName != replaceAWithB($product.typeEnum.someOutputString) )then     modify ($product) {
productName = replaceAWithB($product.typeEnum.someOutputString)
}
     LoggerFactory.getLogger(DroolsSpring.class).debug(String.format("MEDICAL rule applied, product name is now equal to [%s]", $product.productName))endrule "ELECTRONIC rule" dialect "mvel"when     $product : Product( typeEnum == TypeEnum.ELECTRONIC, productName != replaceAWithB($product.typeEnum.someOutputString) )then     modify ($product) {
productName = replaceAWithB($product.typeEnum.someOutputString)
}
     LoggerFactory.getLogger(DroolsSpring.class).debug(String.format("ELECTRONIC rule applied, product name is now equal to [%s]", $product.productName))end
```
We use a factory that is choosing a proper StatelessKnowledgeSession - since we only want to modify an input object. In order to run Drools rules we are running the execute method with a list of input objects.
**ProductServiceImpl.java  **
```java
package pl.grzejszczak.marcin.drools.springintegration.service.drools;
import org.drools.runtime.StatelessKnowledgeSession;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Component;
import pl.grzejszczak.marcin.drools.springintegration.enums.TypeEnum;
import pl.grzejszczak.marcin.drools.springintegration.factory.ProcessingFactory;
import pl.grzejszczak.marcin.drools.springintegration.model.Product;
import pl.grzejszczak.marcin.drools.springintegration.service.ProductService;
import static com.google.common.collect.Lists.newArrayList;
/** * Created with IntelliJ IDEA. * User: mgrzejszczak * Date: 14.01.13 */@Component("ProductServiceImpl")public class ProductServiceImpl implements ProductService {
    private static final Logger LOGGER = LoggerFactory.getLogger(ProductServiceImpl.class);
@Autowired    @Qualifier("ProductProcessingFactory")    ProcessingFactory<StatelessKnowledgeSession, Product> processingFactory;
@Override    public void runProductLogic() {
        LOGGER.debug("Running product logic");
Product medicalProduct = new Product(TypeEnum.MEDICAL);
Product electronicProduct = new Product(TypeEnum.ELECTRONIC);
StatelessKnowledgeSession statelessKnowledgeSession = processingFactory.createProcessingObject(medicalProduct);
LOGGER.debug("Running rules for products...");
statelessKnowledgeSession.execute(newArrayList(medicalProduct, electronicProduct));
LOGGER.debug("...finished running products.");
}

}
```
Now let's have a look on how the factory is implemented. We are using aliases in the applicationContext.xml
**ProductProcessingFactory.java  **
```java
package pl.grzejszczak.marcin.drools.springintegration.factory.drools;
import org.drools.runtime.StatelessKnowledgeSession;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.ApplicationContext;
import org.springframework.stereotype.Component;
import pl.grzejszczak.marcin.drools.springintegration.factory.ProcessingFactory;
import pl.grzejszczak.marcin.drools.springintegration.model.Product;
/** * Created with IntelliJ IDEA. * User: mgrzejszczak * Date: 14.01.13 */@Component("ProductProcessingFactory")public class ProductProcessingFactory implements ProcessingFactory<StatelessKnowledgeSession, Product> {
    @Autowired    private ApplicationContext applicationContext;
@Override    public StatelessKnowledgeSession createProcessingObject(Product inputObject) {
        return (StatelessKnowledgeSession)applicationContext.getBean(inputObject.getTypeEnum().getType() + "KSession");
}

}
```
Now how can we test if it works? I have two unit tests that prove it (they are not running in perfect isolation but they functionality of both approaches). Starting with the test for the manual rules creation test.
**NoRulesProductServiceImplTest.java **
```java
package pl.grzejszczak.marcin.drools.springintegration.service;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
import pl.grzejszczak.marcin.drools.springintegration.enums.TypeEnum;
import pl.grzejszczak.marcin.drools.springintegration.model.Product;
import pl.grzejszczak.marcin.drools.springintegration.utils.SomeUtil;
import java.util.List;
import static com.google.common.collect.Lists.newArrayList;
import static java.lang.String.format;
import static org.junit.Assert.assertEquals;
import static org.junit.Assert.assertNotSame;
/** * Created with IntelliJ IDEA. * User: mgrzejszczak * Date: 14.01.13 */@RunWith(SpringJUnit4ClassRunner.class)@ContextConfiguration(locations = {
"classpath:applicationContext.xml"
}
)public class NoRulesProductServiceImplTest {
    @Autowired    @Qualifier("ProductProcessingService")    private Processor<List<Product>> productProcessingService;
/**     * Test is not run in perfect isolation - the purpose is to show the outcome of processing without Drools     *     * @throws Exception     */    @Test    public void testRunProductLogic() throws Exception {
        Product medicalProduct = new Product(TypeEnum.MEDICAL);
Product electronicProduct = new Product(TypeEnum.ELECTRONIC);
String initialMedicalProductName = medicalProduct.getProductName();
String initialElectronicProduct = electronicProduct.getProductName();
System.out.println(format("Initial productName for Medical [%s]", medicalProduct.getProductName()));
System.out.println(format("Initial productName for Electronic [%s]", electronicProduct.getProductName()));
productProcessingService.process(newArrayList(medicalProduct, electronicProduct));
String finalMedicalProduct = medicalProduct.getProductName();
String finalElectronicProduct = electronicProduct.getProductName();
assertNotSame(finalMedicalProduct, initialMedicalProductName);
assertNotSame(finalElectronicProduct, initialElectronicProduct);
assertEquals(SomeUtil.replaceAWithB(initialMedicalProductName), finalMedicalProduct);
assertEquals(SomeUtil.replaceAWithB(initialElectronicProduct), finalElectronicProduct);
System.out.println(format("Final productName for Medical [%s]", medicalProduct.getProductName()));
System.out.println(format("Final productName for Electronic [%s]", electronicProduct.getProductName()));
}

}
```
And the unit test for the Drools approach.
**ProductServiceImplTest.java **
```java
package pl.grzejszczak.marcin.drools.springintegration.service;
import org.drools.runtime.StatelessKnowledgeSession;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
import pl.grzejszczak.marcin.drools.springintegration.enums.TypeEnum;
import pl.grzejszczak.marcin.drools.springintegration.factory.ProcessingFactory;
import pl.grzejszczak.marcin.drools.springintegration.model.Product;
import pl.grzejszczak.marcin.drools.springintegration.utils.SomeUtil;
import static com.google.common.collect.Lists.newArrayList;
import static java.lang.String.format;
import static org.junit.Assert.assertEquals;
import static org.junit.Assert.assertNotSame;
import static org.junit.Assert.assertTrue;
/** * Created with IntelliJ IDEA. * User: mgrzejszczak * Date: 14.01.13 */@RunWith(SpringJUnit4ClassRunner.class)@ContextConfiguration(locations = {
"classpath:applicationContext.xml"
}
)public class ProductServiceImplTest {
    @Autowired    @Qualifier("ProductProcessingFactory")    ProcessingFactory<StatelessKnowledgeSession, Product> processingFactory;
/**     * Test is not run in perfect isolation - the purpose is to show the outcome of processing with Drools     * @throws Exception     */    @Test    public void testRunProductLogic() throws Exception {
        Product medicalProduct = new Product(TypeEnum.MEDICAL);
Product electronicProduct = new Product(TypeEnum.ELECTRONIC);
String initialMedicalProductName = medicalProduct.getProductName();
String initialElectronicProduct = electronicProduct.getProductName();
System.out.println(format("Initial productName for Medical [%s]", medicalProduct.getProductName()));
System.out.println(format("Initial productName for Electronic [%s]", electronicProduct.getProductName()));
StatelessKnowledgeSession statelessKnowledgeSessionForMedical = processingFactory.createProcessingObject(medicalProduct);
StatelessKnowledgeSession statelessKnowledgeSessionForElectronic = processingFactory.createProcessingObject(electronicProduct);
assertTrue(statelessKnowledgeSessionForMedical == statelessKnowledgeSessionForElectronic);
System.out.println("References for stateless sessions are the same, executing rules...");
statelessKnowledgeSessionForMedical.execute(newArrayList(medicalProduct, electronicProduct));
String finalMedicalProduct = medicalProduct.getProductName();
String finalElectronicProduct = electronicProduct.getProductName();
assertNotSame(finalMedicalProduct, initialMedicalProductName);
assertNotSame(finalElectronicProduct, initialElectronicProduct);
assertEquals(SomeUtil.replaceAWithB(initialMedicalProductName), finalMedicalProduct);
assertEquals(SomeUtil.replaceAWithB(initialElectronicProduct), finalElectronicProduct);
System.out.println(format("Final productName for Medical [%s]", medicalProduct.getProductName()));
System.out.println(format("Final productName for Electronic [%s]", electronicProduct.getProductName()));
}

}
```
Now let's take a look at the logs - take a look that 'Executing some logic' took place 6 times for Drools since when you modify an object the rules are revalidated and rerun:
```java
org.springframework.context.support.ClassPathXmlApplicationContext:495 Refreshing org.springframework.context.support.ClassPathXmlApplicationContext@743399: startup date [Tue Jan 15 16:32:30 CET 2013];
root of context hierarchyorg.springframework.beans.factory.xml.XmlBeanDefinitionReader:315 Loading XML bean definitions from class path resource [applicationContext.xml]org.springframework.beans.factory.xml.XmlBeanDefinitionReader:315 Loading XML bean definitions from class path resource [drools-context.xml][main] org.springframework.beans.factory.support.DefaultListableBeanFactory:557 Pre-instantiating singletons in org.springframework.beans.factory.support.DefaultListableBeanFactory@3b1d04: defining beans [productsKBase,productsKSession,ProductProcessingFactory,NoRulesProcessingFactory,ProductServiceImpl,NoRulesProductServiceImpl,ProductProcessingService,org.springframework.context.annotation.internalConfigurationAnnotationProcessor,org.springframework.context.annotation.internalAutowiredAnnotationProcessor,org.springframework.context.annotation.internalRequiredAnnotationProcessor,org.springframework.context.annotation.internalCommonAnnotationProcessor,org.springframework.context.annotation.ConfigurationClassPostProcessor$ImportAwareBeanPostProcessor#0];
root of factory hierarchypl.grzejszczak.marcin.drools.springintegration.service.drools.ProductServiceImpl:32 Running product logicpl.grzejszczak.marcin.drools.springintegration.service.drools.ProductServiceImpl:36 Running rules for products...pl.grzejszczak.marcin.drools.springintegration.utils.SomeUtil:19 Executing some logicpl.grzejszczak.marcin.drools.springintegration.utils.SomeUtil:19 Executing some logicpl.grzejszczak.marcin.drools.springintegration.utils.SomeUtil:19 Executing some logicpl.grzejszczak.marcin.drools.springintegration.utils.SomeUtil:19 Executing some logicpl.grzejszczak.marcin.drools.springintegration.DroolsSpring:? ELECTRONIC rule applied, product name is now equal to [cccbbbbbb]pl.grzejszczak.marcin.drools.springintegration.utils.SomeUtil:19 Executing some logicpl.grzejszczak.marcin.drools.springintegration.utils.SomeUtil:19 Executing some logicpl.grzejszczak.marcin.drools.springintegration.DroolsSpring:? MEDICAL rule applied, product name is now equal to [bbbbbbccc]pl.grzejszczak.marcin.drools.springintegration.service.drools.ProductServiceImpl:38 ...finished running products.pl.grzejszczak.marcin.drools.springintegration.service.nondrools.NoRulesProductServiceImpl:33 Running product logic without Droolspl.grzejszczak.marcin.drools.springintegration.service.nondrools.NoRulesProductServiceImpl:36 Running rules for products...pl.grzejszczak.marcin.drools.springintegration.utils.SomeUtil:19 Executing some logicpl.grzejszczak.marcin.drools.springintegration.service.nondrools.MedicalProductProcessingService:26 Running processing for Medical Productpl.grzejszczak.marcin.drools.springintegration.utils.SomeUtil:19 Executing some logicpl.grzejszczak.marcin.drools.springintegration.service.nondrools.MedicalProductProcessingService:28 MEDICAL rule applied without Drools, product name is now equal to [bbbbbbccc]pl.grzejszczak.marcin.drools.springintegration.utils.SomeUtil:19 Executing some logicpl.grzejszczak.marcin.drools.springintegration.service.nondrools.ElectronicProductProcessingService:26 Running processing for Electronic Productpl.grzejszczak.marcin.drools.springintegration.utils.SomeUtil:19 Executing some logicpl.grzejszczak.marcin.drools.springintegration.service.nondrools.ElectronicProductProcessingService:28 ELECTRONIC rule applied without Drools, product name is now equal to [cccbbbbbb]pl.grzejszczak.marcin.drools.springintegration.service.nondrools.NoRulesProductServiceImpl:38 ...finished running products.
```
Viola! That's how you can write some rules with Drools that can save plenty of time and effort as far as business logic is concerned. You can find the sources here at my
[BitBucket repository](https://bitbucket.org/gregorin1987/too-much-coding/src/3d8a2ef67b5b/Drools/Spring%20integration?at=default)
.
