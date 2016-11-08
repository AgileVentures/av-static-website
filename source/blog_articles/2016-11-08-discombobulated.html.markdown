---
title: Discombobulated (by Google)
date: 2016-11-08
tags: youtube live hangouts HOA planning agile stripe paypal payment charity domain driven design DDD
author: Sam Joseph
---


Spent a good hour analysing our options to cope with the changes in the Google Hangout API.  The key issue is that the Google Hangout on Air button no longer starts a Hangout directly.  We have the the HOA button throughout our AgileVentures site on project and event pages:

![](https://www.dropbox.com/s/5esltpqdq8y2abt/Screenshot%202016-11-08%2009.23.04.png?dl=1)

Clicking this button now pops up a separate window with instructions on how to create a HOA through the YouTube interface.  BREAKING NEWS.  I just went to WebSiteOne project page to create a screenshot of the pop up for this blog and pressed the button.  Instead of opening an popup with the contents of this page:

https://support.google.com/youtube/answer/7083786?hl=en-GB

Which actually has a good straightforward description of how to create a HOA from the YouTube interface:

![](https://www.dropbox.com/s/gudo78qqtfk04bp/Screenshot%202016-11-08%2009.31.13.png?dl=1) 

However a HOA popped up, our plugin worked and pinged Slack through the AV website/agile-bot hookup.  To say I was discombobulated is to put it mildly :-)  I went straight to my [StackOverflow post](http://stackoverflow.com/questions/40233393/start-a-hangout-on-air-button-for-youtube-livestreaming-api) on the subject to see if there had been an update from the Google engineers, but nothing there.  So, had the activity on the SO post, or our discussion in the YouTube product forums prompted the Google engineers to make a change?  Or was this just another accidental change that will allow HOA buttons to work for a while, but will disappear again at some point in the future?

I guess Google moves in mysterious ways ... what we identified yesterday was that the hangout on air button not working was very confusing for many users.  The manual update of hangout URL was a work around that helped the more experienced AV members to notify the slack community about the hangout going live, but was unlikely to be used by new users.  I admitted that until recently I had been thinking we might want to move away from trying to support synchronous meetings, but the AV community has got really active recently, with well attended scrums, and Michael and I were agreeing that synchronous meetings were very good for coordinating some issues.

In our first session Michael and I had listed out some of the key problems with the HOA button change:

 - increased effort to start hangout on air
 - lower likelihood of notifications (and pair partners)
 - no telemetry
 - no sharing of video link
 - if no fixed event - no notification pathway --> pair now on each project page is basically non-functional
 - event live as function of planned duration rather than actual activity

We reviewed our vision of how the site should ideally operate:

* easy to browse existing sessions
* easy to join existing sessions
* easy to start a new session of your own --> notifications
* telemetry to give people credit for attending, allow us to analyse and improve

We listed out some short term options for improvement:

* [ ] more documentation on current state of system 
* [ ] build instructions on starting hangout on air into existing interface
* [ ] allow manual setting of youtube link 

and some longer term options

* [ ] reviewing alternatives videoconferencing software
  - [ ] GotoMeeting
  - [ ] Zoom
  - [ ] [Open Source](https://elearningindustry.com/top-6-open-source-web-conferencing-software-tools-elearning-professionals)
* [ ] publishing a new hangout app (give us notifications and telemetry)

It would be great if Google hangouts was open source ... anyway, even yesterday I didn't want to leap into working on something.  I also wanted to review the issues related to payment and premium plans.  CraftAcademy Sweden is still keen to work with invoices rather than credit cards, so we how had some premium members who weren't represented in the site correctly.  I tested on my local server that I could update them from the console like to:

```rb
UpgradeUserToPremium.with(user, Time.now, '', PaymentSource::CraftAcademy)
```

It worked locally and I ran the same through the production Rails console.  An example of me playing fast and loose perhaps?  Would a more principled approach be to deploy a rake task, build an admin interface or even an API endpoint? CraftAcademy just graduated three students and it was trivial for me to do through the Rails console, and make the system display the students' Premium status correctly.  I thought it was important to get this set, since otherwise they would have the "Sponsor for Premium" button.  Not that anyone was likely to start throwing money at them, but good to have the database in the correct state.  As the number of CA graduates increases, we'll want to automate this, but right now we're cautiously expanding the domain model.  Michael and I identified a set of other issues:

* Expiration date for CA sponsorship (notifications when 1 year support ends)
* CA sponsorship via Stripe
* Further PayPal payment integration
* Keeping track of how many pairing hours premium F2F and Plus members have left
* Supporting batch payments

I was pleased to have made the admin changes to the DB.  The Google Hangout API issue underlines the importance of working in small steps.  We're feeling our way with CraftAcademy collaboration.  The number of CraftAcademy graduates getting Premium membership bundled with their bootcamp experience could explode, or could stay at steady state, or even drop off.   It would be inexpedient to invest enormous engineering effort on functionality and extending the domain model until it's clear which way that goes.  In the meantime we need to keep knocking off small improvements.  CraftAcademy SouthAfrica (Raoul) was saying he'd really like to make batch payments in credit card.  Another feature.  We just had our first successful Paypal payment (7 day free trial for first paypal premium member just finished).  Naturally we shouldn't just implement every feature that every user asks for.  We've got to prioritise those that overlap and give us more bang for the buck.

I asked Michael what he'd like to work on and he said the "manual setting of youtube links".  We had another AV member join the hangout and tell us that was important for her project.  It made particular sense as it was a feature that would make our events more agnostic in terms of the video recording link.  We did a quick scrum and Michael drove on that feature.  Michael threw up a new feature, and we hammered at the EventInstances controller to see if we could provide a mechanism to manually update the video link for an EventInstance.  Things got a bit sticky.  I was torn a little between refactoring the Cucumber steps and getting things done.  I encouraged Michael as driver to take the route he thought best.  He left the background EventInstance generation steps alone, but tried some simpler versions for the individual scenario steps.  That didn't go smoothly and we saw why the original test writers had gone for executing triggers rather than pure Capybara clicks when things were getting stuck on the overlap.

There were other complexities with the app code trying to post to twitter, and the dynamics of when and if the hangout live elements were being displayed.  At the end of the session the tests were still red and the debug cycle felt a bit slow.  Over night I wondered if as part of migrating to a new domain model, we should prefer an EventCollection entity that consists of individual Events, and remove the terms EventInstance and Hangout from the domain.  The current model has the term Event representing both repeating and single "events" which can be scrums, client meetings, pairing sessions, anything where people get together.  The current codebase and tests use the term Hangout and EventInstance interchangeably which can be confusing.  An EventInstance holds a set of data about a Google Hangout at the moment.  I suspect there are also some cases where EventInstances get clobbered in repeat events started manually by the same person.  

I wondered if we might be able to pull together a more RESTful system by replacing our current Event entity with an EventCollection.  One of the Mentive students I was working with had used that aggregate approach successfully, and discussions about the AsyncVoter REST API made me think I'd like to be rebuilding parts of the WSO system more cleanly.  But how to do it one step at a time?  Perhaps introduce a placeholder term like `AtomicEvent` to represent single meetups; get a clean REST API around that and then finally migrate it to `Event` once the original `Event` entity was removed?  Sounds like it could be very confusing.  And now suddenly this morning the HOA button is working again, so suddenly we can leave that part of the system and focus on payments again?

Or maybe take a detour to AsyncVoter to make sure we get some sort of tracer bullet up?  Again it all comes back to how can be make this charity enterprise sustainable ...



###Related Videos:

* ["Martin Fowler" scrum](https://www.youtube.com/watch?v=Cuaol-FS9wM)
* [Reviewing WSO issues](https://www.youtube.com/watch?v=tSQztvcBQlY)
* ["Kent Beck" scrum](https://www.youtube.com/watch?v=Zp06E5u7neM)
* [Pairing on WebSiteOne](https://www.youtube.com/watch?v=eIc_bQxVUd8)

