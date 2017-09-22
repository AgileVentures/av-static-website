---
title: NonInvasive Progress
date: 2017-01-26
tags: illness, cold, flu, blog, publication, legacy, refactoring, solo coding, upgrades, premium
author: Sam Joseph
---

![airplane vortex](/images/airplane_vortex_edit.jpg)

My cold last week meant there's a delay between the first publication of my daily blogs and the reviews I get from the AV community (particularly Federico).  I'm still going though.  My current routine is knock out a blog in the morning, and review a blog from the week before in the evening and push it to our nonprofits site.  Also in the evening I do a second review on a blog from a couple of months previously and re-publish on medium with edits and changes.  One I just pushed out was on a concept I call [NonInvasive Programming](https://medium.com/agileventures/noninvasive-programming-9821bc3c3f45#.gfps0rbmn).  I was calling it "DriveBy" coding for a while, but I decided I didn't like the violent connotations.

Things are coming full circle here, with me working on the same area of the codebase, again in a NonInvasive fashion, and this time with tiny solo efforts in between admin tasks.  By NonInvasive I am probably just channeling Kent Beck's:

> Do the simplest thing that could possibly work

Maybe I'm not adding anything to that, but I usually associated Kent's ["Simplist first" assertion](http://wiki.c2.com/?DoTheSimplestThingThatCouldPossiblyWork) with test driven design and creating things from scratch.  In my mind NonInvasive Programming is all about dealing with legacy code like that in WebsiteOne.  It's clearly a kindred spirit.  Am I adding any value by breaking out a new term?  Let's see.  The phrase is making sense in my head: to describe the process of handling the overwhelming sets of changes it seems we need to get us to a sustainable system.

My goal is to make it possible for Premium members to upgrade themselves from one Premium plan to another.  At the moment this is something I do manually through the Stripe dashboard for Premium members when they reach out to me through Slack.  That process is just fine at the moment, and really valuable as I get a little dialogue with the member about their needs and so on.  However it won't scale up if and when the number of Premiums ramps up, and I also want the interface to help make it clear to Premiums that they can upgrade.  It's funny, when you're working on something really intensely and talking to several folks about it each day, and blogging about it, and streaming your sessions talking about it on YouTube, it's easy to slip into assuming that everyone's following along :-)

Of course everyone has their own busy lives to take care of and the vast majority of them really don't have the time to follow all your blogging and video-casting, even if you could magically get it down into really pithy bytes of marketing content (although maybe that would help).  In one recent Slack conversation with a Premium, I nervously mentioned that we were doing mob programming sessions on Avdi Grimm's Confident Ruby.  I was nervous because I'm nervous about promoting our services to folks who might not want them.  The response was enthusiastic.  "I didn't know about that, where do I sign up?".  Perhaps I need to be a little more confident reaching out.

Maybe I should keep things purely manual until we get into the black (financially), but my intuition is that folks will feel more comfortable if they can press the upgrade button themselves; and if the upgrade button is there, then it's discoverable and that should help; and we also have some unsightly bugs in the system as we try to handle corner cases like some Premium members sponsoring others, and providing the ability for a sponsor to upgrade someone's plan, versus the individual doing it themselves.  So there's a lot to handle.  The previous NonInvasive sessions had been about just making it possible to upgrade from Premium to PremiumPlus.  The process uncovered various smells in the code, and also since then, we introduced a series of intermediate plans, and upgrading to them first makes loads more sense.

This next round of NonInvasive activity has involved splitting out several tickets:

* [Support generic Premium upgrade](https://github.com/AgileVentures/WebsiteOne/issues/1524)
* [Refactor card update out of subscriptions controller](https://github.com/AgileVentures/WebsiteOne/issues/1526)
* [Confusion about subscription vs single monthly payment](https://github.com/AgileVentures/WebsiteOne/issues/1521)
* [Stripe images and logos](https://github.com/AgileVentures/WebsiteOne/issues/1253)
* [Existing Premium users can be upgraded by anyone, but that leads to failure](https://github.com/AgileVentures/WebsiteOne/issues/1437)

The bigger picture here is the ongoing [Premium Epic](https://github.com/AgileVentures/WebsiteOne/issues/936) which actually shows the significant progress we've made in the last 6 months knocking off tickets and setting up a system that is currently covering half our running costs.  Now we've just got to cover the other half!  I think the key ticket is this [support generic Premium upgrade](https://github.com/AgileVentures/WebsiteOne/issues/1524) and the other three are bugs in the same general area, and a refactoring task to pay down some technical debt that I'm guessing will make progress on the main "upgrade" task simpler in the long run.  

Over the last two days I started punching out work on tiny slices of the bigger problem; little NonInvasive solo shots.  Desperately trying to avoid getting distracted by other little gremlins in the code, or at least making refactoring or bug tickets to record my ideas rather than diving in and fixing things.   I managed to adjust the upgrade so it upgrades to Mob from Premium, rather than to Plus, as a first round of improvement.  It doesn't address much of what we want to do, but it's a critical minor improvement as Plus is not highlighted as an available plan any more, so one user gripe is removed.  Additionally we've had many more Mob upgrades so having a button for this is more useful, and it serves to advertise the existence of mob a little more.  That gives me an idea --> [tooltips ticket](https://github.com/AgileVentures/WebsiteOne/issues/1530).  Am I shooting myself in the foot writing out all the tiny thoughts when I could be putting the time into programming?

The blogging about the whole process over the last six months is allowing me to look back and confirm the shape of the bigger picture I think I am seeing.  Yesterday I finally pulled the credit card components out of the subscriptions controller, paying down the technical debt that I hope will make upgrading smoother, and our controllers have a more maintainable RESTful sheen.  It's all so subjective - what's a simple change, vs. a trivial change.  I'm going to keep slicing and kicking out tiny PRs in between admin tasks.  Let's see if we can't get AgileVentures into the black one PR at a time!

###Related Videos:

* ["Martin Fowler" scrum](https://www.youtube.com/watch?v=20PkAsAs46I)
* ["Kent Beck" scrum](https://www.youtube.com/watch?v=gh283SIP7pI)
