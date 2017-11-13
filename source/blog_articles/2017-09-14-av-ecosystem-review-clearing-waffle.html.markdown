---
title: AV EcoSystem Review Clearing Waffle
date: 2017-09-14
tags: 
author: Sam Joseph
---

![waffle](../images/waffle.jpg)

So I woke at 4am this morning and couldn't go back to sleep.  I lay with my thoughts for 90 minutes before I couldn't bear it any longer and put on audio comedy from Christopher Titus.  Painful thoughts about kids football and tangled electron apps.  Now I'm having trouble focusing on the keyboard, but want to enjoy having cleared the "Please Check" column of the WebSiteOne waffle board yesterday:

![](https://dl.dropbox.com/s/x9br4bz8lefcz2l/Screenshot%202017-09-14%2009.27.24.png?dl=1)

I've just tested the two merged chunks of code on develop and they're working so I'll move them to staging:

```
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/WebsiteOne (1750_client_type)]$ 
→ git checkout develop
Switched to branch 'develop'
Your branch is up-to-date with 'origin/develop'.
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/WebsiteOne (develop)]$ 
→ git pull origin develop
remote: Counting objects: 15, done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 15 (delta 12), reused 14 (delta 12), pack-reused 0
Unpacking objects: 100% (15/15), done.
From http://github.com/AgileVentures/WebsiteOne
 * branch              develop    -> FETCH_HEAD
   5c998b01..7309fb7d  develop    -> origin/develop
Updating 5c998b01..7309fb7d
Fast-forward
 app/views/events/_form.html.erb          |  2 +-
 app/views/layouts/_footer.html.erb       |  1 +
 features/basic_layout.feature            |  1 +
 features/events/client_meetings.feature  | 44 ++++++++++++++++++++++++++++++++++++++++++++
 features/events/show_event.feature       |  8 ++++++++
 features/step_definitions/event_steps.rb |  4 ++++
 6 files changed, 59 insertions(+), 1 deletion(-)
 create mode 100644 features/events/client_meetings.feature
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/WebsiteOne (develop)]$ 
→ git checkout staging
Switched to branch 'staging'
Your branch is up-to-date with 'origin/staging'.
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/WebsiteOne (staging)]$ 
→ git rebase develop
First, rewinding head to replay your work on top of it...
Fast-forwarded staging to develop.
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/WebsiteOne (staging)]$ 
→ git push origin staging
Total 0 (delta 0), reused 0 (delta 0)
To http://github.com/AgileVentures/WebsiteOne
   5c998b01..7309fb7d  staging -> staging
```

So I'll double check on my proposed changes to the Premium page in the marketing meeting today. I've managed a little run down the "in progress" tickets, checking in with what others are doing, and given that all the other changes to Premium pages are dependent on feedback on the currently proposed change to the Premium page, I think I can allow myself to take a stab at the "multiple source repositiory" ticket:

[https://github.com/AgileVentures/WebsiteOne/issues/761](https://github.com/AgileVentures/WebsiteOne/issues/761)

and I moved the project scenarios round, and did a first pass at a scenario to cover multiple repo submission:

```gherkin
  @javascript
  Scenario: Saving a new project with multiple repositories: success
    Given I am logged in
    And I am on the "Projects" page
    When I click the very stylish "New Project" button
    When I fill in "Title" with "multiple repo project"
    And I fill in "Description" with "has lots of code"
    And I fill in "GitHub link" with "http://www.github.com/new"
    And I click "Add repo"
    And I fill in "GitHub link 2" with "http://www.github.com/new2"
    And I fill in "Issue Tracker link" with "http://www.waffle.com/new"
    And I select "Status" to "Active"
    And I click the "Submit" button
    Then I should be on the "Show" page for project "multiple repo project"
    And I should see "Project was successfully created."
    And I should see:
      | Text          |
      | "multiple repo project       |
      | has lots of code |
      | ACTIVE        |
    And I should see a link to "multiple repo project" on github
    And I should see a link to "multiple repo project" on Pivotal Tracker
```

and I found a set of links on dynamically adding extra fields in Rails:

* [https://stackoverflow.com/questions/42540858/add-more-fields-to-form-dynamically-in-rails](https://stackoverflow.com/questions/42540858/add-more-fields-to-form-dynamically-in-rails)
* [http://jyrkis-blogs.blogspot.co.uk/2014/06/adding-fields-on-fly-with-ruby-on-rails.html](http://jyrkis-blogs.blogspot.co.uk/2014/06/adding-fields-on-fly-with-ruby-on-rails.html)
* [https://stackoverflow.com/questions/25588101/rails-fields-for-and-javascript-to-dynamically-add-and-remove-fields-in-a-form](https://stackoverflow.com/questions/25588101/rails-fields-for-and-javascript-to-dynamically-add-and-remove-fields-in-a-form)
* [https://stackoverflow.com/questions/35185689/rails-4-complex-forms-and-nested-attributes](https://stackoverflow.com/questions/35185689/rails-4-complex-forms-and-nested-attributes)

Test is ugly but will get into that and the implementation tommorrow.  In the meantime staging deployed - features are working there and I'm pushing to production.  Clear up, clear up, clear up ...

