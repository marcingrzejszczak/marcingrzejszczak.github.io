---
title: "Review Of Instant Mock Testing With"
summary: "Hi! Quite recently I was asked by Packt publishing to write a review of the book Instant Mock Testing with PowerMock by Deep Shah."
date: 2013-12-16

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
Quite recently I was asked by Packt publishing to write a review of the book
[Instant Mock Testing with PowerMock](https://bit.ly/181GmFv)
by Deep Shah. I have just managed to go through it and would like to share my thoughts :)
[]()
## The layout
I enjoyed the book's layout with clear separation of the discussed issues with level of expertise required for the given section. What was also really nice was the precise division of content into the introductory part and the concrete solution to the problem.
## What I liked
- PowerMock is a very powerful tool that allows to test legacy code that was very badly written. On the other hand it can be tempting not to refactor existing code or even write bad code from the very beginning. So after this long introduction what I liked was that the author emphasised at the beginning of some chapters how a proper design should look like and that PowerMock breaks the rule
- Names of test methods in the shown examples - they are very clear and teach good practices
- Important parts of code are shown in bold - it attracts the reader and makes him focus immediately on the crucial elements
- The examples clearly present the solution to the given problem
- That I could learn about the PowerMock's *suppress* functionality :)
- I had all the important PowerMock features in one place written in a concise and readable manner
- Very thorough explanations for beginners in terms of adding PowerMock to Eclipse or IntelliJ
- Introductory sections of each recipie (for example introducing the Active Record pattern)
## What I didn't like
- It's a very subjective opinion but I definitely prefer creating assertions through
assertThat
methods with proper matchers instead of using
assertEquals
,
assertNull
etc.
- Not sure if that many javadocs were needed in the examples - for me they blur the image
- On one hand the tests seem clean but on the other there are test scenarios in which the implementation is being tested instead of behaviour (for example in stubbing/verifying private methods or in partial mocking with spies). Of course I understand that sometimes it's done if we really care about some method execution or for the sake of showing PowerMock capabilities but in some cases (e.g. Partial mocking with spies) the refactoring process is not clear to me.
## Summary
I really enjoyed the book
[Instant Mock Testing with PowerMock](https://www.packtpub.com/mock-testing-with-powermock/book)
by Deep Shah because I like books related to computer science :) I'm pretty sure that someone that reads it will know about all the important PowerMock's features - Deep Shah did a good job here. I would however recommend that one reads the PowerMock's documentation first, especially the
[when to use it part](https://code.google.com/p/powermock/wiki/Motivation)
since as authors state it:
> ... PowerMock is mainly intended for people with expert knowledge in unit testing. Putting it in the hands of junior developers may cause more harm than good.
Also there is good tutorial
[recommended by the PowerMock's authors](https://www.jayway.com/2013/03/05/beyond-mocking-with-powermock/)
and if you are a Mockito user you can profit from the
[Mockito usage at the project's wiki](https://code.google.com/p/powermock/wiki/MockitoUsage13)
(although the examples there are not that specific as in Deep Shah's book).
