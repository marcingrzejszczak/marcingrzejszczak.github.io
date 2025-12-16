---
title: "Review Of Getting Started With Guava"
summary: "Hi! I didn't have much time to write posts recently (beacuse of work and my book \"Mockito Instant\" ) but I came across Bill Bejeck's book entitled \"Getting Started with Guava\" ."
date: 2013-09-30

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
I didn't have much time to write posts recently (beacuse of work and my book
["Mockito Instant"](https://www.packtpub.com/how-to-create-stubs-mocks-spies-using-mockito/book)
) but
I came across Bill Bejeck's book entitled
["Getting Started with Guava"](https://www.packtpub.com/getting-started-with-google-guava/book)
. After having read it I decided that I will try also to blog about computer science related books. So without any futher ado let's move to the review :)
[]()
To begin with I really enjoyed the book's structure – one can see that the author had a clear view of the book: introduction to the functionality, its presentation with examples and a short review. By keeping such a fixed structure the reader wasn't surprised by the content in each of the chapters what made reading even more pleasant.
Let's move quickly through the book content chapter by chapter.
**Basic Guava Utilities**
First of all you will be able to increase your knowledge about joining and splitting operations on collections by means of the Joiner and Splitter classes - no more unnecessary writing of loops! You will be able also to learn how to operate on Strings using
CharMatcher, Charsets, Strings which often is extremely tidious and produces a lot of boilerplate code. Next you will be able to learn about Preconditions - you won't have to write those cascades of if's in terms of defensive programming. Instead how about checking a condition and throw a runtime exception? To end with the author shows how to use Guava's utility classes to create implementation of toString, hashCode and compareTo methods.
**Functional Programming with Guava**
In this chapter the author shows how to introduce some functional approach to your Object oriented Java code with the Function, Predicate and Supplier interfaces and their corresponding utility classes Functions, Predicates and Suppliers.
**Working with Collections**
Since the Guava library emerged from issues related with collection manipulation the author could show the best examples in this chapter. You will learn about the Collections, FluentIterables, and Iterables utility classes. The author also mentions the Range class that you can use to represent boundaries. You will also be able to find information on other types of collections such as Bimaps (maps that aside from being navigated in the standard key to value way can be navigated from values to keys), Tables (replacement for map of maps), Multimaps (values are collections). There is also a part of the chapter related to the Ordering class that fives you additional posisbilities of working with Comparators.
**Concurrency**
The issue of concurrency is a very difficult issue as such. Guava can assist you in this difficult subject in a number of was that the author depicts: the Monitor class (version of a Mutex) can help you provide the serial access to part of your code, the Futures utility class to work with Future instances and many more cool solutions ;)
**Guava Cache**
The author shows several ways of creating caches, showing their statistics and how to configure them. You will also be able to learn how to register listeners for different cashe related types of events.
**The Event Bus**
The author shows how to subscribe to events by using the Google Guava's Event Bus. What I really liked about this chapter was the presentation of the reason for incorporating it in a project (loose coupling) and a sample of using it in a Spring based application.
**Working with Files**
You will be able to find presentations of the utility classes and helpful solutions related to working with IO such as Files, CharStreams, ByteStreams, Readers , Writers the Closer class (elegant way of ensuring that the Closeable instance gets properly closed). The author presents the concept behind source and sinks too. So if you work a lot with files you will find plenty of cool stuff here.
**Odds and Ends**
Useful classes related to creating hash codes, working with Throwables and creating your applications in a null safe way.
I would never say that I know every aspect of Guava but I tend to use a lot of its functionalities at work. That's why I was really curious about the level of details that the author wanted to present in his book and whether I would find some really interesting details of the library that I wasn't aware of. What I found in the book was very satisfactory for me because although I was already familliar with the majority of the presented examples and functionalities, still I found plenty of those „little things” that I can use to improve my code and remove more boilerplate.
Speaking of which, what I really wanted to look at from the very beginning where the code samples showing how cool and helpful Guava really is. Being a true fan of unit testing I was very happy to see that the author put a lot of effort in those examples - the majority of functionalities were described by means of unit tests and showing some real life situations.
To sum it up I think that Bill Bejeck has put a tremendous effort in writing his book and he has done the job exceptionally well. I would recommend „Getting Started with Guava” for both newbies and experts – for sure both of these groups will be very satisfied.
