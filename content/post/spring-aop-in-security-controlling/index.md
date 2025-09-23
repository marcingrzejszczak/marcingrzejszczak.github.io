---
title: "Spring Aop In Security Controlling"
summary: "The following post will show how in one of the projects that I took part in we used Spring's AOP to introduce some security related functionalities."
date: 2012-10-27

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
The following post will show how in one of the projects that I took part in we used Spring's AOP to introduce some security related functionalities. The concept was such that in order for the user to see some UI components he needed to have a certain level of security privillages. If that requirement was not met then the UIComponent was not presented. Let's take a look at the project structure:
[![](https://3.bp.blogspot.com/-SpxeeCEcyaE/UIx6drCJ6sI/AAAAAAAAAHw/ASHo5_3RQQ0/s320/Aspects+project.png)](https://3.bp.blogspot.com/-SpxeeCEcyaE/UIx6drCJ6sI/AAAAAAAAAHw/ASHo5_3RQQ0/s1600/Aspects+project.png)
Then there were also the
**aopApplicationContext.xml :**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="https://www.springframework.org/schema/beans" xmlns:context="https://www.springframework.org/schema/context" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:aop="https://www.springframework.org/schema/aop" xsi:schemaLocation="https://www.springframework.org/schema/beans https://www.springframework.org/schema/beans/spring-beans-3.0.xsd https://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context-3.0.xsd https://www.springframework.org/schema/tx https://www.springframework.org/schema/tx/spring-tx-3.0.xsd https://www.springframework.org/schema/util https://www.springframework.org/schema/util/spring-util-3.1.xsd https://www.springframework.org/schema/aop https://www.springframework.org/schema/aop/spring-aop-3.0.xsd">
  <aop:aspectj-autoproxy />
  <context:annotation-config />
  <context:component-scan base-package="pl.grzejszczak.marcin.aop">
    <context:exclude-filter type="annotation" expression="org.aspectj.lang.annotation.Aspect"/>
  </context:component-scan>
  <bean class="pl.grzejszczak.marcin.aop.interceptor.SecurityInterceptor" factory-method="aspectOf"/>
</beans>
```
Now let's take a look at the most interesting lines of the Spring's application context.
First we have all the required schemas - I don't think that this needs to be explained in more depth.
Then we have:
```groovy
<aop: aspectj-autoproxy/>
```
which enables the
**@AspectJ**
support.
Next there is the
```xml
<context:annotation-config />
<context:component-scan base-package="pl.grzejszczak.marcin.aop">
  <context:exclude-filter type="annotation" expression="org.aspectj.lang.annotation.Aspect"/>
</context:component-scan>
```
first we are turning on Spring configuration via annotations. Then deliberatly we exclude aspects from being initialized as beans by Spring itself. Why? Because...
```
<bean class="pl.grzejszczak.marcin.aop.interceptor.SecurityInterceptor" factory-method="aspectOf"/>
```
we want to create the aspect by ourselves and provide the
factory-method="aspectOf"
. By doing so our aspect will be included in the autowiring process of our beans - thus all the fields annotated with the
@Autowired
annotation will get the beans injected.
Now let's move on to the code:
**UserServiceImpl.java**
```java
package pl.grzejszczak.marcin.aop.service;
import org.springframework.stereotype.Service;
import pl.grzejszczak.marcin.aop.type.Role;
import pl.grzejszczak.marcin.aop.user.UserHolder;
@Servicepublic class UserServiceImpl implements UserService {
 private UserHolder userHolder;
@Override public UserHolder getCurrentUser() {
  return userHolder;
}
 @Override public void setCurrentUser(UserHolder userHolder) {
  this.userHolder = userHolder;
}
 @Override public Role getUserRole() {
  if (userHolder == null) {
   return null;
}
  return userHolder.getUserRole();
}

}
```
The class UserServiceImpl is immitating a service that would get the current user information from the db or from the current application context.
**UserHolder.java**
```java
package pl.grzejszczak.marcin.aop.user;
import pl.grzejszczak.marcin.aop.type.Role;
public class UserHolder {
 private Role userRole;
public UserHolder(Role userRole) {
  this.userRole = userRole;
}
 public Role getUserRole() {
  return userRole;
}
 public void setUserRole(Role userRole) {
  this.userRole = userRole;
}

}
```
This is a simple holder class that holds information about current user Role.
**Role.java**
```java
package pl.grzejszczak.marcin.aop.type;
public enum Role {
 ADMIN("ADM"), WRITER("WRT"), GUEST("GST");
private String name;
private Role(String name) {
  this.name = name;
}
 public static Role getRoleByName(String name) {
  for (Role role : Role.values()) {
   if (role.name.equals(name)) {
    return role;
}

}
  throw new IllegalArgumentException("No such role exists [" + name + "]");
}
 public String getName() {
  return this.name;
}
 @Override public String toString() {
  return name;
}

}
```
Role is an enum that defines a role for a person being an
*Admin*
,
*Writer*
or a
*Guest*
.
**UIComponent.java**
```java
package pl.grzejszczak.marcin.aop.ui;
public abstract class UIComponent {
 protected String componentName;
protected String getComponentName() {
  return componentName;
}

}
```
An abstraction over concrete implementations of some UI components.
**SomeComponentForAdminAndGuest.java**
```java
package pl.grzejszczak.marcin.aop.ui;
import pl.grzejszczak.marcin.aop.annotation.SecurityAnnotation;
import pl.grzejszczak.marcin.aop.type.Role;
@SecurityAnnotation(allowedRole = {
 Role.ADMIN, Role.GUEST
}
)public class SomeComponentForAdminAndGuest extends UIComponent {
 public SomeComponentForAdminAndGuest() {
  this.componentName = "SomeComponentForAdmin";
}
 public static UIComponent getComponent() {
  return new SomeComponentForAdminAndGuest();
}

}
```
This component is an example of a UI component extention that can be seen only by users who have roles of
*Admin*
or
*Guest*
.
**SecurityAnnotation.java**
```java
package pl.grzejszczak.marcin.aop.annotation;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import pl.grzejszczak.marcin.aop.type.Role;
@Retention(RetentionPolicy.RUNTIME)public @interface SecurityAnnotation {
 Role[] allowedRole();
}
```
Annotation that defines a roles that can have this component created.
**UIFactoryImpl.java**
```java
package pl.grzejszczak.marcin.aop.ui;
import org.apache.commons.lang.NullArgumentException;
import org.springframework.stereotype.Component;
@Componentpublic class UIFactoryImpl implements UIFactory {
 @Override public UIComponent createComponent(Class<? extends UIComponent> componentClass) throws Exception {
  if (componentClass == null) {
   throw new NullArgumentException("Provide class for the component");
}
  return (UIComponent) Class.forName(componentClass.getName()).newInstance();
}

}
```
A factory class that given the class of an object that extends UIComponent returns a new instance of the given UIComponent.
**SecurityInterceptor.java**
```java
package pl.grzejszczak.marcin.aop.interceptor;
import java.lang.annotation.Annotation;
import java.lang.reflect.AnnotatedElement;
import java.util.Arrays;
import java.util.List;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Pointcut;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import pl.grzejszczak.marcin.aop.annotation.SecurityAnnotation;
import pl.grzejszczak.marcin.aop.service.UserService;
import pl.grzejszczak.marcin.aop.type.Role;
import pl.grzejszczak.marcin.aop.ui.UIComponent;
@Aspectpublic class SecurityInterceptor {
 private static final Logger LOGGER = LoggerFactory.getLogger(SecurityInterceptor.class);
public SecurityInterceptor() {
  LOGGER.debug("Security Interceptor created");
}
 @Autowired private UserService userService;
@Pointcut("execution(pl.grzejszczak.marcin.aop.ui.UIComponent pl.grzejszczak.marcin.aop.ui.UIFactory.createComponent(..))") private void getComponent(ProceedingJoinPoint thisJoinPoint) {

}
 @Around("getComponent(thisJoinPoint)") public UIComponent checkSecurity(ProceedingJoinPoint thisJoinPoint) throws Throwable {
  LOGGER.info("Intercepting creation of a component");
Object[] arguments = thisJoinPoint.getArgs();
if (arguments.length == 0) {
   return null;
}
  Annotation annotation = checkTheAnnotation(arguments);
boolean securityAnnotationPresent = (annotation != null);
if (securityAnnotationPresent) {
   boolean userHasRole = verifyRole(annotation);
if (!userHasRole) {
    LOGGER.info("Current user doesn't have permission to have this component created");
return null;
}

}
  LOGGER.info("Current user has required permissions for creating a component");
return (UIComponent) thisJoinPoint.proceed();
}
 /**  * Basing on the method's argument check if the class is annotataed with  * {
@link SecurityAnnotation
}
  *   * @param arguments  * @return  */ private Annotation checkTheAnnotation(Object[] arguments) {
  Object concreteClass = arguments[0];
LOGGER.info("Argument's class - [{

}
]", new Object[] {
 arguments
}
);
AnnotatedElement annotatedElement = (AnnotatedElement) concreteClass;
Annotation annotation = annotatedElement.getAnnotation(SecurityAnnotation.class);
LOGGER.info("Annotation present - [{

}
]", new Object[] {
 annotation
}
);
return annotation;
}
 /**  * The function verifies if the current user has sufficient privilages to  * have the component built  *   * @param annotation  * @return  */ private boolean verifyRole(Annotation annotation) {
  LOGGER.info("Security annotation is present so checking if the user can use it");
SecurityAnnotation annotationRule = (SecurityAnnotation) annotation;
List<Role> requiredRolesList = Arrays.asList(annotationRule.allowedRole());
Role userRole = userService.getUserRole();
return requiredRolesList.contains(userRole);
}

}
```
This is the
**aspect**
defined at the
**pointcut**
of
**executing**
a function
createComponent
of the
UIFactory
interface. Inside the
**Around**
**advice**
there is the logic that first checks what kind of an argument has been passed to the method
createComponent
(for example
SomeComponentForAdminAndGuest.class
). Next it is checking if this class is annotated with
SecurityAnnotation
and if that is the case it checks what kind of
Roles
are required to have the component created. Afterwards it checks if the current user (from
UserService
to
UserHolder's
Roles
) has the required role to present the component. If that is the case
thisJoinPoint.proceed()
is called which in effect returns the object of the class that extends
UIComponent
.
Now let's test it - here comes the
SpringJUnit4ClassRunner
**AopTest.java**
```java
package pl.grzejszczak.marcin.aop;
import org.junit.Assert;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
import pl.grzejszczak.marcin.aop.service.UserService;
import pl.grzejszczak.marcin.aop.type.Role;
import pl.grzejszczak.marcin.aop.ui.SomeComponentForAdmin;
import pl.grzejszczak.marcin.aop.ui.SomeComponentForAdminAndGuest;
import pl.grzejszczak.marcin.aop.ui.SomeComponentForGuest;
import pl.grzejszczak.marcin.aop.ui.SomeComponentForWriter;
import pl.grzejszczak.marcin.aop.ui.UIFactory;
import pl.grzejszczak.marcin.aop.user.UserHolder;
@RunWith(SpringJUnit4ClassRunner.class)@ContextConfiguration(locations = {
 "classpath:aopApplicationContext.xml"
}
)public class AopTest {
 @Autowired private UIFactory uiFactory;
@Autowired private UserService userService;
@Test public void adminTest() throws Exception {
  userService.setCurrentUser(new UserHolder(Role.ADMIN));
Assert.assertNotNull(uiFactory.createComponent(SomeComponentForAdmin.class));
Assert.assertNotNull(uiFactory.createComponent(SomeComponentForAdminAndGuest.class));
Assert.assertNull(uiFactory.createComponent(SomeComponentForGuest.class));
Assert.assertNull(uiFactory.createComponent(SomeComponentForWriter.class));
}

}
```
And the logs:
```java
pl.grzejszczak.marcin.aop.interceptor.SecurityInterceptor:26 Security Interceptor createdpl.grzejszczak.marcin.aop.interceptor.SecurityInterceptor:38 Intercepting creation of a componentpl.grzejszczak.marcin.aop.interceptor.SecurityInterceptor:48 Argument's class - [[class pl.grzejszczak.marcin.aop.ui.SomeComponentForAdmin]]pl.grzejszczak.marcin.aop.interceptor.SecurityInterceptor:54 Annotation present - [@pl.grzejszczak.marcin.aop.annotation.SecurityAnnotation(allowedRole=[ADM])]pl.grzejszczak.marcin.aop.interceptor.SecurityInterceptor:57 Security annotation is present so checking if the user can use itpl.grzejszczak.marcin.aop.interceptor.SecurityInterceptor:70 Current user has required permissions for creating a componentpl.grzejszczak.marcin.aop.interceptor.SecurityInterceptor:38 Intercepting creation of a componentpl.grzejszczak.marcin.aop.interceptor.SecurityInterceptor:48 Argument's class - [[class pl.grzejszczak.marcin.aop.ui.SomeComponentForAdminAndGuest]]pl.grzejszczak.marcin.aop.interceptor.SecurityInterceptor:54 Annotation present - [@pl.grzejszczak.marcin.aop.annotation.SecurityAnnotation(allowedRole=[ADM, GST])]pl.grzejszczak.marcin.aop.interceptor.SecurityInterceptor:57 Security annotation is present so checking if the user can use itpl.grzejszczak.marcin.aop.interceptor.SecurityInterceptor:70 Current user has required permissions for creating a componentpl.grzejszczak.marcin.aop.interceptor.SecurityInterceptor:38 Intercepting creation of a componentpl.grzejszczak.marcin.aop.interceptor.SecurityInterceptor:48 Argument's class - [[class pl.grzejszczak.marcin.aop.ui.SomeComponentForGuest]]pl.grzejszczak.marcin.aop.interceptor.SecurityInterceptor:54 Annotation present - [@pl.grzejszczak.marcin.aop.annotation.SecurityAnnotation(allowedRole=[GST])]pl.grzejszczak.marcin.aop.interceptor.SecurityInterceptor:57 Security annotation is present so checking if the user can use itpl.grzejszczak.marcin.aop.interceptor.SecurityInterceptor:66 Current user doesn't have permission to have this component createdpl.grzejszczak.marcin.aop.interceptor.SecurityInterceptor:38 Intercepting creation of a componentpl.grzejszczak.marcin.aop.interceptor.SecurityInterceptor:48 Argument's class - [[class pl.grzejszczak.marcin.aop.ui.SomeComponentForWriter]]pl.grzejszczak.marcin.aop.interceptor.SecurityInterceptor:54 Annotation present - [@pl.grzejszczak.marcin.aop.annotation.SecurityAnnotation(allowedRole=[WRT])]pl.grzejszczak.marcin.aop.interceptor.SecurityInterceptor:57 Security annotation is present so checking if the user can use itpl.grzejszczak.marcin.aop.interceptor.SecurityInterceptor:66 Current user doesn't have permission to have this component created
```
The unit test shows that for given Admin role only first two components get created whereas for the two others nulls are returned (due to the fact that user doesn't have proper rights).
That is how in our project we used Spring's AOP to create a simple framework that would check if the user can have the given component created or not. Thanks to this after having programmed the aspects one doesn't have to remember about writing any security related code since it will be done for him.
If you have any suggestions related to this post please feel free to comment it :)
