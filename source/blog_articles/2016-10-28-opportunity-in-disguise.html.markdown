---
title: Opportunity in Disguise
date: 2016-10-28
tags: node express mongodb hangouts youtube live api brainstorm solutions problems
author: Sam Joseph
---

The new plan payment end points are wending their way to production, and I stepped back to the AsyncVoter code which was now green following a mob session with Michael, Raphael and Alex yesterday.  I wasn't there so apologies if I left anyone out.  I know Junior, Joao, Junior and Chaiwa are following things closely.  The clear difference for me between a Node/Express app and a Ruby/Sinatra app is that I can pretty much fix any problem very fast in the latter.  I've done Node/Express here and there over the years, but nothing like the volume I have in Ruby.  Raphael also talked about things being slow going in their session, but they'd got everything green.  I think ultimately it had come down to making sure the correct database setup was going on in the `server.js` file:

```js
var mongoose = require('mongoose');

mongoose.connect('mongodb://localhost/asyncvoter');
var db = mongoose.connection;
db.on('error', console.error.bind(console, 'connection error:'));
db.once('open', function () {
    // we're connected!
    console.log("Connected correctly to server");
});

```

[https://github.com/AgileVentures/AsyncVoter/blob/master/bin/server.js](https://github.com/AgileVentures/AsyncVoter/blob/2f42dee39c727cd5b6e849ac955a818bc0faa6ae/bin/server.js)

This is the kind of thing that would benefit from some [athletic](http://philipmjohnson.org/essays/athletic-software-engineering.html) coding repetitions (reps).  I used to do those a lot with the students at Makers Academy, following from Philip Johnson's Athletic Software Engineering concept, where you repeat the same exercise over and over, building in muscle memory and getting your time down.  So for example I've repped generating a Sinatra app many many times, and a PORO domain model with RSpec hundreds of times.  We can also refer to these things as coding kata.  Often kata are just in the plain programming domain, but I think there's a lot to be said for kata that involve repeatedly building a Sinatra or Express app from scratch.  You encounter all the setup errors outside the context of trying to get something else done, which makes them less stressful.

Anyway, it has been a year or so since I was repping on node/express so it was great the others had sorted the database issues in a mob sessions while I was kicking out the premium plan payment endpoints for our Rails app in a solo session.  In the review session I pulled the code onto my machine and everything pretty much worked except a Rails app I still had running on port 3000 blocked the tests, leading to a new [issue](https://github.com/AgileVentures/AsyncVoter/issues/15) for the project.  So we got the PR patched up and our first feature completed for AsyncVoter.  While we were waiting for the CI to pass we got a couple of general discussion points out of the way.

One was the domain model terminology, where we were thinking to rename ["Stories" to "Ballots"](https://github.com/AgileVentures/AsyncVoter/issues/16).  I dashed off a quick domain model:

> Users cast individual Votes, on Ballots that concern Stories. Stories are uniquely identified by a URL, and will often have a Name, which is also the Name of the Ballot. The result of a Ballot will be when all the Users votes are the same, e.g. all 1s, all 2s, or all 3s.

I proposed that we didn't need explicit User or Story domain entities at this point, and that going simplicity first, we could probably get away with a single Ballot having a set of associated Votes.  We'll see if that gets adopted.  The other question was the cool Uncle Bob architecture that Joao had put in the spike where all the code was organised by domain entities.  It had the downside that some folks had been confused by the lack of a "standard" Express structure, with separate folders for models, controllers etc.  We opened a [ticket](https://github.com/AgileVentures/AsyncVoter/issues/17) for that.  It seemed like people didn't care too much either way and we agreed that if anyone felt strongly enough they could do a PR to adjust the structure, but do it before the project got too big.

So, lovely green shoots in the AsyncVoter project, although it may still be a little time before the logistical overhead of running asynchronous votes for other projects will actually be reduced.  Still, no crazy rush now that we're rotating who's running the asynchronous votes in LocalSupport and WebSiteOne.  In the process we're familiarising everyone with the process of asynchronous voting.  Avoiding the synchronous meetings that no one seems to enjoy; providing a lightweight mechanism for people to get involved in projects; leaving a visible trail of discussion in the slack channels so the project activity is clearly visible.  All good really.

That's in stark contrast to the ongoing absence of the Google Hangouts API.  The switch to YouTube Live does not prevent us from starting Hangouts on Air, which we can still do, we just can't seem to do it programmatically.   There has been no response from Google in the three days since I posted by my [SO question](http://stackoverflow.com/questions/40233393/start-a-hangout-on-air-button-for-youtube-livestreaming-api) and reading the YouTube live documentation it seems like it's focused on supporting those who want to start a video stream from their desktop, not start a Hangout where multiple people can congregate to collaborate.

We started to brainstorm the alternatives: 

* [twitch](https://dev.twitch.tv/)
* [room (for slack)](https://agileventures.slack.com/apps/A0F827L3S-room)
* (other slack meeting tools - [I see 20+](https://agileventures.slack.com/apps/search?q=video) )
* [zoom](https://zoom.us)
* [gotomeeting](http://www.gotomeeting.co.uk/)
* [live coding](https://www.livecoding.tv/developer/applications/webrt)
* build our own - [WebRTC](https://webrtc.org/)

We need to evaluate each of these in terms of what we need for AgileVentures, which includes:

* telemetry (plugins)
* stability
* recordable
* access via URL

Our AgileVentures Karma system is based on giving people credit for attending events, so we need telemetry.  The whole thing has to be stable, as it is difficult enough to get people together in a hangout.  When the technology is dodgy it's a huge barrier, and we're also committed to recording as part of our open development philosophy.  Also having a URL that will take you to the video conference makes it hugely easy to share with others to get them into the conference.  

Maybe we will have to end up compromising on one or all of these in order to continue.  At the moment we are limping along manually creating hangouts via the live events interface.  We are bleeding in terms of MOOC students not having support to easily creating pairing hangouts, and people finding it harder to join scrums.  The problems can be itemised as follows:

* manual work for scrum masters (posting links to slack etc.)
* no telemetry from hangouts (within Google button API our plugin no longer communicates to server)
* descriptions of how to start pairing sessions no longer work - MOOC folks and many projects folks confused - unable to connect

We came up with the following short term solutions:

* adjust WSO functionality to better support manual adding of link to hangout (?) (URL) --> ping slack (reduces some manual work)
* adjust WSO functionality to stay alive without telemetry
* add instructions for copying across URL
* hangout plugin - add ability to specific data to send telemetry back (will only work for people who previously ran hangout with plugin) --> no way to now inject hangout plugins?

and also had a few out of the box ideas:

* focus on asynchronous stuff?  scrums in text chat in slack? or something
* revenue stream from ads on youtube
* can we use the live event interface to start a hangout with selenium or something?

It felt good to be able to review all the issues in a group.  The sensible thing seemed to be to focus on the first three items in the short term solutions above.  Good support for manual URL addition would not tie us to Hangouts, which of course might completely disappear.  If only they were open source!  The key fundamental is really to be able to share a URL to the video conference through the various media that people are connected with.   I'll try and get to the manual URL hangout fixes today, and then next week we can keep evaluating all the alternatives.  It might also be time to focus on allowing people to start hangouts from Slack where the activity is watchable, repeatable and copyable.  It offers a much more lightweight learning mechanism than a web interface.  Real time text chat really has the huge advantage over the web that you can immediately ask folks how they did anything, whether that's starting or voting in an Asynchronous vote, how they started a hangout etc ...  

What's been so great to see this week is the AgileVentures members working hard to get round the problems, helping each other, brainstorming solutions.  Maybe this truly is an opportunity in disguise ... Okay, time to code! 


Related Videos:

* ["Martin Fowler" Scrum](https://www.youtube.com/watch?v=DyLL5_QxLkU)
* [AsyncVoter Review](https://www.youtube.com/watch?v=Zmt8FjqMTLE)
* ["Kent Beck" Scrum](https://www.youtube.com/watch?v=DyLL5_QxLkU)