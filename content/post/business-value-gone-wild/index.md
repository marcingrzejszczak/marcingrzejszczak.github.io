---
title: "Business Value Gone Wild"
summary: "This blog post will not be about microservices, Spring or any technology that I've already talked about in Too much coding blog ."
date: 2015-10-16

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
This blog post will not be about microservices, Spring or any technology that I've already talked about in
[Too much coding blog](https://toomuchcoding.blogspot.com/)
. This time it will be my opinion on two subjects
- the more and more frequent "it's not my problem" approach in the IT industry running in a corporation.
- the "business value" frenzy of the management
This article is definitely not a motivational one. Quite frankly, you might get depressed after reading it. Nonetheless, it's better to know how really corporate life sometimes looks like rather than get hit in the face.
TL;DR : the more you care in a corporate enterprise the worse for you. Eventually some developers will hate your ideas of quality and standards because they are paid to tap the keys. Your management will fire you for not bringing "business value". The faster you embrace it, the better for you - you'll start searching for a new job sooner.
[]()
#### Features are not only functionalities
Let's define some facts: IT is paid by the business. Business wants features. IT has to deliver features to gain money. That's a fact and our reality. Even if you hear from your managers that "cleaning technical debt is a necessity" what they really think is:
[![](https://4.bp.blogspot.com/-htlLzbCyZro/Vh6fjoKG5iI/AAAAAAABILw/_zk7wkxqPoU/s320/technical_debt.jpg)](https://4.bp.blogspot.com/-htlLzbCyZro/Vh6fjoKG5iI/AAAAAAABILw/_zk7wkxqPoU/s1600/technical_debt.jpg)
And actually that's not bizarre - business has no understanding of the technical aspects of the IT work. Here we can discern two types of business people:
- they don't get technical aspects, but they trust the engineers
- they don't care about technical aspects and they won't listen to any of the programmers' advice
If you have the latter business people then most likely you're in this situation:
[![](https://3.bp.blogspot.com/-LwS3CWbA0b0/Vh6qyT0FmbI/AAAAAAABIMM/1xY8oezZhFk/s1600/before.jpg)](https://3.bp.blogspot.com/-LwS3CWbA0b0/Vh6qyT0FmbI/AAAAAAABIMM/1xY8oezZhFk/s1600/before.jpg)
and actually you should be doing such a shift:
[![](https://1.bp.blogspot.com/-TCHAm6Q0yT0/Vh6oKJF3mLI/AAAAAAABIMA/pjQoINeQ35c/s320/cable_change.jpg)](https://1.bp.blogspot.com/-TCHAm6Q0yT0/Vh6oKJF3mLI/AAAAAAABIMA/pjQoINeQ35c/s1600/cable_change.jpg)
https://www.technalytical.com/2012/04/aesthetical-cable-management-before-and.html
In order to grow faster. What makes me really surprised that continuously the business picks the first option - just add more mess to the existing one without thinking of the consequences.
Now for the tricky part. Now change the word "business" to "developer" and everything is still valid.
"Delivering a feature" it's not only coding some functions in whatever language you are using. It's not taking a keyboard and pressing the keys to make the functionality work. If this is your approach then you're a key tapper. Tapping keys to get things done.
[![](https://4.bp.blogspot.com/-N9ueTp3dNhI/Vh6sGeO-OUI/AAAAAAABIMY/ZTgNPN9Pras/s320/dunno.jpg)](https://4.bp.blogspot.com/-N9ueTp3dNhI/Vh6sGeO-OUI/AAAAAAABIMY/ZTgNPN9Pras/s1600/dunno.jpg)
#### Programming is more than tapping keys
I hope that nobody feels offended by this term "key tapper". I'm not trying to be offensive - I'm just describing what I saw in my career. In my opinion there are a couple of different types of IT guys:
- there are people for whom programming is a passion. They put a lot of energy and effort to make things better
- there are also IT guys for whom programming isn't a passion, but still put (sucessfully)  a lot of energy and effort in order to make things better just because they want to be honest and valuable employees (thanks Michal Szostek)
- there are people for whom programming is not a passion and they just come to work and tap the keys
- there are others who would love to do stuff properly but the business is breathing at their necks to do stuff in a bad way because the "deadlines are coming".
- there are positions where people last. They come and simulate work. They lie, talk a lot and delegate work so that there is some impression of progress
Regardless of the position, if one doesn't focus on quality and just taps in the functionality then:
- even if he provides the business feature it might badly influence other people (introducing coupling between modules, breaking encapsulation etc.)
- the functionality might be written in such a way that you will result in the global timeout of the whole system
- you're not thinking about the company standards ([passing of CorrelationID for instance](https://www.slideshare.net/MarcinGrzejszczak/4financeit-microservices-092015-kaunas-jug/87?src=clipshare)), that will break the approaches set in the company. This in effect will lead in increased time needed to provide support
- writing the next functionality will take more time than the previous one
Even though it seems to be common knowledge, you can far too often hear something like this:
> I don't have time for this - it's not my problem. I've delivered my business feature and this is what I'm paid for. What you're referring to is not of my interest.
Now imagine that you join a project which is full of such developers and you're asked to fix a bug:
[![](https://2.bp.blogspot.com/-c6LCeYh8sHs/Vh7A-E-ZjaI/AAAAAAABIM0/gelqAg9YKU0/s400/new_guy.png)](https://2.bp.blogspot.com/-c6LCeYh8sHs/Vh7A-E-ZjaI/AAAAAAABIM0/gelqAg9YKU0/s1600/new_guy.png)
#### Technical changes are not bringing money
We have to educate both the business and the developers: writing features and providing business value is actually a sum of a coded and tested functionality with technical advancement. What are those? Code refactoring, introduction of new approaches, migrations from one way of doing things in one way to another. For example:
- version control system (e.g. SVN to Git)
- build system (e.g Maven to Gradle)
- UI framework (e.g. Vaadin to AngularJS)
- library versions (e.g. Spring 3.0 to Spring 4.0)
- going from deployment to application servers to embedded servlet containers (e.g. Glassifsh to embedded JAR with Jetty)
Why do we want these changes to happen? Because they ease our work and enforce standards. Why are standards important?
*"Pick a plug they said, it's gonna be easy, they said"*
[![](https://4.bp.blogspot.com/-StjBYB5gZOE/Vh7DWYGKYLI/AAAAAAABINI/7027zJf7kN8/s400/plugs.jpg)](https://4.bp.blogspot.com/-StjBYB5gZOE/Vh7DWYGKYLI/AAAAAAABINI/7027zJf7kN8/s1600/plugs.jpg)
https://abdulinnewzealand.wordpress.com/2012/12/03/new-things-from-my-visit-to-new-zeland/
If every team in the company uses different:
- libraries
- approach to testing
- approach to deployment
- approach to running the application
Then you can tell your business that they will pay A LOT of money for the support. The learning curve will be gigantic for the newcomers. But hey! It's better to code a new functionality in the meantime right?
Seemingly all the developers would like to see the effect of those migrations and standardization. Everybody wants this to happen but who should actually do it? When asked about this you might hear:
> I don't have time for this - it's not my problem. I've delivered my business feature and this is what I'm paid for. What you're referring to is not of my interest.
How can we solve this?
**Stupid idea**
Introduce the following flow of working in IT:
- the "coding team" writes a business feature and pushes it to master
- the "clean code team" rewrites the code according to the clean code standards
- the "technical team" introduces the technical standards for the written piece of code
- the "migration team" migrates the code from one approach to another
The outcome of the cooperation could look like this:
[![](https://2.bp.blogspot.com/-8PyO94v8WnQ/Vh7FQYoj_TI/AAAAAAABINU/NfuIHRnzdZQ/s320/bathroom.jpg)](https://2.bp.blogspot.com/-8PyO94v8WnQ/Vh7FQYoj_TI/AAAAAAABINU/NfuIHRnzdZQ/s1600/bathroom.jpg)
**Good idea**
Introduce... caring! Invest a lot of time and effort in educating business and developers that you have to take care of the code quality. Imagine where your company would be if every programmer would focus for 1 hour per day to manage the technical debt. If your managers don't understand the importance of clearing that debt, then you should consider changing jobs cause it's going to get worse with every single push to the repo.
#### You are an engineer!
[![](https://2.bp.blogspot.com/-pGZjd5My6EU/Vh7LQrT18oI/AAAAAAABINg/ku5r63yr3oY/s320/engineer.jpg)](https://2.bp.blogspot.com/-pGZjd5My6EU/Vh7LQrT18oI/AAAAAAABINg/ku5r63yr3oY/s1600/engineer.jpg)
Developing a feature is not just typing in code that compiles and makes the tests pass. Maybe the constant breathing of the project manager on your neck made you forget about this but you are an engineer. Following
[Wikipedia](https://en.wikipedia.org/wiki/Engineer)
:
> An
> **engineer**
> is a
> [professional](https://en.wikipedia.org/wiki/Profession)
> practitioner of
> [engineering](https://en.wikipedia.org/wiki/Engineering)
> , concerned with applying
> [scientific knowledge](https://en.wikipedia.org/wiki/Scientific_knowledge)
> ,
> [mathematics](https://en.wikipedia.org/wiki/Mathematics)
> , and
> [ingenuity](https://en.wikipedia.org/wiki/Ingenuity)
> to develop solutions for technical, societal and commercial problems. Engineers design materials, structures, and systems while considering the limitations imposed by practicality, regulation, safety, and cost.
> [[1]](https://en.wikipedia.org/wiki/Engineer#cite_note-bls-1)
> [[2]](https://en.wikipedia.org/wiki/Engineer#cite_note-nspe-2)
> The word
> *engineer*
> is derived from the
> [Latin](https://en.wikipedia.org/wiki/Latin)
> words
> *ingeniare*
> ("to contrive, devise") and
> *ingenium*
> ("cleverness").
> [[3]](https://en.wikipedia.org/wiki/Engineer#cite_note-3)
> [[4]](https://en.wikipedia.org/wiki/Engineer#cite_note-4)
So other than telling one again:
> I don't have time for this - it's not my problem. I've delivered my business feature and this is what I'm paid for. What you're referring to is not of my interest.
you should consider all of the technical aspect before even writing a single line of code. Then you should say:
> My schedule is tight but I'll fix the issues that you suggested. I understand that delivering business value means writing a feature and making the technical progress as a company. This is what I'm paid for and what you are referring to is part of my duties.
Unfortunately there is one problem with this approach...
#### Are you an engineer that has a say? You're gonna get fired!
Yes, if you start caring in a corporate enterprise you will eventually get fired. Business prefers people who nod their heads and agree to everything. After some time quality becomes a burden for the management. It becomes a cost that doesn't bring "business value".
So you will start fighting for the quality because this is the very meaning of your programming life. Deliver quality software that satisfies the business requirements, bearing in mind technical consequences. You will defend your developers against the growing pressure from the business to deliver features at a larger pace. The corporate axe will come closer to your neck with every single fight to defend the very meaning of being an engineer.
In the meantime your fellow developers that don't agree with your permanent interference in the key tapping due to buzzwords like "resilience", "fail-fast", "latency" or "tests" will continue to dislike you. They will constantly show their lack of support to what you're doing. Their mediocrity and lack of willingness to stand to what they believe in will allow them to remain in the company for years to come.
Then one day you will have to pack your stuff in a box and you will be escorted out of the office because you will get fired. The reason will be simple: "not delivering business value".
[![](https://4.bp.blogspot.com/-baNDD9nKPtQ/Vh7QTfvgBTI/AAAAAAABIN4/p1xdBPOtkrU/s320/guillotine.gif)](https://4.bp.blogspot.com/-baNDD9nKPtQ/Vh7QTfvgBTI/AAAAAAABIN4/p1xdBPOtkrU/s1600/guillotine.gif)
But... don't worry! That's actually good. Someone is doing you a favor! In the long run you will definitely profit from being fired. You will gain respect because you stood for your values. You will be able to stand in the mirror, look at yourself and say that you've done everything your power to do things properly with high quality.
#### Epilogue
Hopefully my apocalyptic vision is too harsh but that's what I see when talking to people in the industry. There is a light at the end of the tunnel though (and it's not a freight train).
There are companies that value good engineers and value quality. If you get fired (or you're getting close to that) just file a CV there. You can be shocked that the very sense of caring and eagerness to learn drastically boosts your chances of getting hired.
[![](https://1.bp.blogspot.com/-oFjV8za2yWM/Vh7M1xlgLRI/AAAAAAABINs/_yxMM4Gp_Vw/s320/the_end.gif)](https://1.bp.blogspot.com/-oFjV8za2yWM/Vh7M1xlgLRI/AAAAAAABINs/_yxMM4Gp_Vw/s1600/the_end.gif)
#### Additional reading
- [Living in the age of software fuckery](https://medium.com/@bryanedds/living-in-the-age-of-software-fuckery-8859f81ca877)
- [Don't call yourself a programmer](https://www.kalzumeus.com/2011/10/28/dont-call-yourself-a-programmer/)
