So I'm calling this clearing the backlog, but argh, I want to start on that new coding ticket rather than the older content ones in the WebSiteOne waffle board.  My heuristics for board review are check the "Please Check" tickets.  There I have a ticket from Matt where he hasn't replied to my comments/queries - he's moving house and has lots going on so no shock there.  That was also a request from Joao, so perhaps I should finish that up. 

Ideally I'll only have one ticket assigned to me in any one column at a time.  I've got a hanging ticket in "Please check" about providing a list of volunteer opportunities at AgileVentures ... I put that off as the priority dropped, but it's in the way.  Okay, so let's make this really about clearing the backlog, starting with that.  So I'll update the branch there and kick of a build - hopefully it'll be green before the end of this blog and I can merge it.

Let's pull Matt's branch onto my local machine to check it out:

```
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/WebsiteOne (1715_adjusting_premium_page_layout)]$ 
→ git checkout develop
Switched to branch 'develop'
Your branch is up-to-date with 'origin/develop'.
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/WebsiteOne (develop)]$ 
→ git pull origin develop
From http://github.com/AgileVentures/WebsiteOne
 * branch              develop    -> FETCH_HEAD
Already up-to-date.
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/WebsiteOne (develop)]$ 
→ git checkout 1750_client_type
Branch 1750_client_type set up to track remote branch 1750_client_type from origin.
Switched to a new branch '1750_client_type'
```

I guess part of me leans on I should be leaving this for Matt. However, I think I have to put the project and the wider community first here.  Perhaps my comments/suggestions were too pernickety and that put Matt off.  Perhaps I was being remiss in not just merging it earlier - the build was green, but I'm also the devops person who has to pick up the pieces if it goes wrong.  I had Praveen test his own new feature and bug fix on staging before deploying code yesterday - that seems like a good plan, but I can't put pressure on Matt on this one.  

The two things I was asking Matt for were to pull the motivation for this feature into a separate feature file, although I didn't do that in the opportunities link addition.  There was also checking the rest of the system about whether the introduction of a new event type would clash with other areas - doing a search over the codebase I see we do have checks for the two previous types in Twitter notifications, but we've turned those off, so that's no biggy.  There's something in the EventHelper module:

```rb
module EventHelper
  def cover_for(event)
    case event.category
      when 'PairProgramming'
        image_path('event-pairwithme-cover.png')

      when 'Scrum'
        image_path('event-scrum-cover.png')

      else
        ''
    end
  end 
```

but all these places are looking like they have sensible defaults, so I think due dilligence is done.  The acceptance test, however, is only checking that if a client meeting exists that it will be shown correctly. It doesn't check that a client meeting can actually be created.  So I think it's time to pull out the feature I was thinking of.  Here's my first pass:

```gherkin
@vcr
Feature: Provide a ClientMeeting Category
  As a project manager
  In order to be able to distinguish between client meetings and other types of meeting
  I would like to see and create "client meeting" events

  Background:
    Given following events exist:
      | name       | description             | category        | start_datetime          | duration | repeats | time_zone | project | repeats_weekly_each_days_of_the_week_mask | repeats_every_n_weeks |
      | ClientMtg  | Daily client meeting    | ClientMeeting   | 2014/02/03 11:00:00 UTC | 150      | never   | UTC       |         |                                           |                       |
    And I am logged in
    And the following projects exist:
      | title | description          | pitch | status | commit_count |
      | WSO   | greetings earthlings |       | active | 2795         |
      | EdX   | greetings earthlings |       | active | 2795         |
      | AAA   | for roadists         |       | active |              |

  @javascript
  Scenario: Show Client Meeting event
    Given the date is "2014/02/03 07:01:00 UTC"
    And I am on Events index page
    Then I should see "ClientMtg"
    And I should see "11:00-13:30 (UTC)"

  @javascript
  Scenario: Create Client Meeting event
    Given I am on Events index page
    When I click "New Event"
    And I fill in event field:
      | name        | value          |
      | Name        | Whatever       |
      | Description | something else |
      | Start Date  | 2014-02-04     |
      | Start Time  | 09:00          |
    And I select "EdX" from the event project dropdown
    And I select "ClientMeeting" from the event category dropdown
    And I should not see "End Date"
    And I click on the "event_date" div
    And I click the "Save" button
    Then I should see "Event Created"
    Given the event "Whatever"
    Then I should be on the event "Show" page for "Whatever"
    And the event named "Whatever" is associated with "EdX"
    And I should see "09:00-09:30 (UTC)"
    And I should see "ClientMtg"
```

the first scenario passes, but I have an unimplemented step:

```rb
When(/^I select "([^"]*)" from the event category dropdown$/) do |arg1|
  pending # Write code here that turns the phrase above into concrete actions
end
```

so I implement that:

```
When(/^I select "([^"]*)" from the event category dropdown$/) do |category|
  page.select category, from: "event_category"
end
```

tweak the feature a little - test in dev mode, and get the two scenarios both green.  Then by stashing all the code so far, checking out develop, and running the tests I can check that they fail when the functionality is removed, thus ensuring that we are properly testing the feature:

```sh
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/WebsiteOne (1750_client_type)]$ 
→ git checkout develop
error: Your local changes to the following files would be overwritten by checkout:
	features/events/show_event.feature
Please commit your changes or stash them before you switch branches.
Aborting
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/WebsiteOne (1750_client_type)]$ 
→ git stash
Saved working directory and index state WIP on 1750_client_type: 3ce512a5 Merge branch 'develop' into 1750_client_type
HEAD is now at 3ce512a5 Merge branch 'develop' into 1750_client_type
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/WebsiteOne (1750_client_type)]$ 
→ git checkout develop
Switched to branch 'develop'
Your branch is up-to-date with 'origin/develop'.
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/WebsiteOne (develop)]$ 
→ git stash pop
Auto-merging features/events/show_event.feature
CONFLICT (content): Merge conflict in features/events/show_event.feature
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/WebsiteOne (develop)]$ 
→ be cucumber features/events/client_meetings.feature
[Coveralls] Using SimpleCov's 'rails' settings.
Using the default profile...
@vcr
Feature: Provide a ClientMeeting Category
  As a project manager
  In order to be able to distinguish between client meetings and other types of meeting
  I would like to see and create "client meeting" events

  Background:                         # features/events/client_meetings.feature:7
    Given following events exist:     # features/step_definitions/event_steps.rb:32
      | name      | description          | category      | start_datetime          | duration | repeats | time_zone | project | repeats_weekly_each_days_of_the_week_mask | repeats_every_n_weeks |
      | ClientMtg | Daily client meeting | ClientMeeting | 2014/02/03 11:00:00 UTC | 150      | never   | UTC       |         |                                           |                       |
    And I am logged in                # features/step_definitions/user_steps.rb:63
    And the following projects exist: # features/step_definitions/projects_steps.rb:6
      | title | description          | pitch | status | commit_count |
      | WSO   | greetings earthlings |       | active | 2795         |
      | EdX   | greetings earthlings |       | active | 2795         |
      | AAA   | for roadists         |       | active |              |

  @javascript
  Scenario: Show Client Meeting event           # features/events/client_meetings.feature:19
    Given the date is "2014/02/03 07:01:00 UTC" # features/step_definitions/event_steps.rb:94
    And I am on Events index page               # features/step_definitions/event_steps.rb:15
    Then I should see "ClientMtg"               # features/step_definitions/basic_steps.rb:200
    And I should see "11:00-13:30 (UTC)"        # features/step_definitions/basic_steps.rb:200

  @javascript
  Scenario: Create Client Meeting event                           # features/events/client_meetings.feature:26
    Given I am on Events index page                               # features/step_definitions/event_steps.rb:15
    When I click "New Event"                                      # features/step_definitions/basic_steps.rb:88
    And I fill in event field:                                    # features/step_definitions/basic_steps.rb:152
      | name        | value          |
      | Name        | Whatever       |
      | Description | something else |
      | Start Date  | 2014-02-04     |
      | Start Time  | 09:00          |
    And I select "EdX" from the event project dropdown            # features/step_definitions/event_steps.rb:110
    And I select "ClientMeeting" from the event category dropdown # features/step_definitions/event_steps.rb:114
      Unable to find option "ClientMeeting" (Capybara::ElementNotFound)
      ./features/step_definitions/event_steps.rb:115:in `/^I select "([^"]*)" from the event category dropdown$/'
      features/events/client_meetings.feature:36:in `And I select "ClientMeeting" from the event category dropdown'
    And I should not see "End Date"                               # features/step_definitions/basic_steps.rb:200
    And I click on the "event_date" div                           # features/step_definitions/event_steps.rb:106
    And I click the "Save" button                                 # features/step_definitions/basic_steps.rb:96
    Then I should see "Event Created"                             # features/step_definitions/basic_steps.rb:200
    Given the event "Whatever"                                    # features/step_definitions/event_steps.rb:324
    Then I should be on the event "Show" page for "Whatever"      # features/step_definitions/event_steps.rb:83
    And the event named "Whatever" is associated with "EdX"       # features/step_definitions/event_steps.rb:126
    And I should see "Event type: ClientMeeting"                  # features/step_definitions/basic_steps.rb:200

Failing Scenarios:
cucumber features/events/client_meetings.feature:26 # Scenario: Create Client Meeting event

2 scenarios (1 failed, 1 passed)
23 steps (1 failed, 8 skipped, 14 passed)
0m11.677s
```

Then I pushed it up into the branch, and left Matt the following note:

> @mattlindsey I've gone ahead and done the checks and adjusted the acceptance tests as I was suggesting - apologies if you were hoping to work on this, but I thought that given that it's been open in the PRs for a month I should just get it cleared up.

> Very happy to go through it with you when you have more time :-)

I feel bad about missing the chance to work on it more closely with Matt, but now it's building and hopefully I can merge it later today.  The opportunities link PR that I started a rebuild of at the beginning of this blog failed on what looks like one of our intermittent JS failures ...

```

Scenario: A user can insert an image  

    Then the Mercury Editor modal window should be visible                                         # features/step_definitions/mercury_steps.rb:57
      expected to find css ".mercury-modal" but there were no matches (RSpec::Expectations::ExpectationNotMetError)
      ./features/step_definitions/mercury_steps.rb:58:in `/^the Mercury Editor modal window should (not |)be visible$/'
      features/documents.feature:136:in `Then the Mercury Editor modal window should be visible'
      
Failing Scenarios:
cucumber features/documents.feature:130 # Scenario: A user can insert an image
```

I could add that to the exceptions, but that approach is dangerous as it's easy to forget which tests are omitted in CI - Jon's approach on LocalSupport to re-run failing tests seems more sensible.  I'd also love to get it so that we only output the full details for failing tests, which would also be hit by Jon's approach.  I'll add that chore to the WSO issues - actually I find I already [have one](https://github.com/AgileVentures/WebsiteOne/issues/1803).

Maybe a vote on that is in order ...
