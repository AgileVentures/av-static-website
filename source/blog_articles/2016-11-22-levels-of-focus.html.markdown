---
title: Levels of Focus
date: 2016-11-22
tags: eliminate simplify delegate automate mentive drie premium agile ventures onboarding planning prioritising profiling measuring 
author: Sam Joseph
---

It was a busy weekend.  That was partly the kids' birthday parties and coaching under eights football, but also because of my last Mentive mentoring session and two Premium Plus pairing sessions.  I usually prefer not to work at the weekend, but I really want to make AgileVentures sustainable, and for people who work during the week, it's often convenient to get support from their mentor at the weekend.  The Mentive experience has been interesting to try out GotoMeeting, which is okay, and more powerful than Google Hangouts, but the ability to share a URL with a hangout to anyone, and stream/record live to YouTube, makes me prefer Hangouts.  

As the Mentive chats have become more abstract the Premium Plus pairing sessions have been very code focused. On Sunday I worked with Adrian through a Cross Origin Resource Sharing issue on AsyncVoter so that the new React client could read data off the "[drie push](http://push.drie.co)" hosted app.  We got a spiky [Work in Progress(WIP) PR](https://github.com/AgileVentures/AsyncVoter/pull/77) in and I deployed the branch to the drie staging instance so that Adrian could proceed to work on the [React UI](https://github.com/AgileVentures/asyncvoter-ui).  We'd used the [cors package](https://www.npmjs.com/package/cors) to ensure that we would accept incoming AJAX requests from all sources for the time being.

I had a quick break and was then working on a [running median problem](https://www.hackerrank.com/challenges/find-the-running-median) which involved writing some min and max heaps in Ruby.  Simple sort was deemed too inefficient and we needed a custom data structure to allow fast access to the median.  This was just part of looking at algorithms in general, but I was already thinking about how we could be linking this to useful stats from data-feeds we have on voting and pairing sessions.  We didn't get as far as I would have liked into the analysis portion and we'll hit that next weekend.

There hadn't been programming or pairing of note either side of the weekend on Friday or Monday, Friday having been taking up with an Agile Projects workshop and then some brainstorming with Michael and Ashley.  There'd been sporadic async slack bot hacking, and we got most of the way of pushing a version of LocalSupport onto [drie push](http://push.drie.co). Brainstorming finished up the day as Michael and I digested the new PRs and issue comment from Ashley on the generic [AgileVentures](https://github.com/AgileVentures/AgileVentures) repo.  Ashley had created some great diagrams, and was pushing us to be thinking in terms of more specific user models and big vision statements:

![Strategy, Goals and Impacting Users](https://cloud.githubusercontent.com/assets/673794/20459697/54701236-ae7f-11e6-9fe1-0e968a7581e2.png)

There'll be enormous value if the big picture vision of AgileVentures can be communicated effectively in a compelling and visually attractive manner.  I find myself torn between all the small admin tasks that are ever increasing, smoothing off the rough edges on aspects of the paid premium user experience, delivering on our commitments to the premium users and conducting bigger planning and vision generation efforts.

It's clear that the onboarding experience is bumpy and could be vastly improved at each stage of our funnel, from becoming aware of us, to browsing our website, signing up, joining slack, joining a hangout, participating in a project and starting a project; not to mention signing up for one of the paid plans, and upgrading to get even more professional development support.  Michael and I spent an hour kicking ideas about.  I was reminded of Pavel's point from a couple of years ago about the importance of measuring things--that if we were going to make changes like having the popups on the site encouraging people to join slack, or join hangouts, then we wanted to know if these were having measurable impacts.  However I keep thinking that some things are no-brainers, but then I'm a complete hypocrite.  The whole point of small simple PRs is not assuming that you know how things are going to work.

At the same time, perhaps I am justifiably cautious about efforts to pull out lots of data, where it's unclear what that data is telling us.  We could have a super active slack and super active hangouts, but without enough income it doesn't really mean anything.  I need to get the income past a certain threshold soon.  That's not the best place to be coming from, not the best place to be doing decision making.  It feels difficult to devote time to big picture planning that could be spent on, say, making sure that all the different premium plan options were available for upgrade on each user page.   I do want to make it easier for people to onboard and understand what we're doing, and how they can get involved and derive the most possible benefit from the AV experience.  That said, it feels counterproductive to focus on that when there are directly income related things to fix.  It's so difficult to know.  Will fixing the plan upgrade issue generate more revenue in the short term?  One user encountered a problem related to it, which we fixed through the Stripe interface.  Will others experience it soon?

We're planning our first Premium Mob session on Thursday.  That feels like it might have legs.  If that goes well and generates good energy it could be a powerful incentive for upgrading.  Coming full circle I think it comes back to Eliminate, Simplify, Delegate, Automate.  The mounting admin tasks are not helping me focus on the big picture.  Michael and I started looking at setting up a greeter bot to replace some of my AV slack greeting activities.  It seems on the Slack free tier we can only have one integrated bot, so any greeter bot would have to be merged with the AsyncVoting bot ... ultimately I resolved that the key thing to do today was to profile where I was spending my time and focus the lens of Eliminate, Simplify, Delegate, Automate on it.  I guess I have to measure as well ...

###Related Videos:

* [Premium Plus with Adrian](https://www.youtube.com/watch?v=YGl5BA6o43Y)
* ["Martin Fowler" Scrum](https://youtu.be/Mcm_uYE1vFM)
* [Bug Hunt on WSO](https://www.youtube.com/watch?v=u-g16rhSBUo)
* [Deploying LocalSupport to drie](https://www.youtube.com/watch?v=OudkpuKQ7Aw)
* ["Kent Beck" Scrum](https://www.youtube.com/watch?v=kNOigf5jLYI)
* [AgileBot Deploy and BrainStorming on WSO](https://www.youtube.com/watch?v=uvOFUDRbEmM)



