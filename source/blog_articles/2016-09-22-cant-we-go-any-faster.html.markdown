---
title: Can't We Go Any Faster?
date: 2016-09-22
tags: pairing tickets refactoring delegation incentives hangout SQL noSQL
author: Sam Joseph
---


Got the pairing times right today, but didn't get started as early as we could.  Having a family and working from home means that things can easily come up, like suddenly my eldest needs a new school approved calculator which is sold out on Amazon.  Anyway, we managed two and a half hours or so.  The new JavaScript vendor pipeline's looking good due to Michael's solo follow up work.  We created a small flurry of tickets of possible follow up issues.  We were close to sliding into working on them, and in the background Michael actually resolved one issue about un-needed JavaScript files before we'd completed an async vote on the subject.

It's become increasingly clear to me that it's counter-productive to stand on ceremony with these things.  Running the async votes is definitely an overhead that I would like to automate away, but let's see if perhaps we don't want to return to the kickoff/retro meeting.  I notice Armando Fox's students voting to [return to a lecture format](http://saasbook.blogspot.co.uk/2016/09/flipped-classroom-no-thanks-id-rather.html), but that's getting off my main point that if your pair partner or colleague has just got something working, it's not the time to say "you should have waited for the vote to complete" or make any similar objections.  There are lots of great heuristics like "not working on issues that haven't been voted on", but that's a suggestion, not a law to be enforced.  Working effectively is not about getting everyone to adopt your latest thinking on how things should be done, it's about accepting the way they work and finding a collaborative path.

I guess some people get away with "It's my way, or the highway" but that's not much fun, and what I really don't want is to *accidentally* go that way.  Anyhow, so I had a backlog of tickets in my mind, which I also got into the issues list:

* [Relace CoreJS-Typeahead (to remove bower dependency)](https://github.com/AgileVentures/WebsiteOne/issues/1281)
* [Investigate removing or replacing cubeportfolio.js/confy.js](https://github.com/AgileVentures/WebsiteOne/issues/1283)
* [Investigate options for other items in vendor/assets/javascript](https://github.com/AgileVentures/WebsiteOne/issues/1284)
* [Updating installation documentation](https://github.com/AgileVentures/WebsiteOne/issues/1286)
* [Replacing nearest_timezone](https://github.com/AgileVentures/WebsiteOne/issues/1287)
* [Investigate other possible sources of memory leak](https://github.com/AgileVentures/WebsiteOne/issues/1288)

So Michael had removed `cubeportfolio.js/confy.js` before the dust had settled.  We could have followed up on other items relating to the JavaScript vendor upgrade such as removing the bower dependency, or trying to modularise other libraries, but I was itching to make some different kinds of changes.  I closed out our existing memory investigation ticket and replaced it with two new ones - one for more investigation in general and another for removing a heavyweight gem.  There was also the issue of the install documentation.

The two other things on my mind were improving our hangout telemetry and the profile of our premium plan.  The former might allow us to better understand how the pairing sessions would go in this next run of the MOOC, while the later might improve our balance sheet. As we investigated, it seemed we didn't have tickets for some of the things that Michael and I had been regularly discussing, specifically for giving members Karma credit for [participating in, rather than just starting, hangouts](https://github.com/AgileVentures/WebsiteOne/issues/1290).  I had been thinking that this would require a refactoring of the way we represented hangout data that would also be part of improving our telemetry (which we also didn't have an explicit [ticket](https://github.com/AgileVentures/WebsiteOne/issues/1291) for).  In fact the ticket we did have was focused on improving hangout participant [identity tracking](https://github.com/AgileVentures/WebsiteOne/issues/1197).

To explain, from our hangout plugin, we get a hangout participants G+ id.  Those AgileVenture members who have authenticated with G+ on our site have a G+ id associated with their account.  For those members we can connect their presence in a hangout with their AV account.  This allows us to display more information about who was in a hangout in the [past events view](http://www.agileventures.org/hangouts).  In order to encourage people to link up with their G+ accounts we'd already increased the amount of Karma you get for making that connection.  A quick check on the production DB indicated that a third of our members were connected up.  We should have checked that number several months back when we made the change to give us a hint if the change had an impact.  Doh!

Given that we'd had a long period when the G+ signup was not working at all, I was pleasantly surprised to find a third of our members connected, and while I'm not sure if that's due to our change to the Karma, or fixing the G+ signup (although I guess I could do more mining to get more hints), it drew me away from the ticket's focus of increasing G+ identity match up.  Or at least it pushed me to say, we have the incentive in place, we'll need to make Karma more visible so that the incentive has greater chance of working.  I threw a load of thoughts about that into the ticket itself:

* [ ] G+ link up might improve if we give more credit for participating in hangouts #1290 potentially helps here
* [ ] perhaps more importantly we need more people to be aware of karma
  - [ ] linking karma number to breakdown on user page #1245
  - [ ] breakdown on user page is hidden in sub-tab
  - [ ] breakdown doesn't look so nice
  - [ ] need to mail members about their karma scores?  slack ping them?
  - [ ] tooltips to explain things

And I found myself making tickets that I thought should have already existed (I did search) on these things that Michael and I had been discussing a lot.

* [improve hangout telemetry](https://github.com/AgileVentures/WebsiteOne/issues/1290)
* [give karma credit for participating in a hangout](https://github.com/AgileVentures/WebsiteOne/issues/1291)

And Michael also started speculating about further [gamification](https://github.com/AgileVentures/WebsiteOne/issues/1292) of Karma, which I'd love to get into.  Anyhow, a little playing on the production DB showed that actually we could grab an indication of hangout participation without needing a big refactor (although it might give Avdi a coronary for its timidity):

```rb
EventInstance.all.select {|i| !i.participants.nil? && i.participants.values.count > 1 && i.participants.values.any?{|p| p['person']['id'] == "103524399391704001670"}}.count
```

The EventInstance (or hangout) class currently stores a text string representation of the JSON that comes back from the HangoutPlugin, which looks like this:

```rb
{"0"=>
  {"id"=>"hangoutA749C12C_ephemeral.id.google.com^6285825550",
   "hasMicrophone"=>"false",
   "hasCamera"=>"false",
   "hasAppEnabled"=>"false",
   "isBroadcaster"=>"false",
   "isInBroadcast"=>"true",
   "displayIndex"=>"0",
   "person"=>
    {"id"=>"108104108523167071445",
     "displayName"=>"Michael C",
     "image"=>{"url"=>"https://lh5.googleusercontent.com/-AtamIgns520/AAAAAAAAAAI/AAAAAAAAAAA/4TTnoJlntEM/s96-c/photo.jpg"},
     "qa"=>"false"},
   "locale"=>"en",
   "qa"=>"false"},
 "1"=>
  {"id"=>"hangoutEDC38696_ephemeral.id.google.com^4f0e2ab2d8",
   "hasMicrophone"=>"true",
   "hasCamera"=>"true",
   "hasAppEnabled"=>"true",
   "isBroadcaster"=>"true",
   "isInBroadcast"=>"true",
   "displayIndex"=>"1",
   "person"=>
    {"id"=>"103524399391704001670",
     "displayName"=>"Sam Joseph",
     "image"=>{"url"=>"https://lh3.googleusercontent.com/-Kt12k8aqqTs/AAAAAAAAAAI/AAAAAAAAAAA/TL0ZFPtM2Tc/s96-c/photo.jpg"},
     "qa"=>"false"},
   "locale"=>"en",
   "qa"=>"false"}}
```

It's stored using the serialise method, so we get back a Ruby object, which allows for the above code to work without requiring a parsing step.  Effectively we've got a micro noSQL database inside our bigger SQL relational database.  The computation over the entire dataset for an individual is not particularly fast, but it doesn't take more than a second, and since we're largely calculating Karma offline, it seems like we could get this into production relatively quickly.

The logic here is that we want to encourage hangout participation by giving Karmic credit for it, and in particular you'll need to be G+ up'd on our site in order to get the benefit, so a potentially happy side effect is an increase in the fidelity of our tracking, and improvements in the results of our data mining.  All this virtuous circle will hopefully just help us to help everyone to help charities, and of course ultimately save the world, haha.  Anyway, just as long as we get to play with cool tech along they way, eh? :-)

So I was still on the fence about what to do, when Michael pointed out that our Karma overview calculated all this directly in real time and could slow down the load of the user's profile page.  We talked about caching that in the User table.  Felt ill about how bloated that table has got, and the technical debt we were building up, which pushed me up to wanting to work on Premium and pull the Stripe components out of the user table; but then I had a breakthrough.  We could pull the Karma components into a separate table, cache them all, improve load times on the profile page, and also quickly deliver the incentives we wanted.  I could tell Michael wished I had been able to come to that conclusion without the previous 45 minutes of discussion, but I was still pleased with the tickets we had created and the ideas we had had.

In the remaining 45 minutes or so we managed to knock off a quick acceptance test driven creation of a separate Karma table. It also involved using a funky delegation pattern:

```rb
class User < ActiveRecord::Base

  extend Forwardable

  def_delegator :karma, :hangouts_attended_with_more_than_one_participant=
  def_delegator :karma, :hangouts_attended_with_more_than_one_participant

  has_one :karma

  ...
end
```

I'm sure there's a tighter way to do that, but it breaks up our mammoth User table, and we can now write a separate test for quickly calculating hangout participation offline, and then get on to the bigger refactoring to see details about when people join hangouts, break the user table up further with better support for Premium, and improve the installation documentation.  These are the key things for me in the next few weeks, although they could certainly be derailed if the memory issues turn out to be more serious than we thought.  The question remains, can't we go any faster?  One drag is me spending all this time with my kids.  Maybe I can get them to code for AV too? :-)

###Related videos:

* [Pairing part 1](https://www.youtube.com/watch?v=PliTRMoNrR8)
* [Pairing part 2](https://www.youtube.com/watch?v=6zO-FCmJQSk)
* ["Kent Beck" scrum](https://www.youtube.com/watch?v=_dXb5QASWeU)
