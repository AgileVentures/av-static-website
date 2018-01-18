---
title: AV EcoSystem Review Nicely Formatted Slack Updates
tags: 
author: Sam Joseph
---

![tying up](../images/nicely_formatted_slack_messages.png)

Urgh, stomach bug yesterday so I had to cancel Sunday free trial Premium F2F pairing.  Still weak today, but at least it seems we finally fixed the youtube link extra posting issue once I rolled out the `updated_at` --> `created_at` switch for the `recent_hangouts` method on events.  I think this week might need to focus on performance or maybe on our jitsi server.  Oh yes, and I'd started on trying to fix up our broken "start hangout" button ... I'd left the tests running over the weekend.  The event show view spec is failing.  I decide to delete that - our acceptance tests should check the user interface and we should force logic out of the views to have them tested in unit tests.  With that the specs are passing, and we'll see about the cukes.

In the meantime I'll just bash out this change I've finally settled on for the Slack message updates for hangouts and YouTube updates, or I would if I hadn't just kicked off a cuke test run locally - doh, I've pushed the deleted view spec to the branch, so CI will build it.  I'm weak from not eating yesterday - I can feel today will have lots of similar simple mistakes.  Do I do that performance check on staging to see if removing the information about the next upcoming hangout will make a big impact on our performance?  That'd require my local system to not be running all the cukes it is right now, urgh.  So I just kill the local run of the cukes and get the tiny messaging format fix in for the Slack messages.  Oh wow, now that I have it in ruby/rspec the change is so extraordinarily simple (compared to the CoffeeScript Hubot Microservice we had set up) that I can almost forgive myself for not completing the Hangout button fix that I'd started on Friday.   

In the meantime the CI gives me the failures for the hangout button removal:

```
  @javascript
  Scenario: Display hangout button on a project's page   # features/hangouts/hangout.feature:68
    Given I am a member of project "WebsiteOne"          # features/step_definitions/projects_steps.rb:50
    And I am on the "Show" page for project "WebsiteOne" # features/step_definitions/basic_steps.rb:255
    Then I should see hangout button                     # features/step_definitions/hangout_steps.rb:11
      expected to find css "#liveHOA-placeholder" but there were no matches (RSpec::Expectations::ExpectationNotMetError)
      ./features/step_definitions/hangout_steps.rb:15:in `/^I should (not )?see hangout button$/'
      features/hangouts/hangout.feature:71:in `Then I should see hangout button'

Failing Scenarios:
cucumber -p second_try features/documents.feature:156 # Scenario: Insert media model accepts full url youtube links
cucumber -p second_try features/events/show_event.feature:36 # Scenario Outline: Do not show hangout button until 10 minutes before scheduled start time, and while event is running, Examples (#1)
cucumber -p second_try features/events/show_event.feature:37 # Scenario Outline: Do not show hangout button until 10 minutes before scheduled start time, and while event is running, Examples (#2)
cucumber -p second_try features/events/show_event.feature:40 # Scenario Outline: Do not show hangout button until 10 minutes before scheduled start time, and while event is running, Examples (#5)
cucumber -p second_try features/hangouts/hangout.feature:31 # Scenario: Create a hangout for a scrum event
cucumber -p second_try features/hangouts/hangout.feature:68 # Scenario: Display hangout button on a project's page
```

So I adjusted the relevant hangout check step definition to:

```rb
Then /^I should (not )?see hangout button$/ do |absent|
  if absent
    expect(page).not_to have_css '#hoa_instructions'
  else
    expect(page).to have_css "#hoa_instructions"
  end
end
```

which basically fixed everything once I added the relevant id in the places where the hangout button appears.  So I went ahead and merged in the messaging format changes for the Slack notifications.  Reviewed a few dependabot gem upgrade PRs, and I'm now waiting for the CI to pass on the Hangout button fix.  I'd really like to get that out today.  I'd love to have the Slack message changes out before the first scrum, but that's in 20 minutes now, so probably no chance of that.  Maybe by the afternoon one.  Okay, one step at a time, keep plodding on.
