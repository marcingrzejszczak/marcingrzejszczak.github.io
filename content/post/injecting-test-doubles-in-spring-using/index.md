---
title: "Injecting Test Doubles In Spring Using"
summary: "I'm pretty sure that if you have ever used Spring and are familliar with unit testing, you have encountered a problem related to injecting mocks / spies (Test Doubles) in the Spring's application context which you…"
date: 2013-08-09

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
I'm pretty sure that if you have ever used Spring and are familliar with unit testing, you have encountered a problem related to injecting mocks / spies (Test Doubles) in the Spring's application context which you wouldn't want to modify. This article presents an approach how to solve this issue using Spring's components.
[]()
## Project structure
Let's start with the project structure:
[![](https://1.bp.blogspot.com/-24owAXPWDec/UgS1olJ038I/AAAAAAAABlA/O90115z1yIk/s320/Project+structure.jpg)](https://1.bp.blogspot.com/-24owAXPWDec/UgS1olJ038I/AAAAAAAABlA/O90115z1yIk/s1600/Project+structure.jpg)
As usual to present a problem I'm trying to show a very simple project structure. The approach that I'm about to show could show more benefits if I made the problem more extensive as we had in our project:
- we had dozens of interfaces and implementations autowired to lists
- we wanted to perform some functional tests basing on the existing Spring application context
- we wanted to verify that for certain input conditions some specific implementation would have their methods executed
- we wanted to stub database access.
In this example we have a
PlayerService
that gets a
Player
using a
PlayerWebService
. We have an applicationContext that simply defines packages for autowiring:
**applicationContext.xml**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="https://www.springframework.org/schema/beans" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:context="https://www.springframework.org/schema/context" xsi:schemaLocation="https://www.springframework.org/schema/beans https://www.springframework.org/schema/beans/spring-beans-3.0.xsd https://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context-3.0.xsd">
  <context:component-scan base-package="com.blogspot.toomuchcoding"/>
</beans>
```
Then we have our very simple model:
**Player.java**
```java
package com.blogspot.toomuchcoding.model;
import java.math.BigDecimal;
/** * User: mgrzejszczak * Date: 08.08.13 * Time: 14:38 */public final class Player {
    private final String playerName;
private final BigDecimal playerValue;
public Player(final String playerName, final BigDecimal playerValue) {
        this.playerName = playerName;
this.playerValue = playerValue;
}
    public String getPlayerName() {
        return playerName;
}
    public BigDecimal getPlayerValue() {
        return playerValue;
}

}
```
the implementation of the
PlayerService
that uses
PlayerWebService
to retrieve data regarding the
Player
:
**PlayerServiceImpl.java**
```java
package com.blogspot.toomuchcoding.service;
import com.blogspot.toomuchcoding.model.Player;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
/** * User: mgrzejszczak * Date: 08.06.13 * Time: 19:02 */@Servicepublic class PlayerServiceImpl implements PlayerService {
    private static final Logger LOGGER = LoggerFactory.getLogger(PlayerServiceImpl.class);
@Autowired    private PlayerWebService playerWebService;
@Override    public Player getPlayerByName(String playerName) {
        LOGGER.debug(String.format("Logging the player web service name [%s]", playerWebService.getWebServiceName()));
return playerWebService.getPlayerByName(playerName);
}
    public PlayerWebService getPlayerWebService() {
        return playerWebService;
}
    public void setPlayerWebService(PlayerWebService playerWebService) {
        this.playerWebService = playerWebService;
}

}
```
the implementation of the PlayerWebService that is a provider of data (in this scenario we are simulating awaiting for response):
**PlayerWebServiceImpl.java**
```java
package com.blogspot.toomuchcoding.service;
import com.blogspot.toomuchcoding.model.Player;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Service;
import java.math.BigDecimal;
/** * User: mgrzejszczak * Date: 08.08.13 * Time: 14:48 */@Servicepublic class PlayerWebServiceImpl implements PlayerWebService {
    private static final Logger LOGGER = LoggerFactory.getLogger(PlayerWebServiceImpl.class);
public static final String WEB_SERVICE_NAME = "SuperPlayerWebService";
public static final String SAMPLE_PLAYER_VALUE = "1000";
@Override    public String getWebServiceName() {
        return WEB_SERVICE_NAME;
}
    @Override    public Player getPlayerByName(String name) {
        try {
            LOGGER.debug("Simulating awaiting time for a response from a web service");
Thread.sleep(5000);
}
 catch (InterruptedException e) {
            LOGGER.error(String.format("[%s] occurred while trying to make the thread sleep", e));
}
        return new Player(name, new BigDecimal(SAMPLE_PLAYER_VALUE));
}

}
```
Perhaps the project structure and the methods are not one of the most brilliant you have ever seen but I wanted to keep it simple to present the problem ;)
## The problem
So what actually is the problem? Let us assume that we want our autowired
PlayerWebServiceImpl
to be a Spy that we can verify. What is more you don't want to actually change anything in the
applicationContext.xml
- you want to use the current version of the Spring context.
With mocks it's easier since you can define in your XML file (using Mockito factory method) your bean as a mock to override the original implementation just like this:
```xml
<bean id="playerWebServiceImpl" class="org.mockito.Mockito" factory-method="mock">
  <constructor-arg value="com.blogspot.toomuchcoding.service.PlayerWebServiceImpl"/>
</bean>
```
What about the Spy? It's more problematic since in order to create a Spy you need an already existing object of the given type. In our example we have some autowiring going on so we would have to first create a spring bean of the
PlayerWebService
type (Spring would have to wire all its dependencies) and then wrap it around with
Mockito.spy(...)
and only then would it have to be wired somewhere else... It's getting very complicatied doesn't it?
## The solution
You can see that the problem is not that trivial to be solved. An easy way to fix it however is to use native Spring mechanisms - BeanPostProcessors. You can check my article about how to create a
[Spring BeanPostProcessor for a specified type](https://toomuchcoding.com/2012/10/spring-beanpostprocessor-for-specified.html)
- we'll be using it in this example.
Let's start with checking the test class:
**PlayerServiceImplTest.java**
```java
package com.blogspot.toomuchcoding.service;
import com.blogspot.toomuchcoding.model.Player;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
import java.math.BigDecimal;
import static org.hamcrest.CoreMatchers.is;
import static org.junit.Assert.assertThat;
import static org.mockito.BDDMockito.doReturn;
import static org.mockito.Mockito.verify;
/** * User: mgrzejszczak * Date: 08.06.13 * Time: 19:26 */@RunWith(SpringJUnit4ClassRunner.class)@ContextConfiguration("classpath:testApplicationContext.xml")public class PlayerServiceImplTest {
    public static final String PLAYER_NAME = "Lewandowski";
public static final BigDecimal PLAYER_VALUE = new BigDecimal("35000000");
@Autowired    PlayerWebService playerWebServiceSpy;
@Autowired    PlayerService objectUnderTest;
@Test    public void shouldReturnAPlayerFromPlayerWebService() {
        //given        Player referencePlayer = new Player(PLAYER_NAME, PLAYER_VALUE);
doReturn(referencePlayer).when(playerWebServiceSpy).getPlayerByName(PLAYER_NAME);
//when        Player player = objectUnderTest.getPlayerByName(PLAYER_NAME);
//then        assertThat(player, is(referencePlayer));
verify(playerWebServiceSpy).getWebServiceName();
assertThat(playerWebServiceSpy.getWebServiceName(), is(PlayerWebServiceImpl.WEB_SERVICE_NAME));
}

}
```
In this test we want to mock retrieval of
Player
from the
PlayerWebService
(let's assume that normally it would try to send a request to the outside world - and we wouldn't want that to happen in our scenario) and test that our
PlayerService
returns the
Player
that we provided in the method stub and what is more we want to perform verification on the Spy that the method
getWebServiceName()
has been executed and that it has a very precisely defined return value. In other words we wanted to stub the method
getPlayerByName(...)
and wanted to perform verification of the spy by checking the
getWebServiceName()
method.
Let's check the test context:
**testApplicationContext.xml**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="https://www.springframework.org/schema/beans" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="https://www.springframework.org/schema/beans https://www.springframework.org/schema/beans/spring-beans.xsd">
  <import resource="applicationContext.xml"/>
  <bean class="com.blogspot.postprocessor.PlayerWebServicePostProcessor" />
</beans>
```
The test context is very small since it's importing the current
applicationContext.xml
and creating a Bean that is the key feature in this example - the
BeanPostProcessor
:
**PlayerWebServicePostProcessor.java**
```java
package com.blogspot.postprocessor;
import com.blogspot.toomuchcoding.processor.AbstractBeanPostProcessor;
import com.blogspot.toomuchcoding.service.PlayerWebService;
import static org.mockito.Mockito.spy;
/** * User: mgrzejszczak * Date: 07.05.13 * Time: 11:30 */public class PlayerWebServicePostProcessor extends AbstractBeanPostProcessor<PlayerWebService> {
    public PlayerWebServicePostProcessor() {
        super(PlayerWebService.class);
}
    @Override    public PlayerWebService doBefore(PlayerWebService bean) {
        return spy(bean);
}
    @Override    public PlayerWebService doAfter(PlayerWebService bean) {
        return bean;
}

}
```
The class is extending the
AbstractBeanPostProcessor
that implements the
[BeanPostProcessor ](https://static.springsource.org/spring/docs/3.2.x/javadoc-api/org/springframework/beans/factory/config/BeanPostProcessor.html)
interface. The logic behind this class is to register the Class for which one wants to perform some actions either before initialization (
[postProcessBeforeInitialization](https://static.springsource.org/spring/docs/3.2.x/javadoc-api/org/springframework/beans/factory/config/BeanPostProcessor.html#postProcessBeforeInitialization(java.lang.Object, java.lang.String))
) or after initialization of the bean (
[postProcessAfterInitialization](https://static.springsource.org/spring/docs/3.2.x/javadoc-api/org/springframework/beans/factory/config/BeanPostProcessor.html#postProcessAfterInitialization(java.lang.Object, java.lang.String))
). The AbstractBeanPostProcessor is well explained in my post
[Spring BeanPostProcessor for a specified type](https://toomuchcoding.com/2012/10/spring-beanpostprocessor-for-specified.html)
but there is one slight change - in my old post we were allowed by the abstraction to perform some actions on the bean without the possibility of returning a wrapper or a proxy on the bean.
As you can see in the case of
PlayerWebServicePostProcessor
before initialization we are creating a Spy using
Mockito.spy(...)
method. In this way we create a factory hook on the intialization of beans of given type - it's as simple as that. This method will be executed for all the classes that implement the
PlayerWebService
interface.
## Other possibilities
While checking out current solutions to this problem I have encountered the
[Springockito library](https://bitbucket.org/kubek2k/springockito/wiki/Home)
by Jakub Janczak.
I haven't been using this so I don't know what are (if there are any ;) ) production issues related to this library but it seems really nice and intuitive - great job Jakub! Still, you become dependent on the external library whereas in this example I've shown how to deal with the issue using Spring.
## Summary
In this post I've shown how to
- create mocks for existing beans using XML Spring configuration
- create a BeanPostProcessor implementation that performs logic for a given class of beans
- return Spy (you could also return a Mock) for the given class of bean
Now let's move through the Prons and Cons of my approach:
****
**Advantages**
- you use Spring native mechanism to create Test Doubles for your beans
- you are not required to add any additional external dependencies
- if you use the
AbstractBeanPostProcessor
you have very little changes to implement
**Disadvantages**
- you have to be familliar with internal Spring architecture (that it uses BeanPostProcessors) - but is it a disadvantage? ;) - in fact if you use the
AbstractBeanPostProcessor
you don't have to be familliar with it - you just have to provide the class type and actions to happen before and after initialization.
- it's less intuitive than annotations like in the [Springockito library](https://bitbucket.org/kubek2k/springockito/wiki/Home)
## Sources
The sources are available at
[TooMuchCoding BitBucket repository](https://bitbucket.org/gregorin1987/too-much-coding/src/0e7f9ad4eb4c1d2500562c3634253e26fc1e3a0e/Unit%20Testing/Mockito%20-%20Injecting%20Test%20Doubles%20in%20Spring?at=default)
and
[TooMuchCoding Github repository](https://github.com/marcingrzejszczak/too-much-coding/tree/master/Unit%20Testing/Mockito%20-%20Injecting%20Test%20Doubles%20in%20Spring)
.
