---
title: "Mockito Invocationonmock Checking"
summary: "Hi! In one of my projects we had a very interesting situation in terms of testing."
date: 2012-10-24

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
In one of my projects we had a very interesting situation in terms of testing. We couldn't mock an invocation of a static method due to the fact that PowerMock was not allowed to be used so there were plenty of objects and dependencies being initialized. What is more we were using a custom made dependency injection system that had a possibility of injecting a mock.
The problem was such that during a test we wanted to assert whether one of the objects was in a very precise state. This object was created using the new operator so we couldn't mock it (again no PowerMock allowed). Fortunately this object got passed to a method of an object that we could mock...
As presented below
timeConsumingExternalService
is an object that we could mock via the custom dependency injection system whereas the
SomePojo
class is an object whose state we would like to verify.
```
timeConsumingExternalService.processSomeObject(new SomePojo("name", "surname", 1, 1.0));
```
So what we did was that in our mock to which the object got passed we created a new
Answer
(the same
Answer
that I spoke of
[here](https://toomuchcoding.com/2012/10/simulation-of-time-consuming-actions-in.html)
). Due to which we could access the
InvocationOnMock
and the arguments passed to the method as such.
```
Mockito.doAnswer(new Answer<Object>() {   public Object answer(InvocationOnMock invocation) throws Throwable {    Object[] object = invocation.getArguments();
if (object.length > 0) {          SomePojo somePojo = (SomePojo) object[0];
Assert.assertEquals("name", somePojo.getName());
LOGGER.debug("Names are equal");
Assert.assertEquals("surname", somePojo.getSurname());
LOGGER.debug("Surnames are equal");
Assert.assertTrue(1 == somePojo.getIntValue());
LOGGER.debug("Ints are equal");
Assert.assertTrue(1.0 == somePojo.getDoubleValue());
LOGGER.debug("Doubles are equal");
LOGGER.debug("Object being an argument of the function [" + String.valueOf(somePojo) + "]");
}    return null;
}  }).when(timeConsumingExternalServiceMock).processSomeObject(Mockito.any(SomePojo.class));
```
Of course the logs regarding the equalities are unnecessary since if they wouldn't be equal we would have an assertion exception - I left them for the purpose of this post.
And in the logs we can find:
```groovy
pl.grzejszczak.marcin.ServiceIntegrationTest: 48 Names are equalpl.grzejszczak.marcin.ServiceIntegrationTest: 50 Surnames are equalpl.grzejszczak.marcin.ServiceIntegrationTest: 52 Ints are equalpl.grzejszczak.marcin.ServiceIntegrationTest: 54 Doubles are equalpl.grzejszczak.marcin.ServiceIntegrationTest: 56 Object being an argument of the function [SomePojo [name=name,
surname=surname,
intValue=1,
doubleValue=1.0]]
```
So in this way something that seems impossible to be verified can get verified :)
**Update! **
Thanks to Holger's suggestion I took a look at the
ArgumentCaptor
object and that is true that it is an elegant solution to retrieve information about the arguments executed on a method. Where
InvocationOnMock
can give you much more information and possibilities (for instance regarding the method being executed or just execute the real method) for this particular case a much more elegant, easier and faster way of dealing with the issue would be:
```
//service that executes the external service  executorService.execute(someTask);
final ArgumentCaptor<SomePojo> argumentCaptor = ArgumentCaptor.forClass(SomePojo.class);
Mockito.verify(timeConsumingExternalServiceMock).processSomeObject(argumentCaptor.capture());
SomePojo somePojo = argumentCaptor.getValue();
Assert.assertEquals("name", somePojo.getName());
LOGGER.debug("Names are equal");
Assert.assertEquals("surname", somePojo.getSurname());
LOGGER.debug("Surnames are equal");
Assert.assertTrue(1 == somePojo.getIntValue());
LOGGER.debug("Ints are equal");
Assert.assertTrue(1.0 == somePojo.getDoubleValue());
LOGGER.debug("Doubles are equal");
```
The logs:
```groovy
pl.grzejszczak.marcin.junit.SomeTask: 26 Before processing an objectpl.grzejszczak.marcin.junit.SomeTask: 28 After processing an objectpl.grzejszczak.marcin.ServiceIntegrationTest: 75 Names are equalpl.grzejszczak.marcin.ServiceIntegrationTest: 77 Surnames are equalpl.grzejszczak.marcin.ServiceIntegrationTest: 79 Ints are equalpl.grzejszczak.marcin.ServiceIntegrationTest: 81 Doubles are equal
```
Thanks again Holger!
