---
title: AV EcoSystem Review Dripping Tap
tags: 
author: Sam Joseph
---

![dripping tap](../images/dripping_tap.jpg)

Back after a break, getting back into the routine.  One thing being away highlighted was that we have awesome enthusiastic new members who have the run the scrums while I was away, and awesome enthusiastic long term members (i.e. federico) running the mobs and marketing meetings.  Having other folks start the scrums highlighted one of the outstanding niggles that we have with the new manual updates that Lokesh was working on over the summer.  For those scrums that repeat daily, pushing a new hangout live seems to also push out the youtube link from the previous day.

I'd been coping with this by simply deleting the incorrect link, but that's a hacky manual fix that's going to flummox any new folks.  It's been a dripping tap for a while.  Does it indicate that I'm fundamentally incompetent that I haven't fixed it or got someone to fix it?  I hope not.  There always seems to be fifty different things that need to be done and it's usually unclear what the long term pros and cons of each will be.  Okay, no more navel-gazing, let's see if we can fix this issue that burns up my time, and is confusing for others.

The confusing thing about this bug is that it took me a while to work out the pattern of when it occurred.  Specifically it only occurs when there was an event of the same type that ran the day before, and then not every time.  I think it may be occurring when an event is started within 24 hours of the previous event.  I had previously opened a ticket on the subject:

[https://github.com/AgileVentures/WebsiteOne/issues/1809](https://github.com/AgileVentures/WebsiteOne/issues/1809)

So the clear procedure here would be to try and replicate the issue with a cucumber test.  Although I can't seem to help myself but first have a little gander at the code that I think might cause the problem.  The EventsController grabs the most recent_hangout like so:

```rb
  def show
    @event_schedule = @event.next_occurrences
    @recent_hangout = @event.recent_hangouts.first
    render partial: 'hangouts_management' if request.xhr?
  end
```  

and the form for manually updating the event includes the YouTube URL from this `@recent_hangout`:

```erb
<%= hidden_field_tag 'yt_url', @recent_hangout.try!(:yt_url) %>
```

the `@recent_hangout` is grabbed from the database by some code in the events model:

```rb
  def recent_hangouts
    event_instances
      .where('updated_at BETWEEN ? AND ?', 1.days.ago + duration, DateTime.now.end_of_day)
      .order(updated_at: :desc)
  end
```

I wonder if we need a different date range in here ...?  So here's where I might run off into spiking code, so I drop back to create a new Cucumber scenario:

```gherkin
  Scenario: Event doesn't ping old youtube URL
    Given the date is "2014 Feb 5th 6:59am"
    And that we're spying on the SlackService
    When I manually set a hangout link for event "Repeat Scrum"
    Then the Hangout URL is posted in Slack
    And the Youtube URL is not posted in Slack
```

which relies on a couple of new step definitions

```rb
And(/^that we're spying on the SlackService$/) do
  allow(SlackService).to receive :post_yt_link
  allow(SlackService).to receive :post_hangout_notification
end

Then(/^the Youtube URL is not posted in Slack$/) do
  expect(SlackService).not_to have_received :post_yt_link
end

And(/^the Hangout URL is posted in Slack$/) do
  expect(SlackService).to have_received :post_hangout_notification
end
```

and we catch the error we've been experiencing in the live system:

```sh
  Scenario: Event doesn't ping old youtube URL                  # features/hangouts/edit_hangout_url.feature:41
    Given the date is "2014 Feb 5th 6:59am"                     # features/step_definitions/event_steps.rb:94
    And that we're spying on the SlackService                   # features/step_definitions/hangout_steps.rb:212
    When I manually set a hangout link for event "Repeat Scrum" # features/step_definitions/hangout_steps.rb:108
    Then the Hangout URL is posted in Slack                     # features/step_definitions/hangout_steps.rb:221
    And the Youtube URL is not posted in Slack                  # features/step_definitions/hangout_steps.rb:217
      (SlackService).post_yt_link(no args)
          expected: 0 times with any arguments
          received: 1 time (RSpec::Mocks::MockExpectationError)
      ./features/step_definitions/hangout_steps.rb:218:in `/^the Youtube URL is not posted in Slack$/'
      features/hangouts/edit_hangout_url.feature:46:in `And the Youtube URL is not posted in Slack'

Failing Scenarios:
cucumber features/hangouts/edit_hangout_url.feature:41 # Scenario: Event doesn't ping old youtube URL
````

So can we fix it?  I think the date range must be wrong somehow - beginning of day vs end of day, or subtract the duration rather than adding it ... my intuitions are not bearing fruit - I hook the thing on the debugger:

```sh
[212, 221] in /Users/tansaku/Documents/GitHub/AgileVentures/WebsiteOne/app/models/event.rb
   212: 
   213:   def recent_hangouts
   214:     debugger
   215:     event_instances
   216:       .where('updated_at BETWEEN ? AND ?', 1.days.ago + duration, DateTime.now.end_of_day)
=> 217:       .order(updated_at: :desc)
   218:   end
   219: 
   220:   def less_than_ten_till_start?
   221:     return true if within_current_event_duration?
(byebug) 1.days.ago + duration
Tue, 04 Feb 2014 06:59:22 UTC +00:00
(byebug) DateTime.now.end_of_day
Wed, 05 Feb 2014 23:59:59 +0000
(byebug) event_instances.where('updated_at BETWEEN ? AND ?', 1.days.ago + duration, DateTime.now.end_of_day)
#<ActiveRecord::AssociationRelation [#<EventInstance id: 2, event_id: 2, title: "HangoutsFlow", hangout_url: "http://hangout.test", created_at: "2014-02-04 07:00:00", updated_at: "2014-02-04 07:03:00", uid: "100", category: "Scrum", project_id: nil, user_id: 1, yt_video_id: "QWERT55", participants: {"0"=>{"id"=>"hangout2750757B_ephemeral.id.google.com^a85dcb4670", "hasMicrophone"=>"true", "hasCamera"=>"true", "hasAppEnabled"=>"true", "isBroadcaster"=>"true", "isInBroadcast"=>"true", "displayIndex"=>"0", "person"=>{"id"=>"108533475599002820142", "displayName"=>"Alejandro Babio", "image"=>{"url"=>"https://lh4.googleusercontent.com/-p4ahDFi9my0/AAAAAAAAAAI/AAAAAAAAAAA/n-WK7pTcJa0/s96-c/photo.jpg"}, "na"=>"false"}, "locale"=>"en", "na"=>"false"}}, hoa_status: "started", url_set_directly: true, youtube_tweet_sent: false>]>
```
I think I see the problem - this duration addition is not working ...

```sh
(byebug) 1.days.ago + duration
Tue, 04 Feb 2014 07:00:00 UTC +00:00
(byebug) 1.days.ago
Tue, 04 Feb 2014 06:59:52 UTC +00:00
(byebug) 1.days.ago + 30.minutes
Tue, 04 Feb 2014 07:30:22 UTC +00:00
(byebug) 1.days.ago + duration.minutes
Tue, 04 Feb 2014 07:15:27 UTC +00:00
```

This is the fix:

```rb
  def recent_hangouts
    event_instances
      .where('updated_at BETWEEN ? AND ?', 1.days.ago + duration.minutes, DateTime.now.end_of_day)
      .order(updated_at: :desc)
  end
```

but will it break lots of other things? The other scenarios in that feature pass, so I push the fix up onto the bugfix branch and let the CI run, while kicking off the full set of hangout features locally.  The cukes still take a long time to run even on my local machine (>10 minutes), so I tend to run them in small batches.  Another dripping tap perhaps ...
