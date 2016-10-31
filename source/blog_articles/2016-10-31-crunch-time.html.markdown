---
title: Crunch Time
date: 2016-10-28
tags: Cucumber hangout API button rspec rails delorean TDD BDD UX UI
author: Sam Joseph
---

It was crunch time.  No answer from Google on the Hangout Button API.  It was dismantle our events framework or make some changes to the manual update functionality that would allow some parts of joining an event to start working again.  The problem was that even though our hangout plugin was automatically loaded into Hangouts started via YouTube, that hangout would only load for people who had previously run a successful AgileVentures hangout, and even then was now not set to communicate back to the AgileVenture's server.

The current architecture would send notifications about a hangout on Slack and provide a link to that hangout on the AgileVenture's site *if* the hangout was started from a button on the AV site, and our custom plugin was loaded into the hangout with the right data payload.  Maybe we could adjust the plugin to be [installable](https://developers.google.com/+/hangouts/publishing) into a YouTube started hangout and then connect back to our server to support the notifications and link display.  Certainly apps like [MadEye](https://twitter.com/_madeye) used to work like that, but they've shut down and who knows how long Google will continue supporting the Hangout Plugin ecosystem.  It was clear from the discussions on Thursday that it made sense to start by enabling users to manually specify the hangout URL; have that act of manual specification lead to Slack notifications, and keep the links to the hangout on AgileVentures live for the duration the event.

![example of a live event link in AV](https://www.dropbox.com/s/5vseylfvnfeufd5/Screenshot%202016-10-31%2009.46.02.png?dl=1)

The manual update of the URL had been broken for repeating events; a bug I had fixed earlier in the week.  Now the trouble was that following a manual update the event would only stay "live" for 2 minutes.  As Michael pointed out this was because when the whole system was working the hangout plugin would be sending back telemetry every 2 minutes and would keep the event "live".  Effectively we wanted the site to be able to display "live" events accurately, so that people thinking about joining would be receiving accurate information about whether to expect others in the hangout.  All this was still short of my imagined ideal set up where you are able to see who is in a live hangout before you join.  This functionality was briefly available on the helpdesk hangouts we used to have in the "Agile Development using Ruby on Rails" MOOC, however it never worked for Hangouts on Air.  If the Google Hangout API had stayed stable, it's something we might have been able to add fairly soon.  As it is now we're just scrambling to keep existing features running.

Maybe I'm deluding myself about this, but I really do imagine that an infrastructure that allowed you to browse a set of collaborative events, seeing a preview of what is going on and who is participating, could recreate some of the wandering, networking aspect of an unconference or similar, but support it completely remotely. (maybe I should do a UI mockup ...).  Anyway, let me tell you how I fixed the manual URL update to support "live for the duration" and slack notifications.  I started with a cucumber test, splitting the existing edit hangout URL tests into a separate feature file so that I could reduce the dependencies between the background steps.  Long cucumber files with multiple scenarios relying on the same set of background set up start to feel almost as dangerous as tests relying about a large set of fixtures.  Here's the new feature file header:

```gherkin
Feature: Manual Edit of Hangout URL
  As a person involved in an event
  So that I can ensure everyone can access the correct link to join an event
  I would like to have means of editing the hangout URL
```

And here's the smaller(!) set of background steps I finally converged on:

```gherkin 
  Background:
    Given the date is "2014-02-04 06:59:00"
    And following events exist:
      | name          | description          | category      | start_datetime          | duration | repeats | time_zone | repeats_every_n_weeks | repeats_weekly_each_days_of_the_week_mask |
      | Scrum         | Daily scrum meeting  | Scrum         | 2014/02/04 07:00:00 UTC | 15      | never   | UTC       |                       |                                           |
      | Repeat Scrum  | Daily scrum meeting  | Scrum         | 2014/02/03 07:00:00 UTC | 15      | weekly  | UTC       | 1                     | 15                                        |
    And the following hangouts exist:
      | Start time          | Title        | Project    | Category | Event        | EventInstance url   | Youtube video id | End time            |
      | 2012-02-04 07:00:00 | HangoutsFlow | WebsiteOne | Scrum    | Repeat Scrum | http://hangout.test | QWERT55          | 2014-02-04 07:02:00 |
      | 2014-02-05 07:00:00 | HangoutsFlow | WebsiteOne | Scrum    | Repeat Scrum | http://hangout.test | QWERT55          | 2014-02-05 07:03:00 |
    And I am logged in
    And I have Slack notifications enabled
```

These are irritatingly long at the moment partly due to the need to specify the `repeats_every_n_weeks` and `repeats_weekly_each_days_of_the_week_mask` fields on Events.  Trying to move fast I wasn't about to start refactoring that, although I'd love to rewrite both of the above `following entities exist` steps to avoid using factories that are shared with our specs, and make the whole thing more readable.

I drove from the simpler non-repeating event situation:

```gherkin
  @javascript
  Scenario: Edit Hangout URL and ensure event stays live
    And I navigate to the show page for event "Scrum"
    And I open the Edit URL controls
    And I fill in "hangout_url" with "http://test.com"
    And I click on the Save button
    Then I should see link "Join now" with "http://test.com"
    And I jump to one minute before the end of the event at "2014-02-04 07:14:00"
    And I navigate to the show page for event "Scrum"
    Then I should see link "Join now" with "http://test.com"
```

Naturally this was failing due to no updates from the plugin, and so I dropped to the RSpec level and drove the creation of a new field on EventInstance called `url_set_directly` to serve as a flag to indicate that the hangout URL had been set manually. I'd been struggling with the domain entity name of EventInstance, which we use to represent a Hangout, and the names are used interchangeably throughout the codebase.  I had been thinking we should consolidate on Hangout, but actually now the future of Hangouts is up in the air, EventInstance starts to feel like the better choice, although surely there must be a better term?  Anyhow, don't get sidetracked making domain model tweaks when you're trying to get a fix into production!  I RSpec'd in the EventInstance spec like so:

```rb
describe EventInstance, type: :model do

  it 'has url_set_directly default to false' do
    expect(hangout.url_set_directly).to be_falsey
  end

  context 'hangout_url is present and is not finished' do
    before do
      hangout.hangout_url = 'test'
      hangout.hoa_status = 'anything'
    end

    context 'url manually overridden' do
      before { hangout.url_set_directly = true }

      it 'reports live if the link is older than 2 minutes, and event duration not expired' do
        allow(hangout).to receive(:within_current_event_duration?).and_return(true)
        allow(Time).to receive(:now).and_return(Time.parse('10:02:01 UTC'))
        expect(hangout.live?).to be_truthy
      end
    end
  end
```

which naturally failed since there was no such field as `url_set_directly`, which lead me to generate this migration:

```rb
class AddUrlSetDirectlyColumnToEventInstance < ActiveRecord::Migration
  def change
    add_column :event_instances, :url_set_directly, :boolean, default: false
  end
end
```

And made the following changes in the EventInstance class:

```rb
  def live?
    started? && hoa_status != 'finished' && (updated_within_last_two_minutes? || manually_updated_event_not_finished?)
  end

  private

  def manually_updated_event_not_finished?
    url_set_directly && within_current_event_duration?
  end

```

The spec's passed, although not the cukes.  Notice I'd stubbed the EventInstance (hangout) in the spec to allow calling `within_current_event_duration?` on EventInstance objects.  I wanted to be able to ask that of an EventInstance instance, and I should be able to get that supported with another spec to drive the creation of a delegation:


```rb
describe EventInstance, type: :model do

  it { should delegate_method(:within_current_event_duration?).to(:event) }
```


```rb
class EventInstance < ActiveRecord::Base

  delegate :within_current_event_duration?, to: :event
```

I'd like to say that with that I got he Cukes green, but there was an additional change in the view (to set the url_set_directly flag) and then a good hour of getting lost in the time elements of Event, the IceCube scheduling gem, and ultimately I think it was all down to slight incorrectness in the way I was setting the dates in the Cucumber steps.   Ultimately it was all passing, and I got a [PR](https://github.com/AgileVentures/WebsiteOne/pull/1370) in which Raoul deployed and we tested in staging.  It seemed to keep the event live, but Slack notifications were not going through.  That took the addition of several more parameters to the view, and another [PR](https://github.com/AgileVentures/WebsiteOne/pull/1372) which ultimately worked.  I'd definitely gone over budget with the time on this, I was not comfortable with some of the compromises I had made on the Cucumber steps, but I had something working that would allow João to run the Pacific Rim scrum in such a way that with a single manual step he'd be able to notify everyone on Slack and the links on the AV site would stay live for 15 minutes to let people into the Scrum.

The set up also seemed to work at least partially for some less experienced Scrum masters over the weekend, but more support and scaffolding is clearly needed.  I also think that `url_set_directly` needs to be date rather than a flag, since I expect some repeating events may now say live incorrectly ... although maybe events all should say live for the duration - we would just need some kind of popup message in the hangout for people entering to let them know that no one else is there yet and that's okay, and people could still meetup.  Of course Hangout's can't be re-broadcast ... is it worth pursuing a Hangout plugin to handle the new setup or will work invested there have to be thrown out when/if Google completely sunsets Hangouts?

Perhaps now this week it's time to focus on adding PayPal payment support, and allowing people to sponsor each other's Premium memberships on AgileVentures.  I have payments I could start receiving from at least two people if those two features were in place, so perhaps that's the best focus?  In the meantime I could get some emails off to all the Hangout alternatives with a list of our needed features, e.g. recordability, URL to conference etc. ... On to the next Crunch Time!

Related Videos

* ["Kent Beck" Scrum](https://www.youtube.com/watch?v=nJVeelkuoGw)


















