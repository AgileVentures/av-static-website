---
title: Meetings vs Coding
date: 2016-12-07
tags: Twitter developer account, legacy Twitter functionality, Tweeting Live Events, twitter notifications Background, factory girl, editing Wikipedia articles, youtube stream 
author: Sam Joseph
---

Tuesday suddenly sprouted a couple of meetings.  Well, the WikiEDU meeting had been planned from the week before, and gave WikiEDU developer Sage Ross the chance to tell AV about the WikiEDU project, which helps instructors set editing Wikipedia articles as assignments in class.  Sage heard about AgileVentures on RubyRogues and liked the idea and is bringing the open source non-profit project to AgileVentures.  WikiEDU is a Rails/React app that was developed by an agency and now needs maintainenance and also the development of new features.  See the full meeting in the [video of that session](https://www.youtube.com/watch?v=nfsWSIXbHRc).

The meeting with sponsor, drie, to review where we were with deploying to drie push came up more suddenly, but was definitely half an hour well spent as we got more of the desired workflow and endpoints for migrating the LocalSupport project ironed out.

```
https://www.harrowcn.org.uk --> drie instance (production)
https://staging.harrowcn.org.uk --> drie instance (staging)
https://develop.harrowcn.org.uk --> drie instance (develop)
```

There wasn't much time left in the day for coding.  Matt, Michael and I got in a hangout that Matt started to look at the new characterization test for the legacy Twitter functionality on WebsiteOne.  Matt had issues with the hangout (YouTube not hooked up) that meant that it wasn't recorded - we'll need to debug that.  We got VCR sandboxing the test with a new Twitter developer account for Matt. The cuke for the current functionality looks like this:

```gherkin
@vcr
Feature: Tweeting Live Events
  As a site admin
  In order to increase participation in events
  I would like live events to generate twitter notifications

  Background:
    Given following events exist:
      | name       | description             | category        | start_datetime          | duration | repeats | time_zone | project | repeats_weekly_each_days_of_the_week_mask | repeats_every_n_weeks |
      | Scrum      | Daily scrum meeting     | Scrum           | 2014/02/03 07:00:00 UTC | 150      | never   | UTC       |         | And an event "Scrum"                      |                       |
    And an event "Scrum"

  Scenario: Event going live causes tweet of hangout link to be sent
    When the HangoutConnection has pinged to indicate the event start, an appropriate tweet will be sent

  Scenario: Event stream going live causes tweet of the youtube stream to be sent
    Given that the HangoutConnection has pinged to indicate the event start
    And youtube stream has gone live
    Then an appropriate tweet has been sent # e.g. see live stream
```

And we uncovered various corner sad cases, such as when the tweet is too long.  I'm still desperate to de-couple the cukes from the steps that use the same factory girl elements as RSpec, but one thing at a time.  Michael and I took a stab at making progress on LocalSupport, but the drie https was flaking out and we didn't make much progress.  After the dust from all the meetings settled we did spend a chunk on AsyncVoter trying to move the tests to a point where we could support simultaneous voting in multiple channels.  It was short and we didn't introduce any new functionality, but we saw how Arreche had laid out an example Yada (Cucumber) test for us to play with, and we worked out how to use BotMock to check for messages in multiple channels.  I think I've convinced myself to stick with BotMock, particularly how Arreche has it loading through npm.  It's so interesting to see the parallel evolution of the same functionality in the npm and bundle stacks.

We were still left with whether we could get all the botmock stuff working with Yada, but I had to take my kids out to their evening's activity.  I managed to review the ProjectScope pull requests while they played and looked back on a day in which I'd kept my blogging to 20 minutes, managed to get out an AV emailing, push an old blog to social media and get a few things done.  I've got a stopwatch running to keep this blog time under 20 minutes!  And I'm going to see if I can't get Zapier to automate pinging Slack for me.  Another meeting today with a potential sponsor.  I'd rather be coding, but seems these meeting things are important to bring the money in ...


###Related Videos:

* ["Martin Fowler" Scrum](https://www.youtube.com/watch?v=xROu2SNKXmM)
* [LocalSupport devops](https://www.youtube.com/watch?v=DxuOMwiv2p4)
* [edu.wiki.dashboard project kickoff](https://www.youtube.com/watch?v=nfsWSIXbHRc)
* ["Kent Beck" Scrum](https://www.youtube.com/watch?v=VL0ZO1uLZuo)
* [Pairing on AsyncVoter](https://www.youtube.com/watch?v=w02Ey4Z8xvA)
