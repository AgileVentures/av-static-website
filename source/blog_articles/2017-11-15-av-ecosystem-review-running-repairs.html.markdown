9:24 am blog face, but no--9:29 am--since I got distracted by Slack and email.  Yesterday I rolled out some code to make the automated Slack sign up more robust.  The code is on production now, and so maybe a test from the heroku command line is the way to go ...

```sh
irb(main):003:0> SlackInviteJob.new.perform(User.last.email)
[ActiveJob] [ActionMailer::DeliveryJob] [ff673d08-8c78-403c-a740-268417c9a4c7] Performing ActionMailer::DeliveryJob from Inline(mailers) with arguments: "AdminMailer", "failed_to_invite_user_to_slack", "deliver_now", "email@email.com", nil, "\"already_invited\""
[ActiveJob] [ActionMailer::DeliveryJob] [ff673d08-8c78-403c-a740-268417c9a4c7]   Rendered admin_mailer/failed_to_invite_user_to_slack.html.erb within layouts/mailer (0.9ms)
[ActiveJob] [ActionMailer::DeliveryJob] [ff673d08-8c78-403c-a740-268417c9a4c7] 
Sent mail to support@agileventures.org (215.3ms)
[ActiveJob] [ActionMailer::DeliveryJob] [ff673d08-8c78-403c-a740-268417c9a4c7] Performed ActionMailer::DeliveryJob from Inline(mailers) in 386.04ms
[ActiveJob] Enqueued ActionMailer::DeliveryJob (Job ID: ff673d08-8c78-403c-a740-268417c9a4c7) to Inline(mailers) with arguments: "AdminMailer", "failed_to_invite_user_to_slack", "deliver_now", "email@email.com", nil, "\"already_invited\""
=> {"ok"=>false, "error"=>"already_invited"}
```
Now that looks like we might be in business.  The most recently signed up member has already been invited, and I get a sensible error message when running the SlackInviteJob from the command line.  Let's see if the last 50 have all been invited?  Seven haven't.  That's the lowest number over the last ten days that I've been manually inviting.  Those seven could have signed up before the deploy.  The acid test will be tomorrow when we'll see if it's zero, if it's not, there's still something to fix.

Robert had been making the point that we needed to make it easier for folks to get into Slack and that the page that was the top hit on Google for "Agile Ventures Slack" was an [old one](https://www.agileventures.org/projects/agileventures-community/documents/introduction-to-the-agileventures-slack-channel), so I quickly updated that to ensure it mentioned the new sign up process.

The other running repair we need to make is on the events system where non-repeating single events are not showing up in the future event list.  Maybe they never did?  We need an acceptance test to expose this.  Something like this:

```gherkin
@vcr
Feature: List Single Events
  As a site user
  So I can find events relevant to me that are happening now
  I would like to see one off events

  Background:
    Given the following projects exist:
      | title | description          | pitch | status |
      | cs169 | greetings earthlings |       | active |
    Given following events exist:
      | name | description      | category         | start_datetime          | duration | repeats | time_zone | project | 
      | Once | Once off meeting | Pair Programming | 2016/05/02 07:00:00 UTC | 150      | never   | UTC       |  cs169  |

  Scenario: Show correct timezone
    Given the date is "2016/05/01 09:15:00 UTC"
    And I am on events index page
    Then I should see "Once"
    And the local time element should be set to "2016-05-02T07:00:00Z"
    
```   

Now that passes, but so perhaps there's something about when there are also repeating events in the mix ...?  Maybe I should hook locally on the debugger?  I do that and see that a newly created event is not listed in the upcoming events.  It seems that a newly created single event has it's repeat_ends set to true by default and so it's not captured in the list of future events:

```rb
  def self.base_future_events(project)
    project.nil? ? Event.future_events : Event.future_events.where(project_id: project)
  end

  def self.future_events
    Event.where('repeat_ends = false OR repeat_ends IS NULL OR repeat_ends_on > ?', Time.now)
  end

  def self.upcoming_events(project=nil)
    events = Event.base_future_events(project).inject([]) do |memo, event|
      memo << event.next_occurrences
    end.flatten.sort_by { |e| e[:time] }
    Event.remove_past_events(events)
  end
```

My test was passing because it's not creating the event in the same way that the form does, with `repeat_ends` set to true. I adjust the test and it fails, and I can get it to pass with the following change:

```rb
  def self.future_events
    Event.where('repeats = \'never\' OR repeat_ends = false OR repeat_ends IS NULL OR repeat_ends_on > ?', Time.now)
  end
```

Is this a new thing?  Maybe this never worked.  A complete overhaul of the events system is certainly looking in order.  There's so much more clean up here, but let's fix the bleed in production first ...
