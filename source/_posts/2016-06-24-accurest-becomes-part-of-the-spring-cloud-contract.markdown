---
layout: post
title: "Accurest becomes part of the Spring Cloud Contract"
date: 2016-06-24 18:43:07 +0200
comments: true
categories:
- spring-cloud
- deployment
- accurest
- spring-cloud-contract
- consumer-driven-contracts
- cdc
---

I'm extremely happy to announce that we have successfully rebranded the [Accurest project](https://codearte.github.io/accurest). It's officially become part of the [Spring Cloud Contract](https://github.com/spring-cloud/spring-cloud-contract) initiative. Ladies and Gentlemen please welcome the new projects:

* [Spring Cloud Contract Verifier](https://codearte.github.io/accurest/)
* [Spring Cloud Contract Stub Runner](https://codearte.github.io/accurest/#spring-cloud-contract-stub-runner)

<!-- more -->

## A little bit of history

Accurest was created because of lack of an easy-to-use tool for doing [Consumer Driven Contracts](https://martinfowler.com/articles/consumerDrivenContracts.html). From our production experience the biggest problem was lack of verification that the defined contract actually does what it says it does. We wanted to ensure that from the contract automatically tests are generated so that we can have a proof that the stubs are reliable. Since there was no such tool the first commit of Accurest took place on 12/2014. The very idea and its implementation was initially set by [Jakub Kubrynski](https://www.kubrynski.com/) and me. The last available version of Accurest was 1.1.0 released on 06/2016 (the docs for the old version are [available here](https://codearte.github.io/accurest/deprecated/)). During these 19 months a lot of feedback has been gathered. The tool has received a lot of very good reception and that made us want to work even harder. Many times we have decided to decrease the time required for sleeping so as to fix a bug or develop a new feature in Accurest.

## Notable features

Speaking of features, especially quite a few of them definitely makes Accurest stand out on the "market" of Consumer Driven Contract (CDC) tooling. Out of many the most interesting are:

- Possibility to do CDC with messaging
- Clear and easy to use, statically typed DSL
- Possibility to copy paste your current JSON file to the contract and only edit its elements
- Automatic generation of tests from the defined Contract
- Stub Runner functionality - the stubs are automatically downloaded at runtime from Nexus / Artifactory
- Spring Cloud integration - no discovery service is needed for integration tests

For more information check out my posts about [Stub Runner](https://toomuchcoding.com/blog/2016/04/06/accurest-stubrunner-released/), [Accurest Messaging](https://toomuchcoding.com/blog/2016/04/30/accurest-and-stub-runner-1-dot-1-0-dot-m3/) or [just read the docs](https://codearte.github.io/accurest/).

## Spring Cloud Contract

In Pivotal we came to the conclusion that Accurest could become an interesting addition to our Spring Cloud tooling. Due to the increased interest of the community in the Consumer Driven Contracts approach we've decided to start the [Spring Cloud Contract](https://github.com/spring-cloud/spring-cloud-contract) initiative.

Accurest became *Spring Cloud Contract Verifier* (note: the name might change in the future) but for the time being will remain in the [Codearte repository](https://github.com/Codearte). It's becoming the part of Spring Cloud tooling as a mature tool with a growing community around it. Some arguments for that are that it has:

- [a nice AsciiDoc documentation that was completely rewritten following users' feedback](https://codearte.github.io/accurest/)
- [active Gitter channel where we try to immediately answer any support questions](https://gitter.im/Codearte/accurest)
- [Over 80 stars on Github and counting ;)](https://github.com/Codearte/accurest/stargazers)

Since we believe very much in the Consumer Driven Contract approach we also want to do the library in a Client Driven way. That means that we (server side) are very open to your feedback (consumer side) and want you be the main driver of changes in the library.

## Credits

The Accurest project would never come to life without the hard work of the [Codearte](https://codearte.io) developers (the order is random):

- [Olga Maciaszek-Sharma](https://twitter.com/olga_maciaszek)
- [Jakub Kubrynski](https://www.kubrynski.com)
- [Marcin Zajaczkowski](https://solidsoft.wordpress.com/)
- [Mariusz Smykula](https://github.com/mariuszs)

and obviously everybody who has [ever commited something to the project](https://github.com/Codearte/accurest/graphs/contributors).

## Links

If you want to read more about *Spring Cloud Contract Verifier* just check out the following links.

- [Spring Cloud Contract Verifier Github Repository](https://github.com/Codearte/accurest)
- [Spring Cloud Contract Verifier Documentation](https://codearte.github.io/accurest)
- [Accurest Legacy Documentation](https://codearte.github.io/accurest/deprecated)
- [Spring Cloud Contract Stub Runner Documentation](https://codearte.github.io/accurest/#spring-cloud-contract-stub-runner)
- [Spring Cloud Contract Stub Runner Messaging Documentation](https://codearte.github.io/accurest/#stub-runner-for-messaging)
- [Spring Cloud Contract Verifier Gitter](https://gitter.im/Codearte/accurest)
- [Spring Cloud Contract Verifier Maven Plugin](https://github.com/Codearte/accurest-maven-plugin)
