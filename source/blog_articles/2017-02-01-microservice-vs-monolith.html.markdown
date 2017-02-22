---
title: Microservices vs Monolith
date: 2017-02-01
tags: rails, DHH, DevOps, honesty, profanity, public speaking, facebook
author: Sam Joseph
---

Full on day yesterday with Rails creator David Heinemeier Hansson (DHH) dropping by to chat to AgileVentures and CraftAcademy, as well as other wall to wall meetings and DevOps.  Big shout-out to AgileVentures CoFounder and CraftAcademy boss, Thomas Ochman, for arranging and chairing the session.  It was great to hear from DHH in person and he was on form with his usual colorful language.  Thomas pointed us recently to a [paper that indicated a positive correlation between profanity and honesty](http://journals.sagepub.com/doi/full/10.1177/1948550616681055). DHH said things like:

> everyone starts out being a shitty programmer writing shitty code, but the great thing is that you don't have to stop there

and went on to tell us about how he looks back disfavourably at code he wrote in the past.  I believe the point that DHH is making is that it's okay to start slow and make mistakes and that's just part of the process.  To his great credit he elaborated about that eloquently and effectively, and I think despairingly about my own public speaking skills.  I guess I should think that everyone starts out being a shitty public speaker giving shitty speeches, but the great thing is that you don't have to stop there.  What worries me is how I've been talking too fast and cutting myself off in the middle of sentences for years :-)

If we believe the academic study mentioned above, perhaps the use of profanity indicates there's greater honesty in statements involving profanity, or by those who frequently profane, but if I'm honest I have to admit that profanity makes me uncomfortable.  I think that says more about me than it does about anything else.  I associate profanity with anger.  I swear when I'm angry, as my father did when he was angry.  I avoid it otherwise, as did he.  I find intense anger unpleasant and very sticky mentally.  If someone gets angry at me I find I am thinking about it for days.  DHH did not come across at all angry and was making a great point that I heartily agree with; even if I might have chosen slightly different words :-)

It's fascinating to look at how the authors of the academic study were measuring honesty in the facebook posts they were analyzing:

> The honesty ... was assessed following the approach ... that liars use fewer first person pronouns (e.g., I, me), fewer third-person pronouns (e.g., she, their), fewer exclusive words (e.g., but, exclude), more motion verbs (e.g., arrive, go), and more negative words (e.g., worried, fearful). 

This apparently comes from liars trying to dissociate themselves from the lie and so avoiding self-reference; preferring concrete over abstract language; using simpler language to reduce the mental load from generating the lie and using more negative terms because they are feeling uncomfortable about the lie.  Particularly interesting is that this entire analysis is entirely about expressions that people make on facebook, but enough of that rabbit hole.  Even more interesting to me was that DHH said:

> only use micro services when no one person can understand the single app - don't use micro services until you are ready to declare that "intellectual bankruptcy"

Which is a great point, but I wondered if that still applies when you have lots of part time, volunteer folks rather than a full time team, where everyone can devote themselves full time to understanding the entire system.  After the meeting, I posed that question to the AV community in Slack.  Long time AV contributer and software magician, Bryan Yap, responded that:

> every project still has a project lead, I imagine the project lead would know a considerable amount about the app. I think it is more for when the app grows too complicated for any one person to comprehend
 
> it seems to be a common misconception that micro-services would solve a lot of the problems in an app, speaking to people more experienced than I am suggests the opposite :-) micro services only amplify the issues that already exist within an application. Micro services are good at solving the organisational issues as DHH mentioned, but technically it is much more difficult to manage

Bryan went on to say that he didn't see any benefit from moving from a clean monolith to micro services, because you just distribute your system and start to introduce consistency problems.  Of course that assumes you have a clean monolith, which can sometimes be a big ask. To me the biggest danger would be to say a priori that either a monolith or microservices is necessarily better. There are pros and cons for both, but what I was really trying to explore was DHH's statement about not breaking the monolith until one person can't fit it in their head. I think individuals have different capacities to fit things in their head depending on how much time they can devote to a project ...  As the conversation continued, Arreche chimed in with:

> Wise people coincide that it is safer starting with monolithic and move later to microservices when and if it is needed.
But this doesn't mean that we cannot learn and experiment with microservices building not critical things. The AV community is a great place to have this kind of experiences indeed :slightly_smiling_face: (edited)

To which Bryan responded: 

> yeah experimentation is good :slightly_smiling_face: but from experience, moving to a micro service when you are not ready is very painful. Having them in a single database is not necessarily a bad thing, it's much faster to manage updates across the entire system because it is all in one place. When you need to make changes that span multiple projects with different teams… thats when all the problems kick in :disappointed: so having a good domain model and setting the right domain boundaries is necessary before moving to micro services

I replied to Bryan that that was good advice, and that single databases are great in the right circumstances and getting that good domain model is key.  In my experience the difficulty is working out that domain model, since it requires a process of exploration.  In addition, if you have a project with a lot of part-time occasional folks who don't necessarily have the time or inclination to understand the domain model, then it maybe makes sense to have a series of smaller services, each with a simpler model that they're more likely to grasp.

I think Bryan's point that there is usually one full-time maintainer who has the whole app in their head, but even if they do, I don't think that's necessarily the best criteria to make the decision to start splitting out some microservices. The key distinction I was trying to draw is that the best approach for a project where you have 6 full-time programmers on staff, may not work quite so well when you have one part-time person and 30 volunteers putting in bits of code here and there.  Sometimes splitting those 30 folks up into 6 teams of 5 each working on a different microservice might actually be simpler.  Of course as Kent Beck says, it depends :-)

###Related Videos:

* ["Martin Fowler" Scrum](https://www.youtube.com/watch?v=dtr_Um8J0XU)
* [Marketing meeting](https://www.youtube.com/watch?v=-dQWAbjr7pc)
* [Chat with DHH](https://www.youtube.com/watch?v=I0LJTMgEomM)
* ["Kent Beck" Scrum](https://www.youtube.com/watch?v=RZf-mhm20gk)

###Related Links:

* [Frankly, We Do Give a Damn: The Relationship Between Profanity and Honesty](http://journals.sagepub.com/doi/full/10.1177/1948550616681055)
* [Martin Fowler on MonolithFirst](https://martinfowler.com/bliki/MonolithFirst.html)
