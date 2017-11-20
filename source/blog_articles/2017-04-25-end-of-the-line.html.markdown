---
title: End of the Line
date: 2017-04-25
tags: Google Hangouts, Hangout API, sunsetting, retirement, planning, telemetry
author: Sam Joseph
---

![end of the line](/images/end_of_the_line.jpg)

Today is April 25th, the day that Google shuts down its Hangout API.  We've been watching this coming for several months, with Google enterprise support hinting at a replacement all the while.  Yesterday (April 24th) all the hangout apps stopped functioning and our scrum hosts were forced back to taking the hangout URLs and pasting them into the site to update the site link and notify Slack (as we had done during a previous Google Hangout API outage).  The "Start a Hangout on Air" buttons were still working, but are now mainly serving to cause the disabled hangout apps to load into the hangout, which requires (all?) users to click "cancel" a couple of times before the hangout will start sharing video or screen.

Maybe I've been derelict in my duty by failing to spend the last three months thoroughly researching and implementing an alternate solution.  The approach I've taken is to wait and see what's left after the Hangout API is retired.  It's frustrating.  We've poured hundreds of hours into the Google Hangout plugins, and the data we get from them underpins a lot of our site's Karma metric and our ability to analyse and track pairing and scrumming in the AgileVentures site and the edX MOOC.  The MOOC is just about to start again and realistically we'll have no telemetry on any pairing.  That said, I've just got an email saying that the whole AgileVentures site will have to be overhauled for accessibility if it's going to be linked to the edX MOOC.  Maybe going forward we just won't be able to have a direct link pointing our MOOC students to pair using our system.  Early days - lots to review and work out.

For the purpose of continuing the day to day business of AgileVentures: scrums, meetings and the occasional pairing session, we are still in principle able to use the hangouts.  It's a usability pain, but regular members will be able to learn how to update the events in the AV site manually.  It generates a series of action items:

* Inform members individually
* Update documentation in AV to indicate new approach to taking hangouts live
* Update documentation in edX MOOC about new strategy (or remove entirely)

Here's a screenshot of the interface element that allows anyone to update an event hangout link:

![interface element that allows anyone to update an event hangout link](https://dl.dropbox.com/s/m6soei7ibumpmym/Screenshot%202017-04-24%2016.16.49.png?dl=1)

At the moment we don't support a manual function for distributing the video link, which is something we might want to add.  That and the manual mechanism might make sharing the meeting links less arduous, but they don't address the tracking/telemetry elements that we're losing, or the awareness components that show who is in a meeting and whether it is still live. 

Google appears to be splitting Hangouts into two separate products, "meet" and "chat".  I can now start Google "meets" through my enterprise account.  They appear to be hangouts with all the apps and chat removed:

![Google Meet](https://dl.dropbox.com/s/xd8bp268seota88/Screenshot%202017-04-25%2009.50.08.png?dl=1)

As yet I can find no mention of an API.   I've applied for the Early Adopter Program (EAP) for chat, but haven't gained access yet.  I can guess some of Google's motivation for splitting the two products in terms of simpler offerings, but it goes in a completely different direction from what we want.  We'd like more tightly integrated chat and videoconferencing.

Maybe we need to back up and work out what's really mission critical for AgileVentures.  What's mission critical for me is delivering on the NHS project so I can feed my kids.  My intuition is that being able to start video events from Slack would be much more convenient and comprehensible for everyone.  It's possible and the de-motivator for me in the past is that those hangouts haven't been recordable; but maybe we need to drop that recording aspect?  The pros are that it's great for learning, openneess, accessibilty, catching up etc., but knowing you'll be recorded is a big barrier for some folks.  We've got some different technology options to pursue such as seeing whether we could use Zoom, or a hand-rolled WebRTC solution, but we've got limited development resources.  Before we start spending them I'd like to do a full week design sprint to prototype and test the next solution.

It feels a bit like the end of the line for the moment.  The AgileVentures events page [wasn't loading at all yesterday](https://github.com/AgileVentures/WebsiteOne/issues/1635) due to a 500 error we couldn't understand (some 3rd party service timing out perhaps?), but seems to be working today.  I guess change is the only certainty.  We've built a community using the free offerings that companies like Slack and Google have provided us, but it was always without guarantees.  Relationships with partners change, and mixed feelings are perhaps inevitable when those changes come, but relationships can't stay static forever.

### Related Videos

* ["Martin Fowler" scrum](https://www.youtube.com/watch?v=4pS97UiieBg)
* ["Kent Beck" Scrum](https://www.youtube.com/edit?o=U&video_id=vomDvVEfw_k)

