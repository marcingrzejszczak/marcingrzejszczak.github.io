---
title: "Apache Camel With Spring Routing With"
summary: "Sorry for not having posted anything in some time but I had plenty of work. Anyway today I will continue the example with JMS that I've shown you some time ago."
date: 2012-11-22

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
Sorry for not having posted anything in some time but I had plenty of work. Anyway today I will continue the example with JMS that I've shown you some time ago.
The idea of the previous example was to simplify the work that we had to do in a manual way - we've sed JmsTemplate and Spring listener containers. The routing as such unfortunately was still done by us. In order to facilitate this process we can use
[Apache Camel](https://camel.apache.org/)
.
The following example bases on the one that we've seen in this post
[Spring JMS, message automatic conversion, JMS template](https://toomuchcoding.com/2012/11/spring-jms-message-automatic-conversion.html)
but with slight modifications:
**CamelRouter.java**
```java
package pl.grzejszczak.marcin.camel;
import org.apache.camel.spring.Main;
public class CamelRouter {
 /**  * @param args  * @throws Exception  */ public static void main(String[] args) throws Exception {
  Main main = new Main();
main.setApplicationContextUri("/camel/camelContext.xml");
main.run(args);
}

}
```
What we can see here is the usage of the Camel's
Main
class which you can reuse to more easily boot up Camel and keep it running until the JVM terminate.
Then we have a new file
camelContext.xml
in which we have the Camel context in the Spring configuration file.
**camelContext.xml**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="https://www.springframework.org/schema/beans" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="https://camel.apache.org/schema/spring https://camel.apache.org/schema/spring/camel-spring.xsd https://www.springframework.org/schema/beans https://www.springframework.org/schema/beans/spring-beans-3.0.xsd ">
  <import resource="classpath:/camel/jmsApplicationContext.xml" />
  <camel:camelContext id="camel" xmlns:camel="https://camel.apache.org/schema/spring">
    <camel:dataFormats>
      <camel:jaxb id="jaxb" prettyPrint="true" contextPath="pl.grzejszczak.marcin.camel.jaxb.generated" />
    </camel:dataFormats>
    <camel:route>
      <camel:from uri="activemq:topic:Initial.Topic" />
      <camel:unmarshal ref="jaxb" />
      <camel:bean ref="enrichingService" />
      <camel:marshal ref="jaxb" />
      <camel:to uri="activemq:topic:Routed.Topic" />
    </camel:route>
  </camel:camelContext>
</beans>
```
We are defining in this file in order to create a Camel Route - from the activemq topic called
*Initial.Topic*
to the one called
*Routed.Topic*
. In the meantime we are doing some unmarshalling and marshalling by means of Jaxb.
In the jmsApplicationContext we no longer define the sender to the final topic:
*Routed.Topic*
.
**jmsApplicationContext.java**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="https://www.springframework.org/schema/beans" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:context="https://www.springframework.org/schema/context" xmlns:jms="https://www.springframework.org/schema/jms" xmlns:oxm="https://www.springframework.org/schema/oxm" xsi:schemaLocation="https://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context-3.0.xsd https://www.springframework.org/schema/beans https://www.springframework.org/schema/beans/spring-beans-3.0.xsd https://www.springframework.org/schema/jms https://www.springframework.org/schema/jms/spring-jms-3.0.xsd https://www.springframework.org/schema/oxm https://www.springframework.org/schema/oxm/spring-oxm-3.0.xsd">
  <!-- Spring configuration based on annotations -->
  <context:annotation-config />
  <!-- Show Spring where to search for the beans (in which packages) -->
  <context:component-scan base-package="pl.grzejszczak.marcin.camel" />
  <!-- Show Spring where to search for the properties files -->
  <context:property-placeholder location="classpath:/camel/jms.properties" />
  <!-- The ActiveMQ connection factory with specification of the server URL -->
  <bean id="activeMQConnectionFactory" class="org.apache.activemq.ActiveMQConnectionFactory">
    <property name="brokerURL" value="tcp://localhost:61616" />
  </bean>
  <!-- Spring's jms connection factory -->
  <bean id="cachingConnectionFactory" class="org.springframework.jms.connection.CachingConnectionFactory">
    <property name="targetConnectionFactory" ref="activeMQConnectionFactory" />
    <property name="sessionCacheSize" value="10" />
  </bean>
  <!-- The name of the queue from which we will take the messages -->
  <bean id="origin" class="org.apache.activemq.command.ActiveMQTopic">
    <constructor-arg value="${jms.origin}" />
  </bean>
  <!-- The name of the queue to which we will route the messages -->
  <bean id="destination" class="org.apache.activemq.command.ActiveMQTopic">
    <constructor-arg value="${jms.destination}" />
  </bean>
  <!-- Configuration of the JmsTemplate together with the connection factory and the message converter -->
  <bean id="producerTemplate" class="org.springframework.jms.core.JmsTemplate">
    <property name="connectionFactory" ref="cachingConnectionFactory" />
    <property name="messageConverter" ref="oxmMessageConverter" />
  </bean>
  <!-- Custom message sender sending messages to the initial queue -->
  <bean id="originPlayerSender" class="pl.grzejszczak.marcin.camel.manual.jms.PlayerDetailsSenderImpl">
    <property name="destination" ref="origin" />
  </bean>
  <!-- Custom message listener - listens to the destination queue -->
  <bean id="destinationListenerImpl" class="pl.grzejszczak.marcin.camel.manual.jms.FinalListenerImpl"/>
  <!-- Spring's jms message listener container - specified the connection factory, the queue to be listened to and the component that listens to the queue -->
  <bean id="jmsDestinationContainer" class="org.springframework.jms.listener.DefaultMessageListenerContainer">
    <property name="connectionFactory" ref="cachingConnectionFactory" />
    <property name="destination" ref="destination" />
    <property name="messageListener" ref="destinationListenerImpl" />
  </bean>
  <!-- Message converter - automatically marshalls and unmarshalls messages using the provided marshaller / unmarshaller-->
  <bean id="oxmMessageConverter" class="org.springframework.jms.support.converter.MarshallingMessageConverter">
    <property name="marshaller" ref="marshaller" />
    <property name="unmarshaller" ref="marshaller" />
  </bean>
  <bean id="enrichingService" class="pl.grzejszczak.marcin.camel.service.EnrichingServiceImpl"/>
  <!-- Spring's JAXB implementation of marshaller - provided a class the JAXB generated class -->
  <oxm:jaxb2-marshaller id="marshaller">
    <oxm:class-to-be-bound name="pl.grzejszczak.marcin.camel.jaxb.generated.PlayerDetails" />
  </oxm:jaxb2-marshaller>
</beans>
```
Once we have already initialized our camel context, what we need to do is to send a message to the
*Initial.Topic*
. We are doing it by means of our modified
ActiveMQRouter
class.
**ActiveMQRouter.java**
```java
package pl.grzejszczak.marcin.camel.manual;
import java.io.File;
import java.util.Scanner;
import javax.jms.JMSException;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import org.springframework.core.io.ClassPathResource;
import org.springframework.core.io.Resource;
import pl.grzejszczak.marcin.camel.jaxb.PlayerDetailsConverter;
import pl.grzejszczak.marcin.camel.jaxb.generated.PlayerDetails;
import pl.grzejszczak.marcin.camel.manual.jms.FinalListenerImpl;
import pl.grzejszczak.marcin.camel.manual.jms.Sender;
public class ActiveMQRouter {
 /**  * @param args  * @throws JMSException  */ public static void main(String[] args) throws Exception {
  ApplicationContext context = new ClassPathXmlApplicationContext("/camel/jmsApplicationContext.xml");
@SuppressWarnings("unchecked")  Sender<PlayerDetails> sender = (Sender<PlayerDetails>) context.getBean("originPlayerSender");
Resource resource = new ClassPathResource("/camel/RobertLewandowski.xml");
Scanner scanner = new Scanner(new File(resource.getURI())).useDelimiter("\\Z");
String contents = scanner.next();
PlayerDetailsConverter converter = context.getBean(PlayerDetailsConverter.class);
FinalListenerImpl listener = (FinalListenerImpl) context.getBean("finalListenerImpl");
sender.sendMessage(converter.unmarshal(contents));
}

}
```
The class is reading the file and sending it to the initial topic. We also initialize a FinalListenerImpl - a class that will listen to the messages coming to the final topic - to prove that everything is working correctly.
That's it! Now let's check out the logs. Logs of CamelRouter:
```java
2012-11-22 22:51:39,429 INFO  [main] org.apache.camel.main.MainSupport:300 Apache Camel 2.9.2 starting2012-11-22 22:51:40,028 INFO  [main] org.springframework.context.support.ClassPathXmlApplicationContext:495 Refreshing org.springframework.context.support.ClassPathXmlApplicationContext@4c5e176f: startup date [Thu Nov 22 22:51:40 CET 2012];
root of context hierarchy2012-11-22 22:51:40,213 INFO  [main] org.springframework.beans.factory.xml.XmlBeanDefinitionReader:315 Loading XML bean definitions from class path resource [camel/camelContext.xml]2012-11-22 22:51:40,746 INFO  [main] org.springframework.beans.factory.xml.XmlBeanDefinitionReader:315 Loading XML bean definitions from class path resource [camel/jmsApplicationContext.xml]2012-11-22 22:51:41,120 INFO  [main] org.springframework.context.annotation.ClassPathBeanDefinitionScanner:210 JSR-330 'javax.inject.Named' annotation found and supported for component scanning2012-11-22 22:51:43,219 INFO  [main] org.springframework.beans.factory.config.PropertyPlaceholderConfigurer:177 Loading properties file from class path resource [camel/jms.properties]2012-11-22 22:51:43,233 INFO  [main] org.springframework.beans.factory.annotation.AutowiredAnnotationBeanPostProcessor:139 JSR-330 'javax.inject.Inject' annotation found and supported for autowiring2012-11-22 22:51:43,274 INFO  [main] org.springframework.beans.factory.support.DefaultListableBeanFactory:557 Pre-instantiating singletons in org.springframework.beans.factory.support.DefaultListableBeanFactory@19d03a4e: defining beans [org.springframework.context.annotation.internalConfigurationAnnotationProcessor,org.springframework.context.annotation.internalAutowiredAnnotationProcessor,org.springframework.context.annotation.internalRequiredAnnotationProcessor,org.springframework.context.annotation.internalCommonAnnotationProcessor,AgeEnricher,ClubEnricher,PlayerDetailsConverter,finalListenerImpl,playerDetailsSenderImpl,org.springframework.beans.factory.config.PropertyPlaceholderConfigurer#0,activeMQConnectionFactory,cachingConnectionFactory,origin,destination,producerTemplate,originPlayerSender,destinationListenerImpl,jmsDestinationContainer,oxmMessageConverter,enrichingService,marshaller,template,consumerTemplate,camel:beanPostProcessor,camel,org.springframework.context.annotation.ConfigurationClassPostProcessor$ImportAwareBeanPostProcessor#0];
root of factory hierarchy2012-11-22 22:51:43,424 INFO  [main] org.springframework.oxm.jaxb.Jaxb2Marshaller:436 Creating JAXBContext with classes to be bound [class pl.grzejszczak.marcin.camel.jaxb.generated.PlayerDetails]2012-11-22 22:51:44,521 INFO  [main] org.springframework.context.support.DefaultLifecycleProcessor:334 Starting beans in phase 21474836472012-11-22 22:51:45,061 INFO  [main] org.springframework.jms.connection.CachingConnectionFactory:291 Established shared JMS Connection: ActiveMQConnection {
id=ID:marcin-SR700-47684-1353621104666-1:1,clientId=null,started=false
}
2012-11-22 22:51:45,608 INFO  [main] org.apache.camel.spring.SpringCamelContext:1374 Apache Camel 2.9.2 (CamelContext: camel) is starting2012-11-22 22:51:45,611 INFO  [main] org.apache.camel.management.ManagementStrategyFactory:38 JMX enabled. Using ManagedManagementStrategy.2012-11-22 22:51:45,850 INFO  [main] org.apache.camel.management.DefaultManagementLifecycleStrategy:790 StatisticsLevel at All so enabling load performance statistics2012-11-22 22:51:45,961 INFO  [main] org.apache.camel.impl.converter.AnnotationTypeConverterLoader:119 Found 3 packages with 15 @Converter classes to load2012-11-22 22:51:45,995 INFO  [main] org.apache.camel.impl.converter.DefaultTypeConverter:405 Loaded 170 core type converters (total 170 type converters)2012-11-22 22:51:46,002 INFO  [main] org.apache.camel.impl.converter.AnnotationTypeConverterLoader:109 Loaded 2 @Converter classes2012-11-22 22:51:46,023 INFO  [main] org.apache.camel.impl.converter.AnnotationTypeConverterLoader:119 Found 1 packages with 1 @Converter classes to load2012-11-22 22:51:46,024 WARN  [main] org.apache.camel.impl.converter.DefaultTypeConverter:257 Overriding type converter from: StaticMethodTypeConverter: public static org.apache.activemq.command.ActiveMQDestination org.apache.camel.component.activemq.ActiveMQConverter.toDestination(java.lang.String) to: StaticMethodTypeConverter: public static org.apache.activemq.command.ActiveMQDestination org.apache.activemq.camel.converter.ActiveMQConverter.toDestination(java.lang.String)2012-11-22 22:51:46,043 INFO  [main] org.apache.camel.impl.converter.DefaultTypeConverter:431 Loaded additional 3 type converters (total 173 type converters) in 0.041 seconds2012-11-22 22:51:46,360 INFO  [main] org.apache.camel.converter.jaxb.JaxbDataFormat:277 Creating JAXBContext with contextPath: pl.grzejszczak.marcin.camel.jaxb.generated and ApplicationContextClassLoader: sun.misc.Launcher$AppClassLoader@35a168692012-11-22 22:51:46,500 INFO  [main] org.apache.camel.spring.SpringCamelContext:1980 Route: route1 started and consuming from: Endpoint[activemq://topic:Initial.Topic]2012-11-22 22:51:46,509 INFO  [main] org.apache.camel.spring.SpringCamelContext:1409 Total 1 routes, of which 1 is started.2012-11-22 22:51:46,510 INFO  [main] org.apache.camel.spring.SpringCamelContext:1410 Apache Camel 2.9.2 (CamelContext: camel) started in 0.901 seconds2012-11-22 22:51:46,519 INFO  [main] org.springframework.context.support.DefaultLifecycleProcessor:334 Starting beans in phase 21474836472012-11-22 22:52:08,375 DEBUG [Camel (camel) thread #1 - JmsConsumer[Initial.Topic]] pl.grzejszczak.marcin.camel.service.EnrichingServiceImpl:21 Enriching player details2012-11-22 22:52:08,377 DEBUG [Camel (camel) thread #1 - JmsConsumer[Initial.Topic]] pl.grzejszczak.marcin.camel.enricher.AgeEnricher:17 Enriching player [Lewandowski] with age data2012-11-22 22:52:10,379 DEBUG [Camel (camel) thread #1 - JmsConsumer[Initial.Topic]] pl.grzejszczak.marcin.camel.enricher.ClubEnricher:16 Enriching player [Lewandowski] with club data2012-11-22 22:52:12,462 DEBUG [jmsDestinationContainer-1] pl.grzejszczak.marcin.camel.manual.jms.FinalListenerImpl:35 Message already enriched! Shutting down the system
```
We can see that the Camel Context has been initialized and then the bean that we have created in the
jmsApplicationContext.xml
that is listening to the final destination is acknowledging that the message has been enriched properly.
What about the
ActiveMQRouter.java
logs?
```java
2012-11-22 22:52:06,077 INFO  [main] org.springframework.context.support.ClassPathXmlApplicationContext:495 Refreshing org.springframework.context.support.ClassPathXmlApplicationContext@43462851: startup date [Thu Nov 22 22:52:06 CET 2012];
root of context hierarchy2012-11-22 22:52:06,153 INFO  [main] org.springframework.beans.factory.xml.XmlBeanDefinitionReader:315 Loading XML bean definitions from class path resource [camel/jmsApplicationContext.xml]2012-11-22 22:52:06,417 INFO  [main] org.springframework.context.annotation.ClassPathBeanDefinitionScanner:210 JSR-330 'javax.inject.Named' annotation found and supported for component scanning2012-11-22 22:52:06,721 INFO  [main] org.springframework.beans.factory.config.PropertyPlaceholderConfigurer:177 Loading properties file from class path resource [camel/jms.properties]2012-11-22 22:52:06,733 INFO  [main] org.springframework.beans.factory.annotation.AutowiredAnnotationBeanPostProcessor:139 JSR-330 'javax.inject.Inject' annotation found and supported for autowiring2012-11-22 22:52:06,758 INFO  [main] org.springframework.beans.factory.support.DefaultListableBeanFactory:557 Pre-instantiating singletons in org.springframework.beans.factory.support.DefaultListableBeanFactory@362f0d54: defining beans [org.springframework.context.annotation.internalConfigurationAnnotationProcessor,org.springframework.context.annotation.internalAutowiredAnnotationProcessor,org.springframework.context.annotation.internalRequiredAnnotationProcessor,org.springframework.context.annotation.internalCommonAnnotationProcessor,AgeEnricher,ClubEnricher,PlayerDetailsConverter,finalListenerImpl,playerDetailsSenderImpl,org.springframework.beans.factory.config.PropertyPlaceholderConfigurer#0,activeMQConnectionFactory,cachingConnectionFactory,origin,destination,producerTemplate,originPlayerSender,destinationListenerImpl,jmsDestinationContainer,oxmMessageConverter,enrichingService,marshaller,org.springframework.context.annotation.ConfigurationClassPostProcessor$ImportAwareBeanPostProcessor#0];
root of factory hierarchy2012-11-22 22:52:07,224 INFO  [main] org.springframework.oxm.jaxb.Jaxb2Marshaller:436 Creating JAXBContext with classes to be bound [class pl.grzejszczak.marcin.camel.jaxb.generated.PlayerDetails]2012-11-22 22:52:07,628 INFO  [main] org.springframework.context.support.DefaultLifecycleProcessor:334 Starting beans in phase 21474836472012-11-22 22:52:07,883 INFO  [main] org.springframework.jms.connection.CachingConnectionFactory:291 Established shared JMS Connection: ActiveMQConnection {
id=ID:marcin-SR700-53586-1353621127755-1:1,clientId=null,started=false
}
2012-11-22 22:52:08,093 DEBUG [main] pl.grzejszczak.marcin.camel.manual.jms.PlayerDetailsSenderImpl:26 Sending [pl.grzejszczak.marcin.camel.jaxb.generated.PlayerDetails@3ea86d12] to topic [topic://Initial.Topic]2012-11-22 22:52:12,463 DEBUG [jmsDestinationContainer-1] pl.grzejszczak.marcin.camel.manual.jms.FinalListenerImpl:35 Message already enriched! Shutting down the system
```
First we see that our spring Context has been initialized and then we see that a message has been sent to the
*Initial.Topic*
. At the end of the processing we can see that the listener is confirming that the message has been properly enriched - so all the Camel work has been done in a proper way.
This example is showing how easy and simple it can be to create a routing / enriching service by means of Spring and Camel (integrated with Spring).
The sources are available at
[Too Much Coding's repository at bitbucket](https://bitbucket.org/gregorin1987/too-much-coding/src/26b70bca3b44e37c20c627e0efe4644d28f8d468/Camel%20and%20Spring?at=default)
.
