I've got the RSpec unit(?) tests green for the new version of the WSO Slack Service that takes the AgileBot microservice completely out of the picture.  I start the day by merging the latest from develop, which is a gem bump from dependabot (mixed feelings about automating PRs for gem upgrades).  Then I check that we're still green for the RSpec.  Dangerous to be pulling in other changes when I still have pending RSpecs and failing cukes, but hmmm ... anyway, here are the regression failures from running the cukes on the new slack service:

```sh
#<Double "Logger"> was originally created in one example but has leaked into another example and can no longer be used. rspec-mocks' doubles are designed to only last for one example, and you need to create a new one in each example you wish to use it for. (RSpec::Mocks::ExpiredTestDoubleError)
./app/services/slack_service.rb:35:in `send_slack_message'
./app/services/slack_service.rb:14:in `post_hangout_notification'
./app/controllers/event_instances_controller.rb:15:in `update'
./features/step_definitions/event_steps.rb:266:in `/^the HangoutConnection has pinged to indicate the event (start|continuing)$/'
features/events/live_event.feature:21:in `And the HangoutConnection has pinged to indicate the event start'

# all the below were of the same form as above

Failing Scenarios:
cucumber features/events/live_event.feature:19 # Scenario: Ongoing ping from HangoutConnection app keeps event alive
cucumber features/events/live_event.feature:30 # Scenario: Lack of ping from HangoutConnection after two minutes kills events
cucumber features/events/upcoming_events.feature:34 # Scenario: Shows event past end time when still live
cucumber features/twitter.feature:13 # Scenario: Event going live without valid live stream does not cause youtube link to be tweeted
cucumber features/twitter.feature:18 # Scenario: Event going live without valid live stream will have youtube link tweeted later when live
cucumber features/twitter.feature:27 # Scenario: Event going live without valid live stream still causes hangout link to be tweeted
cucumber features/twitter.feature:32 # Scenario: Event going live with valid livestream causes tweets of hangout link and youtube link to be sent
```

Do the Specs are all green, and the cukes that are failing are all hitting this issue of a double leaking from one example to another ..., and they pass individually, and I can't see any mention of "Logger" in the step definitions. Weird.  I trace through the stack trace above and again see no mention of a double :logger being created anywhere ... no mention of any doubles anywhere in the cuke step definitions.  Something from factoryGirl?  No mention of doubles there ... I try running all the live_event.feature cukes together and yes, the error recurrs.  So the first scenario passes:

```
  Background:
    Given following events exist:
      | name       | description             | category        | start_datetime          | duration | repeats | time_zone | project | repeats_weekly_each_days_of_the_week_mask | repeats_every_n_weeks |
      | Scrum      | Daily scrum meeting     | Scrum           | 2014/02/03 07:00:00 UTC | 150      | never   | UTC       |         |                                           |                       |

  Scenario: Event is seen to be live when event is started a minute previously
    Given an event "Scrum"
    And the HangoutConnection has pinged to indicate the event start
    Then the event should be live
    And after one minute
    Then the event should still be live
```

but then the following two fail:

```
  Scenario: Ongoing ping from HangoutConnection app keeps event alive
    Given an event "Scrum"
    And the HangoutConnection has pinged to indicate the event start
    Then the event should be live
    And after three minutes
    When the HangoutConnection pings to indicate the event is ongoing
    Then the event should still be live
    And after three more minutes
    When the HangoutConnection pings to indicate the event is ongoing
    Then the event should still be live

  Scenario: Lack of ping from HangoutConnection after two minutes kills events
    Given an event "Scrum"
    And the HangoutConnection has pinged to indicate the event start
    Then the event should be live
    And after three minutes
    Then the event should be dead
```

with the same Logger issue.  I also notice that the test runs are hitting the test Slack instance, which is not ideal ... we're covered by VCR I think ... so I'm not going to add extra stubs immediately, and I think if I can solve this Logger issue I can make progress.  Previous experience would make me suspect the background step creating events via factories, but inspection shows that it is creating a normal active record element:

```rb
Given(/^following events exist:$/) do |table|
  table.hashes.each do |hash|
    hash[:project_id] = Project.find_by(title: hash['project']).id unless hash['project'].blank?
    hash.delete('project')
    Event.create!(hash)
  end
end
```

The hangout ping step is rather long:

```
When(/^the HangoutConnection has pinged to indicate the event (start|continuing)$/) do |type|
  participants = {"0"=>{"id"=>"hangout2750757B_ephemeral.id.google.com^a85dcb4670", "hasMicrophone"=>"true", "hasCamera"=>"true", "hasAppEnabled"=>"true", "isBroadcaster"=>"true", "isInBroadcast"=>"true", "displayIndex"=>"0", "person"=>{"id"=>"108533475599002820142", "displayName"=>"Alejandro Babio", "image"=>{"url"=>"https://lh4.googleusercontent.com/-p4ahDFi9my0/AAAAAAAAAAI/AAAAAAAAAAA/n-WK7pTcJa0/s96-c/photo.jpg"}, "na"=>"false"}, "locale"=>"en", "na"=>"false"}}
  header 'ORIGIN', 'a-hangout-opensocial.googleusercontent.com'
  put "/hangouts/@google_id", {title: @event.name, host_id: '3', event_id: @event.id,
                               participants: participants, hangout_url: 'http://hangout.test',
                               hoa_status: 'live', project_id: '1', category: 'Scrum', yt_video_id: '11'}
end
```
but again, there's no indication of any mocking, stubbing, doubles or loggers.  I google the error message and the only place I see it is in the RSpec docs: https://relishapp.com/rspec/rspec-mocks/docs/basics/scope where we see an example of a double being added to an object and then being referred to in another instance, but this is cucumber and not RSpec, although we're using RSpec commands in the step definitions.  So the place to look is the steps that are using those RSpec commands, e.g. 

```
    Then the event should be live
    And after one minute
    Then the event should still be live
```

```rb
Then(/^the event should (still )?be live$/) do |ignore|
  visit event_path(@event)
  expect(page).to have_content('This event is now live!')
end
```

```rb
And(/^after one (more )?minute$/) do |ignore|
  Delorean.time_travel_to '1 minute from now'
end
```

```rb
Then(/^the event should (still )?be live$/) do |ignore|
  visit event_path(@event)
  expect(page).to have_content('This event is now live!')
end
```

So the state we're carrying over is the @event instance, but that should get recreated for each scenario ... I try removing all the expectation steps from the scenarios, but the issue still occurs.  The failure is specifically on the attempt to ping the event start, and the error is thrown in the slack service when we try to send the slack message ... I take out that line and we pass:

```
  def send_slack_message(client, channel, text, user)
    client.chat_postMessage(channel: channel, text: text, username: user.display_name, icon_url: user.gravatar_url, link_names: 1)
  end
```

There's no mention of doubles in the SlackRubyClient github issues, but there is mention of some [thread leak issue](https://github.com/slack-ruby/slack-ruby-client/issues/104) that was fixed, but also mentions that the SlackRubyClient has a logger ... I try setting that to nil, in our Rails initializer, to no avail, so I go ahead and open an issue on the SlackRubyClient repo:

https://github.com/slack-ruby/slack-ruby-client/issues/174

I seem to have fixed the issue by setting the slack client logger to the Rails.logger

```
Slack::Web::Client.new(logger: Rails.logger)
```

So now I just have to decide whether these acceptance tests need to explicitly check that the SlackService is operating correctly rather than just catching them in VCR ...
