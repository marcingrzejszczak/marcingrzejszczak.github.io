---
title: "How To Speed Up Your Gradle Build From"
summary: "Even though I was supposed to write a series of blog posts about micro-infra-spring here at Too Much Coding blog , today I'll write about how we've managed to decrease our biggest project's build time from 90 to 8…"
date: 2015-02-08

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
Even though I was supposed to write a series of blog posts about
[micro-infra-spring](https://toomuchcoding.blogspot.com/search/label/micro-infra-spring)
here at
[Too Much Coding blog](https://toomuchcoding.blogspot.com/)
, today I'll write about how we've managed to decrease our biggest project's build time from 90 to 8 minutes!
[]()
At one of the companies that I've been working we've faced a big problem related to pull request build times. We have one monolithic application that we are in progress of slicing into microservices but still until this process is finished we have to build that big app for each PR. We needed to change things to have really fast feedback from our build so that pull request builds don't get queued up endlessly in our CI. You can only imagine the frustration of developers who can't have their branches merged to master because of the waiting time.
**Structure**
In that project we have over 200 Gradle modules and over a dozen big projects (countries) from which we can build some (really fat) fat-jars. We have also a core module that if we change then we would have to rebuild all the big projects to check if they weren't affected by the modifications. There are a few old countries that are using GWT compilers and we have some JS tasks executed too.
**Initial stats**
Before we started to work on optimization of the process the whole application (all the countries) was built in about 1h 30 minutes.
*Current build time: ~90 minutes.*
**Profile your build**
First thing that we've done was to run the build with the --profile switch.
That way Gradle created awesome stats for our build. If you are doing any sort of optimization then it's crucial to gather measurements and statistics. Check out this
[Gradle page about profiling your build ](https://gradle.org/docs/current/userguide/tutorial_gradle_command_line.html#sec:profiling_build)
for more info on that switch and features.
**Exclude long running tasks in dev mode**
It turned out that we are spending a lot of time on JS minification and on GWT compilation. That's why we have added a custom property -PdevMode to disable some long running tasks in dev mode build. Those tasks were:
- excluded JS minification
- optimized GWT compilation:
*Overall gain: ~ 40 minutes.*
*Current build time: ~50 minutes.*
**Check out your tests**
Together with the one and only
[Adam Chudzik](https://github.com/achudzik)
we have started to write our own
[Gradle Test Profiler](https://github.com/marcingrzejszczak/gradle-test-profiler)
(it's a super beta version ;) ) that created a single CSV with sorted tests by their execution time. We needed quick and easy gains without endless test refactoring and it turned out that it's really simple. One of our tests took 50 seconds to execute and it was testing a feature that has and will never be turned on on production. Of course there were plenty of other tests that we should take a look into (we'd have to look for test duplication, check out the test setup etc.) but it would involve more time, help of a QA and we needed quick gains.
Benefit: By simple disabling this test we gained about 1 minute.
*Overall gain: ~ 41 minutes.*
*Current build time: ~49 minutes.*
**Turn on the --parallel Gradle flag at least for the compilation**
Even though at this point our gains were more or less 40 minutes it was still unacceptable for us to wait 40 minutes for the pull request to be built.
That's why we decided to go parallel! Let's build the projects (over 200) in parallel and we'll gain a lot of time on that. When you execute the Gradle build with the --parallel flag Gradle calculates how many threads can be used to concurrently build the modules. For more info go to the
[Gradle's documentation on parallel project execution](https://gradle.org/docs/current/userguide/multi_project_builds.html#sec:parallel_execution)
.
It's an incubating feature so wen we started to get BindExceptions on port allocation we initially thought that most likely it's Gradle's fault. Then we had a chat with
[Szczepan Faber](https://twitter.com/szczepiq)
who worked for Gradleware and it turns out that the feature is actually really mature (thx Szczepan for the help BTW :) ).
We needed quick gains so instead of fixing the port binding stuff we decided only to compile everything in parallel and then run tests sequentially.
Benefit: By doing this lame looking hack we gained ~4 mintues (on my 8 core laptop).
*Overall gain: ~ 45 minutes.*
*Current build time: ~45 minutes.*
**Don't be a jerk - just prepare your tests for parallelization**
This command seemed so lame that we couldn't even look at it. That's why we said - let's not be jerks and just fix the port issues.
So we went through the code, randomized all the fixed ports, patched
[micro-infra-spring](https://github.com/4finance/micro-infra-spring)
so it does the same upon Wiremock and Zookeeper instantiation and just ran the building of the project like this:
We were sure that this is the killer feature that we were lacking and we're going to win the lottery. Much to our surprise the result was really disappointing.
Benefit: Concurrent project build decreased the time by ~5 minutes.
*Overall gain: ~ 50 minutes.*
*Current build time: ~40 minutes.*
**Check out your project structure**
You can only imagine the number of WTFs that were there in our office. How on earth is that possible?
We've opened up htop, iotop and all the possible tools including vmstat  to see what the hell was going on. It turned out that context switching is at an acceptable level whereas at some point of the build only part of the cores are used as if sth was executed sequentially!
The answer to that mystery was pretty simple. We had a wrong project structure.
We had a module that ended up as a test-jar in testCompile dependency of other projects. That means that the vast majority of modules where waiting for this project to be built. Built means compiled and tested. It turned out that this test-jar module had also plenty of slow integration tests in it so only after those tests were executed could other modules be actually built!
**Simple source moving can drastically increase your speed**
By simply moving those slow tests to a separate module we've unblocked the build of all modules that were previously waiting.
Now we could do further optimization - we've split the slow integration tests into two modules to make all the modules in the whole project be built in more or less equal time (around 3,5 minutes).
.
Benefit: Fixing the project structure decreased the time by ~10 minutes
*Overall gain: ~ 60 minutes.*
*Current build time: ~30 minutes.*
**Don't save on machine power**
We've invested in some big AWS instance with 32 cores and 60 gb of RAM to really profit from the parallel build's possibilities. We're paying about 1.68$ per one hour of such machine's (c3.8xlarge) working time.
If someone form the management tells you that that machine costs a lot of money and the company can't afford it you can actually do a fast calculation. You can ask this manager what is more expensive - paying for the machine or paying the developer for 77 minutes * number of builds of waiting?
Benefit: Paying for a really good machine on AWS decreased the build time by ~22 minutes
*Overall gain: ~ 82 minutes.*
*Current build time: ~8 minutes.*
**What else can we do?**
Is that it? Can we decrease the time further on? Sure we can!
Possible solutions are:
- Go through all of the tests and check why some of them take so long to run
- Go through the integration tests and check if don't duplicate the logic - we will remove them
- We're using Liquibase for schema versioning and we haven't merged the changests for some time thus sth like 100 changesets are executed each time we boot up Spring context (it takes more or less 30 seconds)
- We could limit the Spring context scope for different parts of our applications so that Spring boots up faster
- Buy a more powerful machine ;)
There is also another, better way ;)
SPLIT THE MONOLITH INTO MICROSERVICES AND GO TO PRODUCTION IN 5 MINUTES ;)
**Summary**
Hopefully I've managed to show you how you can really speed up your build process. The work to be done is difficult, sometimes really frustrating but as you can see very fruitful.
