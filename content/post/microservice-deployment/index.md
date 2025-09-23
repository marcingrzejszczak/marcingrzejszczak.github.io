---
title: "Microservice Deployment"
summary: "It's been a while since my last post. In the meantime of course nothing has changed in terms of the microservice hype."
date: 2015-09-26

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
It's been a while since my last post. In the meantime of course nothing has changed in terms of the microservice hype. I've been  attending many microservice's talks and what I'm always missing are concrete details on many different subjects. One of which is deployment. In this post I'll try to depict how in a big, multinational company one would want to do microservice deployment. I'll walk you through the most basic deployment pipeline that could be created in such an enterprise.
[]()
#### The goal
Our goal was to:
**Enforce standards**
****
Have a unique way of deploying alll microservices - we need to enforce standards
**Tackle the microservice dependencies complexity issue**
Make the deployment process maintainable from the infrastructure and operations perspective
**Make the pipeline fast and certain**
Have the greatest possible certainty that our features are working fine.
We wanted to make the deployment pipeline as fast as possible.
It was crucial to add the possibility to automatically rollback if something goes wrong.
#### Enforce standards
It is crucial that if you're starting with microservices you start introducing standards. Standards of running applications, configuring them (externalized properties) but also you should enforce standards in how you deploy your applications. At some point in time we have seen that different applications do common tasks in different ways.
Why should we bother - we have business value to deliver and not waste time on enforcing standards - your manager might say. Actually he is really wrong because you're wasting plenty of time (thus money) on supporting such nonstandard applications. Imagine how much it needs for the new developers to understand how exactly the rules are set in this particular process.
The same relates to deployment and deployment pipelines. That's why we decided to enforce one, single way of deploying microservices.
#### Tackle the microservice dependencies complexity issue
If you have two monolithic applications talking to each other and not too many developers working on the codebases you can queue deployment of both apps and always perform end to end tests.
[![](https://2.bp.blogspot.com/-G80VsWKpIy0/VgbyR9nENVI/AAAAAAABII4/rYV8ZIMJi6A/s320/monolith.png)](https://2.bp.blogspot.com/-G80VsWKpIy0/VgbyR9nENVI/AAAAAAABII4/rYV8ZIMJi6A/s1600/monolith.png)
Two monolithic applications deployed for end to end testing
In case of microservices the scale starts to be a problem:
[![](https://2.bp.blogspot.com/-kggPwWHR-iQ/VgbyhX1x5aI/AAAAAAABIJA/tf2lLkCruxA/s320/many_microservices.png)](https://2.bp.blogspot.com/-kggPwWHR-iQ/VgbyhX1x5aI/AAAAAAABIJA/tf2lLkCruxA/s1600/many_microservices.png)
Many microservices deployed in different versions
The questions arise:
- Should I queue deployments of microservices on one testing environment or should I have an environment per microservice?
- To remove that issue I can have an environment per microservice
- In which versions should I deploy the dependent microservices - development or production versions?
#### Make the pipeline fast and certain
Since we really believe in the agile methodology and continuous deployment we would like our features to be delivered to production as fast as possible. When working with the monolithic applications we've faced the following issues:
- For monolithic applications we had plenty of unit, integration and end to end tests
- The different types of tests covered the same functionality up to three times
- The tests took a lot of time to run
Having all of this in mind we wanted not to have such issues with our new deployment pipeline.
#### Simplify the infrastructure complexity
Due to technical issues, difficulties to maintain the spawned environments we've decided to simplify the pipeline as much as possible. That's why since we are enthusiasts of TDD and we know what Consumer Driven Contract is we've decided not to do End to End tests. We're deploying our application to a virtual machine where the executed tests don't interfere with other pipelines executed in the very same time.
[![](https://3.bp.blogspot.com/-3BIKN1VzDKA/Vgb7oxk8jXI/AAAAAAABIJQ/q_A0LifBgEI/s320/stubbed_dependencies.png)](https://3.bp.blogspot.com/-3BIKN1VzDKA/Vgb7oxk8jXI/AAAAAAABIJQ/q_A0LifBgEI/s1600/stubbed_dependencies.png)
Execute tests on a deployed microservice on stubbed dependencies
That way you can look at your application tests (we called them smoke tests) in the following way:
[![](https://3.bp.blogspot.com/-7jseO68-q6A/Vgb8h7Ia1CI/AAAAAAABIJc/C8W0S4qZAic/s320/no_e2e_tests.png)](https://3.bp.blogspot.com/-7jseO68-q6A/Vgb8h7Ia1CI/AAAAAAABIJc/C8W0S4qZAic/s1600/no_e2e_tests.png)
We're testing microservices in isolation
Why smoke tests? Because we deliberately want to enforce the testing pyramid in the following way:
- A lot of unit tests executed during build time
- Some integration tests running on stubs of dependent services executed during build time
- Not many acceptance tests running on stubs of dependent services executed during build time (these can be treated as special case of integration tests)
- A handful of smoke tests executed on a deployed application to see if the app is really packaged properly
Such an approach to testing and deployment gives the following benefits:
- No need to deploy dependent services
- The stubs used for the tests ran on a deployed microservice are the same as those used during integration tests
- Those stubs have been tested against the application that produces them (check [Accurest](https://github.com/Codearte/accurest) for more information)
- We don't have many slow tests running on a deployed application - thus the pipeline gets executed much faster
- We don't have to queue deployments - we're testing in isolation thus pipelines don't interfere with each other
- We don't have to spawn virtual machines each time for deployment purposes
It brings however the following challenges:
- No end to end tests before production - you don't have the full certainty that a feature is working
- Due to this certainty that the functionality is working decreases
- First time the applications will talk in a real way will be on production
#### Overcoming fear of uncertainty
The argument that we don't know if a functionality is working properly made us invest more time and effort in tools that will give us more information on how our applications work on production. That's why we've added plenty of monitoring both technical and business via Graphite. Also we've introduced Seyren as the alerting mechanism to ping us immediately when something is really wrong on production.
Whatever time you spend on improving your tests, testing environments or UATs with endless hours of clicking - it will never signify that on production your application will run in the same manner.
Our decisions were related to trade offs. We decided to give away the complexity in the artificial test environments. That complexity was pushed to the monitoring of production instances. With microservices there is never an easy decision - there's always some price needed to pay.
#### The technical overview of the solution
We've divided the simplest scenario of the microservice deployment pipeline into the following steps.
[![](https://4.bp.blogspot.com/-JmkGUgmrI8Q/Vgb-y9Eg9RI/AAAAAAABIJo/QMa0rkaSfUk/s640/Microservice%2BPipeline.png)](https://4.bp.blogspot.com/-JmkGUgmrI8Q/Vgb-y9Eg9RI/AAAAAAABIJo/QMa0rkaSfUk/s1600/Microservice%2BPipeline.png)
Microservice deployment pipeline (without A/B testing)
**Build the app (after commit)**
Most preferably we would like after each merge of a PR trigger the deployment pipeline (thus do Continuous Deployment).
The result of this step would be to have the application tested in the following ways:
- unit and integration tests are ran
- validity of declared stubs specifications is tested against the application itself
Finally what is published to Nexus is the fat-jar of the application together with its stubs.
**Deploy to staging**
We deploy our freshly built application to the staging environment.
[Micro Infra Spring Stub-runner](https://github.com/4finance/micro-infra-spring/wiki/Stub-runner)
is responsible for downloading the current
**development**
versions of stubs of declared dependencies of the microservice.
In the first version of the pipeline we've decided to go towards development versions since we would like each of the applications to go live after each commit. That means that there is a high probability that the development version of a stub is in fact the production one. Of course that not necessarily needs to be true - but this is our trade off.
In the future versions of the pipeline we would like to test the app against both development and production versions of stubs.
**What is very important to see is that in this step we are upgrading the microservice's DB schema.**
****
**Test application rollback scenario**
We don't want to rollback the database. If you have MongoDB like databases there is no schema in fact. If you have Liquibase - you can provide the rollback scripts for relational DBs. They however introduce complexity on the DB level.
We've decided to go with a trade off that the complexity goes out from the DB level to the code. We're not rolling back the DB but we're rolling back the application. That means that the developers need to write their code to support backwards compatibility.
**That means that the NEW version of the application MUST support the OLD schema of the database. Also developers MUST NOT do backwards incompatible changes in subsequent liquibase scripts.**
****
We're running old smoke tests on the rolled back version of the application that is connected to the new version of the schema. That way we can ensure that most probably we will be able to rollback on production without greater issues.
**Deploy to production**
If the smoke tests have passed and we've checked the rollback procedures we can go live. Here the monitoring part comes in. We need to ensure that we've put all the KPI checking alerts in place. As a part of deployment procedure a review of monitoring and alerts needs to take place.
As you can see in the picture the first scenario of live deployment doesn't include 0 downtime approach. That was yet another trade off that we've decided to take. We don't want to tackle the issue of automatic data migration right now. Also for the developers writing code that supports both old and new schema is actually mind blowing. That's why we want to do things a step at a time - for now we kill all the application instances on production, boot one up and  change the schema and then boot the rest up too.
**Rollback procedure**
If our KPI monitoring starts to go all red on production then we need to rollback as fast as possible. Since we've tested the rollback procedure it shouldn't be an issue on production to kill all the instances, download the previous version of the app and run it against the new schema.
#### Summary
As everything related to distributed systems - you can see that microservice deployment is not easy. Actually it's full of trade offs and complexity. Starting from the infrastructure going through testing and finishing with database schema changes.
The presented solution seems to be an acceptable compromise between time, effort, certainty and feedback.
