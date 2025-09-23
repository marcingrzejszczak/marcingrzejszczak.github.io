---
title: "Spring Beanpostprocessor For Specified"
summary: "I was recently having a discussion how can one use a BeanPostProcessor to execute some logic for a specified class."
date: 2012-10-22

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
I was recently having a discussion how can one use a BeanPostProcessor to execute some logic for a specified class.
Looking at the Javadoc for the BeanPostProcessor one can read that:
> Factory hook that allows for custom modification of new bean instances, e.g. checking for marker interfaces or wrapping them with proxies.
So how can one create in an easy way a BeanPostProcessor for a precise type without creating a cascade of ifs or instance ofs? This is my concept of solving this problem - perhaps you know an easier one? :)
**SomeService.java**
```java
package pl.grzejszczak.marcin.postprocessor;
public interface SomeService {
 void methodA();
void methodB();
}
```
**SomeServiceImpl.java**
```java
package pl.grzejszczak.marcin.postprocessor;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
public class SomeServiceImpl implements SomeService {
 private static final Logger LOGGER = LoggerFactory.getLogger(SomeServiceImpl.class);
public SomeServiceImpl() {
  LOGGER.debug("SomeServiceImpl - I'm created!");
}
 private void afterInit() {
  LOGGER.debug("SomeServiceImpl - After init!");
}
 private void destroyMethod() {
  LOGGER.debug("SomeServiceImpl - Destroy Method!");
}
 @Override public void methodA() {
  LOGGER.debug("SomeServiceImpl - Method A executed");
}
 @Override public void methodB() {
  LOGGER.debug("SomeServiceImpl - Method B executed");
}

}
```
**SomeOtherService.java**
```java
package pl.grzejszczak.marcin.postprocessor;
public interface SomeOtherService {
 void methodC();
void methodD();
}
```
**SomeOtherServiceImpl.java**
```java
package pl.grzejszczak.marcin.postprocessor;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
public class SomeOtherServiceImpl implements SomeOtherService {
 private static final Logger LOGGER = LoggerFactory.getLogger(SomeOtherServiceImpl.class);
public SomeOtherServiceImpl() {
  LOGGER.debug("SomeOtherServiceImpl - I'm created!");
}
 private void afterInit() {
  LOGGER.debug("SomeOtherServiceImpl - After init!");
}
 private void destroyMethod() {
  LOGGER.debug("SomeOtherServiceImpl - Destroy Method!");
}
 @Override public void methodC() {
  LOGGER.debug("SomeOtherServiceImpl - Method C executed");
}
 @Override public void methodD() {
  LOGGER.debug("SomeOtherServiceImpl - Method D executed");
}

}
```
**AbstractBeanPostProcessor.java**
```java
package pl.grzejszczak.marcin.postprocessor;
import org.springframework.beans.BeansException;
import org.springframework.beans.factory.config.BeanPostProcessor;
public abstract class AbstractBeanPostProcessor<T> implements BeanPostProcessor {
 private Class<T> clazz;
public AbstractBeanPostProcessor(Class<T> clazz) {
  this.clazz = clazz;
}
 @Override public Object postProcessAfterInitialization(Object bean, String name) throws BeansException {
  checkConditions();
if (clazz.isAssignableFrom(bean.getClass())) {
   doAfter();
}
  return bean;
}
 @Override public Object postProcessBeforeInitialization(Object bean, String name) throws BeansException {
  checkConditions();
if (clazz.isAssignableFrom(bean.getClass())) {
   doBefore();
}
  return bean;
}
 private void checkConditions() {
  if (clazz == null) {
   throw new NullArgumentException("Provide the interface for the post processor");
}

}
 public abstract void doBefore();
public abstract void doAfter();
}
```
**SomeServicePostProcessor.java**
```java
package pl.grzejszczak.marcin.postprocessor;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Component;
@Componentpublic class SomeServicePostProcessor extends AbstractBeanPostProcessor<SomeService> {
 private static final Logger LOGGER = LoggerFactory.getLogger(SomeServicePostProcessor.class);
public SomeServicePostProcessor() {
  super(SomeService.class);
}
 @Override public void doBefore() {
  LOGGER.info("BEFORE it's init method has been executed but AFTER SomeServiceImpl has been instantiated I would like to do sth...");
}
 @Override public void doAfter() {
  LOGGER.info("AFTER SomeServiceImpl has executed its init method I would like to do sth more...");
}

}
```
**SpringMain.java**
****
```java
package pl.grzejszczak.marcin.postprocessor;
import org.springframework.context.ConfigurableApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
public class SpringMain {
 public static void main(String[] args) {
  ConfigurableApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
SomeService someService = context.getBean(SomeService.class);
someService.methodA();
someService.methodB();
SomeOtherService someOtherService = context.getBean(SomeOtherService.class);
someOtherService.methodC();
someOtherService.methodD();
context.close();
}

}
```
**ApplicationContext.xml**
```
<bean class="pl.grzejszczak.marcin.postprocessor.SomeServiceImpl" destroy-method="destroyMethod" init-method="afterInit"/> <bean class="pl.grzejszczak.marcin.postprocessor.SomeOtherServiceImpl" destroy-method="destroyMethod" init-method="afterInit"/> <bean class="pl.grzejszczak.marcin.postprocessor.SomeServicePostProcessor"/>
```
**Logs**
```java
2012-10-23 00:20:38,863 INFO  [main] org.springframework.context.support.ClassPathXmlApplicationContext:495 Refreshing org.springframework.context.support.ClassPathXmlApplicationContext@28d11816: startup date [Tue Oct 23 00:20:38 CEST 2012];
root of context hierarchy2012-10-23 00:20:38,956 INFO  [main] org.springframework.beans.factory.xml.XmlBeanDefinitionReader:315 Loading XML bean definitions from class path resource [applicationContext.xml]2012-10-23 00:20:39,213 INFO  [main] org.springframework.beans.factory.support.DefaultListableBeanFactory:557 Pre-instantiating singletons in org.springframework.beans.factory.support.DefaultListableBeanFactory@6bb4469: defining beans [pl.grzejszczak.marcin.postprocessor.SomeServiceImpl#0,pl.grzejszczak.marcin.postprocessor.SomeOtherServiceImpl#0,pl.grzejszczak.marcin.postprocessor.SomeServicePostProcessor#0];
root of factory hierarchy2012-10-23 00:20:39,214 DEBUG [main] pl.grzejszczak.marcin.postprocessor.SomeServiceImpl:10 SomeServiceImpl - I'm created!2012-10-23 00:20:39,215 INFO  [main] pl.grzejszczak.marcin.postprocessor.SomeServicePostProcessor:18 BEFORE its init method has been executed but AFTER SomeServiceImpl has been instantiated I would like to do sth...2012-10-23 00:20:39,216 DEBUG [main] pl.grzejszczak.marcin.postprocessor.SomeServiceImpl:14 SomeServiceImpl - After init!2012-10-23 00:20:39,216 INFO  [main] pl.grzejszczak.marcin.postprocessor.SomeServicePostProcessor:23 AFTER SomeServiceImpl has executed its init method I would like to do sth more...2012-10-23 00:20:39,220 DEBUG [main] pl.grzejszczak.marcin.postprocessor.SomeOtherServiceImpl:10 SomeOtherServiceImpl - I'm created!2012-10-23 00:20:39,221 DEBUG [main] pl.grzejszczak.marcin.postprocessor.SomeOtherServiceImpl:14 SomeOtherServiceImpl - After init!2012-10-23 00:20:39,225 DEBUG [main] pl.grzejszczak.marcin.postprocessor.SomeServiceImpl:23 SomeServiceImpl - Method A executed2012-10-23 00:20:39,241 DEBUG [main] pl.grzejszczak.marcin.postprocessor.SomeServiceImpl:28 SomeServiceImpl - Method B executed2012-10-23 00:20:39,242 DEBUG [main] pl.grzejszczak.marcin.postprocessor.SomeOtherServiceImpl:23 SomeOtherServiceImpl - Method C executed2012-10-23 00:20:39,242 DEBUG [main] pl.grzejszczak.marcin.postprocessor.SomeOtherServiceImpl:28 SomeOtherServiceImpl - Method D executed2012-10-23 00:20:39,242 INFO  [main] org.springframework.context.support.ClassPathXmlApplicationContext:1020 Closing org.springframework.context.support.ClassPathXmlApplicationContext@28d11816: startup date [Tue Oct 23 00:20:38 CEST 2012];
root of context hierarchy2012-10-23 00:20:39,243 INFO  [main] org.springframework.beans.factory.support.DefaultListableBeanFactory:433 Destroying singletons in org.springframework.beans.factory.support.DefaultListableBeanFactory@6bb4469: defining beans [pl.grzejszczak.marcin.postprocessor.SomeServiceImpl#0,pl.grzejszczak.marcin.postprocessor.SomeOtherServiceImpl#0,pl.grzejszczak.marcin.postprocessor.SomeServicePostProcessor#0];
root of factory hierarchy2012-10-23 00:20:39,244 DEBUG [main] pl.grzejszczak.marcin.postprocessor.SomeOtherServiceImpl:18 SomeOtherServiceImpl - Destroy Method!2012-10-23 00:20:39,245 DEBUG [main] pl.grzejszczak.marcin.postprocessor.SomeServiceImpl:18 SomeServiceImpl - Destroy Method!
```
As you can see it is quite easy, using generics and the BeanPostProcessor, to specify certain behavors for a given type (generics and constructor of SomeServiceImpl) or a group of types of classes (generics and constructor of SomeService).
