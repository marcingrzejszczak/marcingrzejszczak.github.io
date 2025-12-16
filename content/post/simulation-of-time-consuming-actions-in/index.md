---
title: "Simulation Of Time Consuming Actions In"
summary: "Hi! Quite recently in one of my projects I had a situation in which I needed to create an integration test for the application. That's not very odd isn't it?"
date: 2012-10-07

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
Quite recently in one of my projects I had a situation in which I needed to create an integration test for the application. That's not very odd isn't it? :)
What was interesting was the fact that the logic of the app involved some concurrency issues and one of the components had to connect to an external service which would take a couple of seconds. Since in the integration test there was no need to make the actual connection, the component needed to be mocked. What about the simulation of the time consuming action? Well, let's take a look at the way I did it...
**The task.**
```java
package pl.grzejszczak.marcin;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
/** * Service that does some things including processing of the external service *  * @author marcin *  */public class SomeTask implements Runnable {
 private static final Logger LOGGER = LoggerFactory.getLogger(SomeTask.class);
// Service is injected via a dependency injection system private Processable timeConsumingExternalService;
private void methodThatConnectsToExternalServices() {
  // connects to an external service and spends a couple of seconds there  LOGGER.debug("Before processing");
timeConsumingExternalService.process();
LOGGER.debug("After processing");
// some other things to do
}
 public void run() {
  methodThatConnectsToExternalServices();
}
 public void setTimeConsumingExternalService(Processable timeConsumingExternalService) {
  this.timeConsumingExternalService = timeConsumingExternalService;
}

}
```
**The integration test.**
```java
package pl.grzejszczak.marcin;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import org.mockito.Mockito;
import org.mockito.invocation.InvocationOnMock;
import org.mockito.stubbing.Answer;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
public class ServiceIntegrationTest {
 private static final Logger LOGGER = LoggerFactory.getLogger(ServiceIntegrationTest.class);
private ExecutorService executorService = Executors.newCachedThreadPool();
private Processable timeConsumingExternalServiceMock = Mockito.mock(Processable.class);
private SomeTask someTask = new SomeTask();
public ServiceIntegrationTest() {
  initializeMocks();
}
 private void initializeMocks() {
  Mockito.doAnswer(new Answer<Object>() {
   public Object answer(InvocationOnMock invocation) throws Throwable {
    // Simulation of connection to external services    LOGGER.debug("Sleeping");
Thread.sleep(5000);
LOGGER.debug("Stopped Sleeping");
return null;
}

}
).when(timeConsumingExternalServiceMock).process();
// Inject the mock to the Task - in any possible way  someTask.setTimeConsumingExternalService(timeConsumingExternalServiceMock);
}
 public void executeTest() {
  executorService.execute(someTask);
}
 public static void main(String args[]) {
  ServiceIntegrationTest integrationTest = new ServiceIntegrationTest();
integrationTest.executeTest();
}

}
```
And the output to the console:
2012-10-07 22:42:37,378 DEBUG pl.grzejszczak.marcin.SomeTask:21 Before processing
2012-10-07 22:42:37,389 DEBUG pl.grzejszczak.marcin.ServiceIntegrationTest:28 Sleeping
2012-10-07 22:42:42,390 DEBUG pl.grzejszczak.marcin.ServiceIntegrationTest:30 Stopped Sleeping
2012-10-07 22:42:42,392 DEBUG pl.grzejszczak.marcin.SomeTask:23 After processing
Let's take a closer look at the most important part in which an Answer for the execution of the service is being created
```
Mockito.doAnswer(new Answer<Object>() {   public Object answer(InvocationOnMock invocation) throws Throwable {    // Simulation of connection to external services    LOGGER.debug("Sleeping");
Thread.sleep(5000);
LOGGER.debug("Stopped Sleeping");
return null;
}  }).when(timeConsumingExternalServiceMock).process();
```
This piece of code changes the default action that should be done by the given object on a given method execution. In this particular case we had to mock a method that returns void - that's why we start with doAnswer(...) and finish with when(...).process().
That is how inside the integration test I managed to create a simulation of waiting for the service to finish. If you have any ideas or comments on how you would do it in another way please feel free to post a comment below :)
