---
title: Longer than Expected
date: 2016-09-23
tags: pairing tickets refactoring domain model hangout SQL noSQL sqeuences factorygirl driveby factory
author: Sam Joseph
---


Adding the function to give Karma credit for attending hangouts took longer than expected.  Ironically spiking it had been fast, but adding a test for it was time consuming and frustrating.  I'm trying to dissect why that was.  Looking back I can see a few things that caused us to spin our wheels:

1) the EventInstance FactoryGirl was creating a participants structure that was different to our assumptions
2) the `after_validation` hook on user causes the karma calculation to take place during FactoryGirl object creation
3) the EventInstance FactoryGirl was creating an additional user object
4) the version of FactoryGirl we are on (4.5) does not [reset sequence integers between test runs](https://robots.thoughtbot.com/getting-sequential-a-look-into-a-factory-girl)

Let's have a quick look at the original EventInstance FactoryGirl factory:

```rb
FactoryGirl.define do
  sequence :participant do |n|
    [ "#{n}", { :person => { displayName: "Participant_#{n}", id: "youtube_id_#{n}", isBroadcaster: 'false' } } ]
  end
  sequence :broadcaster do |n|
    [ "#{n}", { :person => { displayName: "Broadcaster#{n}", id: "youtube_id_#{n}", isBroadcaster: 'true' } } ]
  end

  factory :event_instance do
    transient do
      created Time.now
      updated Time.now
    end

    sequence(:uid) { |n| "uid_#{n}"}
    sequence(:title) { |n| "Hangout_#{n}"}
    sequence(:category) { |n| "Category_#{n}"}
    hangout_url "http://hangout.test"
    sequence(:yt_video_id) { |n| "yt_video_id_#{n}"}

    project
    event
    user

    participants { [(generate :broadcaster), (generate :participant)] }

    created_at { Time.parse("#{created} UTC")}
    updated_at { Time.parse("#{updated} UTC")}
  end
end
```

Note the creation of the participants field to be an array consisting of a broadcaster and a participant.  Notice also the use of sequences to generate that participant and broadcaster.  Without going into all the details I think we fell a foul of working with a legacy test and legacy object that had more interrelationships than we expected.  It's not quite a mock train wreck as there are no mocks here.  Effectively what we were working on after our acceptance test of pulling some Karma data into a new AR model, was an integration test, which had more moving parts than we anticipated.

It makes me think of Avdi's "MacGyver method" where we got wrapped up in using the existing tools, rather than writing our own compelling narrative.  My own "drive-by" coding strategy is often one of just going with the existing grain to make the smallest change possible, so I'm definitely one who can get wrapped up in the existing tool set.  Maybe we would have done better to complete the planned refactoring of the EventInstance participants field, which as I mentioned is effectively a micro-noSQL database inside our SQL database.

I think we might also want to look at our pairing strategy.  I think Michael was getting frustrated at the speed with which I switched from stack trace to code and back again as we tried to hunt bugs through the legacy test infrastructure.  There's the lag of the hangout screenshare as well, but I think it's more an indication of my keyboard hogging.  We seem to have fallen into a pattern of switching roles about once an hour, and I wonder if that's too infrequent?  There are other issues with me having less available time to code due to family commitments.  I wonder if we might benefit from having an enforced 10min switch as I've seen in some companies.

There's also what we are doing to the domain model.  I'm still conflicted about the dissonance between the terms "EventInstance" and "Hangout".  EventInstances do represent data from Google Hangouts in our system.  EventInstances is nicely generic - maybe in the future we'll support alternatives to Google Hangouts, but our hangouts are very often associated with events, but not always.  Actually Michael and I mainly pair in spontaneous hangouts that are associated with the project, so EventInstance is a misnomer there.

We were feeling good about pulling Karma out of the User table, and given that we have a Karma calculator it certainly seems like we are moving towards evolving a Karma entity in our domain.  It occurs to me that all the logic in the KarmaCalculation service could be moved to the Karma model.  Much as I love recombinable services, given that we now have a need to persist the intermediate calculations from the over Karma Calculator I'm moving to think that the sensible next step is a real "unit" and not "integration" test of the Karma model so that we can avoid getting bogged down in too much complexity, i.e. dependencies on many other elements of the system.

Following that logic, it would be nice if the Karma calculation could avoid depending on the structure of the data coming back from the hangout, i.e. that lump of JSON that gets transformed into a ruby data-structure by the serialise method.  That's a bigger operation which might involve developing a domain model like so:

```rb
class Participant < AR::Base
  has_many :hangouts
  belongs_to :user
  has_many :hangout_events # join at x time, leave at y time
end

class Hangout < AR::Base
  has_many :participants
  belongs_to :initiator, class_name: user
end
```

At the end of days pairing we had got the tests green, but not without removing the `after_validation` Karma calculation from the User class, and having to adjust a number of other tests to adapt to how we had adjusted the EventInstance FactoryGirl participant model.  Part of the issue there was that the serialise operation gives us "indifferent access" to the returned data structure, i.e. strings and symbols are interchangeable.  That was only part of the difference between the data structure the Factory was creating, and that real data-structure coming back from the DB.  That pushes me to want to refactor away from that, but that's a bigger task.... 

We were then asking ourselves how to slice and dice to try and stick with our "drive-by" methodology.  What was the simplest thing we could release?  And what activities should be pushed off into other tickets.  The following three seemed sensible other tickets.

* remove karma total from user table
* create all the other karma breakdown elements in karma table
* ensure that any reference to these throughout codebase is linked correctly to karma table

In principle the work of the day could be a PR, and the only additional thing required to release it would be the creation of a separate Karma update task.  We'd be losing the feature that Karma would be updated whenever users saved their profiles, but that seems like something we could easily add back in later, without using these dangerous AR lifecycle hooks.

The real question would be should we get that simpler PR in, and then immediately start more tickets based on it?  The danger there as we'd seen before, is that particularly with the new "squash and merge" feature on GitHub PRs, building on an unmerged PR would guarantee merge conflicts down the line.  That would seem to point us towards reviewing all the tickets and working on a different corner of the codebase until this got merged.  That feels challenging in that I'm now itching to do that bigger participant refactoring, and try and get some clean confident code.

We'll see - I guess after a day in which a lot of your assumptions are thrown it makes sense to allow a bit of time for the dust to settle.  Reflect on what worked and what didn't work, and maybe work on something else before coming back to the code face ...



