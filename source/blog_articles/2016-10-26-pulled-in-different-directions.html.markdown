---
title: Pulled in Different Directions
date: 2016-10-26
tags: migration refactoring planning prioritizing hangouts API google usability
author: Sam Joseph
---

The PremiumPlus upgrade deploy appeared to go completely smoothly.  The test migration ran fine locally (with a copy of the production data).  Given we can't afford to completely replicate the production data on staging I took a risk and ran the migration directly on production.  It all went fine, and I created tickets for [removing the migration and old columns](https://github.com/AgileVentures/WebsiteOne/issues/1360), [updating the legacy Premium start dates](https://github.com/AgileVentures/WebsiteOne/issues/1362) and [making the upgrade button look nicer](https://github.com/AgileVentures/WebsiteOne/issues/1361).  The real test will be when a user tries to upgrade, or we get a new sign up, or someone tries to change their credit card.  All those cases are covered by feature tests that are green, but I won't rest easy till a few transactions have gone through on the live servers.

We also managed to deploy a fix to manual updates of event hangout URLs.  This is particularly important given that the [Hangout API is no longer working](https://github.com/AgileVentures/WebsiteOne/issues/1359), and so our hangouts cannot connect back to our server to provide an easy way into the hangouts.  Unfortunately the manual updates [only stay live for a couple of minutes](https://github.com/AgileVentures/WebsiteOne/issues/1358) at a time before resetting the event status.  This feels like the resurgence of an earlier bug, and I'm torn between working on this and on rolling out payment functionality for some new premium plans.  

At least yesterday, rather than burning time trying to take apart the Google Hangout API, I got the PremiumUpgrade feature out, and helped kick start the AsyncVoter project.  I already had the sense that the Google Hangout API issue was something on the Google side and might not have a quick fix.  This was clear from a [YouTube product forum thread](https://productforums.google.com/forum/#!topic/youtube/bww-BpJGAMQ) that Michael had found. The YouTube Live API support pages say that support is through StackOverflow, so I got a [question](http://stackoverflow.com/questions/40233393/start-a-hangout-on-air-button-for-youtube-livestreaming-api) posted, describing the precise details of our problem and linked in all the relevant resources.  Over night I see new posts in the forum with one user having digged deeper into the APIs and coming up against walls that only Google engineers will be able to fix.

It's a strange situation.  Google might fix this tomorrow and we'll be back to a stable setup, or that might just be it.  It's a bleed for us, because it means that realistically all the repeating events that countdown on our homepage will not have the correct hangout links, *unless* I can fix the manual link update to work correctly, *and* educate all the scrum masters to use it.  At the moment we are all pasting our links manually into Slack.   What we don't have is numbers on how many people come in through links posted to Slack (and the Meetup group) and those coming in through the site itself.  My sense is we do get some from all these sources.  Regular members tend to come in through Slack, but we've definitely met new great people coming in through the site or their  meetup group.

More frustratingly we also lose all our telemetry and Karma increments related to hangouts, because the Google Hangout plugin is not being loaded into the Hangouts.  With all due respect to everyone who worked on the code previously, the hangout/event_instance code is a gnarly mess.  I would anticipate possibly a days work to fix the issue with the manual update, and several days work to clean things up into a state where things were maintainable, and we could adapt to the new circumstances by ensuring that manually setting a hangout URL on the website would ping Slack and the meetup group, and reduce the admin load on the scrum masters.

Maybe this is an opportunity in disguise?   The problem with starting hangouts from the website is that it's not easy for others to learn how to do this.  FreeCodeCamp gets round issues like this by having well place animated gifs in their site that show people what to do.  Another alternative is operating things purely in Slack - there are various integrations with Slack that allow you to start hangouts or other video conferencing tools with commands like `/hangout`.  Frustratingly the hangout one does not support recordable hangouts on air.  I've always felt that the recording of our scrums and pairing sessions is an important part of AgileVentures' commitment to "Open Development"; but the flip side is that some are intimidated by the recording.  Then again perhaps we get our positive community vibe from people realising they are always on and so should be nice to each other.  I'm not sure.

A serious challenge is that we have lots of legacy documentation in AgileVentures and in the "Agile Development using Ruby on Rails" MOOC that talks about things in terms of the event/hangout creation flow that worked in the past.  We have lots of people who are familiar with that flow.  It's not necessarily an easily learnable flow.  We could keep approximately with the grain of that flow by having a manual update that pinged Slack/Meetup etc. and then it could even be extended to be started from Slack ... a fair engineering effort, for what returns?  It's unclear.

I was pleased to kick off some mob programming on the nodejs AsyncVoter project yesterday.  The ability to run asynchronous votes on stories bugs and chores in Slack channels has something that starting hangouts through web pages does not; you can easily watch other people doing it, and learn the approximate process.  Also, people don't all have to be available at the same time, so there's a lower barrier to entry.  You don't have to click a sign off on a warning saying "PEOPLE YOU DON'T KNOW WILL BE ABLE TO WATCH YOU!!!!!"   

Almost immediately there are tides that try to make the AsyncVoter project more complex.  Support multiple projects, support for a generic planning poker tool.  I do feel strongly that the challenges we have with maintaining our current infrastructure arise from having tried to add too many features too fast.  Moving tools we need simple tools that have a clear logic and set of domain entities at the centre.  In order to survive money has to flow; I think I must prioritize releasing actual payment pages for the new Premium Mob and Premium F2F offerings.  Beyond that I think we need to enable people to start voting on tickets with the minimum of effort and maximum of visibility.  This is the stepping stone to them taking on a ticket and putting in a pull request. It's through pull requests of code that all our members learn, and also how we fundamentally deliver value as an organisation.

Of course I have to go spend the morning with my accountant, but after that ...!

Related Videos:

* ["Martin Fowler" Scrum](https://www.youtube.com/watch?v=oCcWrBHqPfk)
* [AsyncVoter Mob session](https://www.youtube.com/watch?v=iUPcDHE7HUM)
* ["Kent Beck" Scrum](https://www.youtube.com/watch?v=feCOvYV6fN4)





