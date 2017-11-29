I'm getting a bit stuck.  My motivation to clear things off my desk has completely collapsed.  Today I did my social media station keeping before opening the blog.  I'd love to get to the computer at 8am like I sometimes used to and get a real meaty chunk of work by 10am to then have the motivation to hit on non social-media admin.  Gosh social media is gamified addiction fun isn't it.

Anyhow, I've cleared all the dependabot low hanging fruit, so I'm rebuilding some older PRs, which might just be falling foul of intermittent fails.  Gosh that's a time sync - that reminds me about my plan to try and get the medium-editor in place so we can drop mercury.  I'm also slighltly stuck on my revision to the WSO README for fear of getting into arguments about the accuracy of the project history - maybe better to leave that section out?  Maybe I move the new draft README out of my last blog and into a placeholder location ...

[https://github.com/AgileVentures/WebsiteOne/blob/develop/README-draft.md](https://github.com/AgileVentures/WebsiteOne/blob/develop/README-draft.md)

So keep polishing the README, sort out the pending issues showing up in the test output, work on the medium editor branch, or fix some of the niggly things, e.g. the formatting issue that Susanna highlighted yesterday?  I think I can get a quick win on this [formatting issue](https://github.com/AgileVentures/WebsiteOne/issues/1994) from Susanna.  I make a branch and amend an existing cucumber test:

```gherkin
  Background:
    Given following events exist:
      | name       | description             | category        | start_datetime          | duration | repeats | time_zone | project | repeats_weekly_each_days_of_the_week_mask | repeats_every_n_weeks |
      | Standup    | Daily standup meeting<br>woot!   | Scrum           | 2014/02/03 07:00:00 UTC | 150      | weekly  | UTC       |         |   15                                      |     1                 |

  Scenario: Show correct timezone
    Given the date is "2016/05/01 09:15:00 UTC"
    And I am on events index page
    Then I should see "Standup"
    And the local time element should be set to "2016-05-10T07:00:00Z"
    And I should not see "<br>"
```

which fails in the way I expect, i.e. the html tag shows up in the list view as Susanna pointed out.  I want to refactor the cuke, but I must fix the issue first, which might be as simple as just adding the `auto_link` command to the relevant part of the view:

```erb
              <p><%= auto_link(event.description.truncate(120, separator: /\s/)) %></p>
```
  
It is.  So can I make the cuke a better example of self documentation?  I refactor it to:

```gherkin
@vcr
Feature: List Repeating Events
  As an event creator
  So that I don't have to keep making separate events for repeating meetings
  I would like everyone to see repeats of regular events

  Background:
    Given following events exist:
      | name       | description             | category        | start_datetime          | duration | repeats | time_zone | project | repeats_weekly_each_days_of_the_week_mask | repeats_every_n_weeks |
      | Standup    | Daily standup meeting<br>woot!   | Scrum           | 2014/02/03 07:00:00 UTC | 150      | weekly  | UTC       |         |   15                                      |     1                 |
    Given the date is "2016/05/01 09:15:00 UTC"
    When I am on events index page

  Scenario: Show correct timezone for repeating event
    Then I should see "Standup"
    And the local time element should be set to "2016-05-10T07:00:00Z"

  Scenario: Do not show raw HTML in repeating event description
    Then I should not see "<br>"
```

It feels like a waste of resources to break it into two scenarios when it's the same set up and it's effectively reloading the entire system twice to check for something that could be checked in sequence.  Perhaps it would be better to get the intent of the second scenario in the step definition itself.  I adjust like so:

```
  Scenario: Show correct timezone for repeating event
    Given the date is "2016/05/01 09:15:00 UTC"
    When I am on events index page
    Then I should see "Standup"
    And the local time element should be set to "2016-05-10T07:00:00Z"
    And I should not see any HTML tags
```

Would love to adjust the background step so it wasnt' quite so wide ... I've got it down to:

```gherkin
@vcr
Feature: List Repeating Events
  As an event creator
  So that I don't have to keep making separate events for repeating meetings
  I would like everyone to see repeats of regular events

  Background:
    Given the following events exist that repeat every weekday:
      | name    | description     | category | start_datetime          | duration | time_zone |
      | Standup | always<br>woot! | Scrum    | 2014/02/03 07:00:00 UTC | 150      | UTC       |

  Scenario: Show correct timezone for repeating event
    Given the date is "2016/05/01 09:15:00 UTC"
    When I am on events index page
    Then I should see "Standup"
    And I should see "woot!"
    And the local time element should be set to "2016-05-10T07:00:00Z"
    And I should not see any HTML tags
```

I'd still like to add more explanation to the `local time element should be set to "2016-05-10T07:00:00Z"` step so that it explains that this is the raw element that the javascript library `local_time` uses to display the correct time zone to the user and that we gave up testing the execution of that, since the headless browser can't easily be manipulated to a different time zone.  Still my ideal Scenario text would be more like:

```
  Scenario: Show correct timezone for repeating event
    Given the date is a weekday in 2016
    When I am on events index page
    Then I should see the event title and description
    And I should not see any HTML tags
    And the elements needed by the local_time js library that shows times in the users timezone should be  set correctly
```

where the intention for each line is explicit.  I'll leave that for another time, as I've performed several refactorings already.  At least I now have some aspirational text to put in the new README draft.

Well that makes me feel heaps better, to actually have a [quick fix](https://github.com/AgileVentures/WebsiteOne/pull/1995) for something reported by a Premium user.  I do need to get on to that medium editor thing in order to speed up our overall process.  Even the double run of the cukes hasn't really addressed our failing javascript tests.  I guess the trick is to keep fixing things up, improving the cukes and getting those as examples into the system documentation showing how we are trying to improve things ...


