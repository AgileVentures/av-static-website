---
title: Naming and Cucumber
date: 2016-11-11
tags: pull requests rails cucumber features refactoring architecture rearchitecting domain driven design DDD steps nested
author: Sam Joseph
---

In a series of disjointed solo sessions I managed to fix the bug for manual hangout edits making the following event live.  Michael and I had wrapped this bug in a cucumber scenario the day previously.  The manual hangout edit interface looks like this:

![manual hangout edit interface](https://www.dropbox.com/s/4xbad7ddniesyo7/Screenshot%202016-10-18%2017.00.20.png?dl=1)

Originally the manual edit had not worked at all on repeating events with lots of history; which had been a big problem when the automatic updates failed for two weeks while the Google Hangout API was in flux.  We'd got manual updates working on recurring events by adjusting the way that an event searched for recent hangouts, replacing a sort and filter by `created_at` with one on `updated_a`t.  The problem had been that with a long series of event instances, and manual updates clobbering old ones started by the same user, the created_at date would lag very far behind the updated_at date.  The recent_hangouts method became:

```rb
  def recent_hangouts
    event_instances
      .where('updated_at BETWEEN ? AND ?', 1.days.ago.beginning_of_day, DateTime.now.end_of_day)
      .order(updated_at: :desc)
  end
```

I'd also added a flag on the EventInstance model to indicate whether an EventInstance had been manually set.  This manual setting meant that the following EventInstances would also report live.  I considered briefly switching the boolean flag to a date time, but demurred and adjusted the way we create the unique id for manually edited hangout event instances.  I wanted to ensure that every manual edit was recorded as a separate EventInstance instance.  I added a SecureRandom unique id to the EventInstancesHelper method that is used during a manual update:

```rb
require 'securerandom'
module EventInstancesHelper
  def generate_event_instance_id(user, project_id = nil)
    "#{user.id}#{project_id || '00'}-#{SecureRandom.uuid}"
  end
end
```

By itself this didn't fix the problem, so I also adjusted the `recent_hangouts` method further so that it only looked in a time window starting from the same time on the previous day, plus the duration of the event:

```rb
  def recent_hangouts
    event_instances
      .where('updated_at BETWEEN ? AND ?', 1.days.ago + duration, DateTime.now.end_of_day)
      .order(updated_at: :desc)
  end
```

The cukes went green and I had to fix up a few specs for EventInstanceHelper and Event class.  I was't sure if the fully unique id was actually necessary, but when I saw the specs for EventInstanceHelper that were saying a unique id should be returned, when it patently wasn't doing so, I felt I really wanted to stick with the change.  Arguably it could be the subject of a completely separate pull request, driven by a different cucumber scenario.  I've been being harsh with the AsyncVoter folks to keep out any functionality not related to the feature being worked on.  It's easy to give myself slack on the other side; but in my defence this is a bug fix, and I'm trying to make sure we aren't losing valuable data about user activity.  I did try to get the feature to wrap this.  Actually I did a major refactoring of the cukes here, which relates to a [PR from John on LocalSupport](https://github.com/AgileVentures/LocalSupport/pull/391) where he's using a lot of nested steps, which I'm not such a big fan of.

On WSO the editing a hangout url on a repeating event cuke scenario had gotten really long  :

```gherkin
  Scenario: Edit Hangout URL on repeating event and ensure event stays live
    Given the date is "2014-02-06 07:00:00"
    # manually setting the hangout link
    And I navigate to the show page for event "Repeat Scrum"
    And I open the Edit URL controls
    And I fill in "hangout_url" with "https://hangouts.google.com/hangouts/_/ytl/HEuWPSol0vcSmwrkLzR4Wy4mkrNxNUxVmqHMmCIjEZ8=?hl=en_US&authuser=0"
    And I click on the Save button
    # checking that event shows live and that link
    And I navigate to the show page for event "Repeat Scrum"
    Then I should see link "Join now" with "https://hangouts.google.com/hangouts/_/ytl/HEuWPSol0vcSmwrkLzR4Wy4mkrNxNUxVmqHMmCIjEZ8=?hl=en_US&authuser=0"
    # checking that the event stays live for the specified duration (as no pings coming from hangout plugin)
    And I jump to one minute before the end of the event at "2014-02-06 07:14:00"
    And I navigate to the show page for event "Repeat Scrum"
    Then I should see link "Join now" with "https://hangouts.google.com/hangouts/_/ytl/HEuWPSol0vcSmwrkLzR4Wy4mkrNxNUxVmqHMmCIjEZ8=?hl=en_US&authuser=0"
    # Check that the event the following day is not automatically taken live
    And I jump to one minute before the end of the event at "2014-02-07 07:01:00" # actually a week later
    And I navigate to the show page for event "Repeat Scrum"
    Then I should not see "This event is now live!"
```

The previous day with Michael I'd inserted comments about what each section was doing.  I resolved to turn those into steps.  The quick and dirty path would be to take each set of steps and turn them into nested steps.  But the approach I preferis to make new steps that are collections of ruby statements to avoid multiple levels of redirection.  The steps I generated yesterday were:

```rb
And(/^I manually set a hangout link for event "([^"]*)"$/) do |name|
  @hangout_url = 'https://hangouts.google.com/hangouts/_/ytl/HEuWPSol0vcSmwrkLzR4Wy4mkrNxNUxVmqHMmCIjEZ8=?hl=en_US&authuser=0'
  visit event_path(Event.find_by_name(name))
  page.execute_script(  %q{$('li[role="edit_hoa_link"] > a').trigger('click')}  )
  fill_in 'hangout_url', with: @hangout_url
  page.find(:css, %q{input[id="hoa_link_save"]}).trigger('click')
end

Then(/^"([^"]*)" shows live for that hangout link for the event duration$/) do |event_name|
  event = Event.find_by_name(event_name)
  visit event_path(event)
  expect(page).to have_link('Join now', href: @hangout_url)
  time = Time.parse(@jump_date) + event.duration.minutes - 1.minute
  Delorean.time_travel_to(time)
  visit event_path(event)
  expect(page).to have_link('Join now', href: @hangout_url)
end

And(/^"([^"]*)" is not live the following day$/) do |event_name|
  event = Event.find_by_name(event_name)
  Delorean.time_travel_to(Time.parse(@jump_date) + 1.day)
  visit event_path(event)
  expect(page).not_to have_content('This event is now live!')
end

```

which allowed that long convoluted scenario above to be boiled down to the following:

```gherkin
  Scenario: Edit Hangout URL on repeating event and ensure event stays live
    Given the date is "2014-02-06 07:00:00"
    And I manually set a hangout link for event "Repeat Scrum"
    Then "Repeat Scrum" shows live for that hangout link for the event duration
    And "Repeat Scrum" is not live the following day
```

Maybe I'm crazy, not re-using the steps, but my goal is to make it transparent for me in the future, and other developers, to get from the english description to get straight to the description at the ruby/rspec/capybara level.  What I find again and again in WSO and LS where the steps are deeply nested is that I get tripped up because the English descriptions are slightly misleading.  This is actually a facet of a more general problem for coding in general, that wherever an abstraction (e.g. cuke step, or method name) fails to accurately reflect what's going on under the hood, you can easily be misled.  Of course it all comes back to humans.  What's transparently clear for one person might not be for another.  But take a look at this background step from the same Cuke file:

```gherkin
And the following event instances exist:
 ... <table of data>
```

We'd created this to replace the original setup step that referred to hangouts (for which there was no corresponding domain model) and used rspec factories that were shared with the specs and did a fair amount of data massaging.  Having made the change we realised that to keep things working we would also need a complex participants data.  It was trivial to adjust our new background step to add that data, but now that was hidden at the level of the feature file so we adjusted as follows:

```gherkin
And the following event instances (with default participants) exist:
 ... <table of data>
```

Too much perhaps?  But the intention is to give someone else who wants to use that step (or method) heads up about what's going on under the hood.  The cuke steps above are a bit long and need some DRYing out, but I think I'd rather have them DRYed out into other ruby methods, that are well named.  There's perhaps almost too much room for nuance with the English descriptions of pure cucumber steps.  So for example the following:

```rb
Then(/^"([^"]*)" shows live for that hangout link for the event duration$/) do |event_name|
  event = Event.find_by_name(event_name)
  visit event_path(event)
  expect(page).to have_link('Join now', href: @hangout_url)
  time = Time.parse(@jump_date) + event.duration.minutes - 1.minute
  Delorean.time_travel_to(time)
  visit event_path(event)
  expect(page).to have_link('Join now', href: @hangout_url)
end
```
might become

```rb

Then(/^"([^"]*)" shows live for that hangout link for the event duration$/) do |event_name|
  @event = Event.find_by_name(event_name)
  time = Time.parse(@jump_date) + event.duration.minutes - 1.minute
  check_event_live
  Delorean.time_travel_to(time)
  check_event_live
end

def check_event_live
  visit event_path(@event)
  expect(page).to have_link('Join now', href: @hangout_url)
end
```

Perhaps that's no different to:

```
Then(/^"([^"]*)" shows live for that hangout link for the event duration$/) do |event_name|
  @event = Event.find_by_name(event_name)
  time = Time.parse(@jump_date) + event.duration.minutes - 1.minute
  step 'I check event is live '
  Delorean.time_travel_to(time)
  check_live(event)
end

Then(/^I check event is live$/) do
  visit event_path(@event)
  expect(page).to have_link('Join now', href: @hangout_url)
end
```

which allows that nested step to be re-used in other cucumber scenarios, but the debugging support is not as good.  Maybe this just bike-shedding.  The critical thing is likely to be a constant general refining to a consistent domain model and methods/steps that are not too long and not too short and do a best effort to have a description that doesn't leave too many surprised under the hood.

I made sure that the refactored cukes did actually break, which took a combination of adjusting the Event#recent_hangouts method back to use `created_at` AND removing the secure random id from the EventsInstanceHelper, indicating that both were needed to get the desired behaviour at the user level; so I didn't need to feel a total hypocrite for pushing on the AsyncVoter folks to remove all extraneous code :-)  That didn't stop me waking up in the middle of the night thinking, "we don't even need to search for recent_hangouts!" - we just grab the latest one reverse sorted by updated_at and check if it's live?  That recent_hangouts method is used nowhere else in the code base.  The method name makes no sense in terms of what we want really.  What we want to know is whether the most recently updated event instance is still live?  More refactoring awaits!

###Related Videos

* ["Martin Fowler" scrum](https://www.youtube.com/watch?v=QMvJADMRpo0)
* [Solo on WebSiteOne](https://www.youtube.com/watch?v=ntvRINA66UM)
* ["Kent Beck" scrum](https://www.youtube.com/watch?v=xM-QeSOiHqA)
 



