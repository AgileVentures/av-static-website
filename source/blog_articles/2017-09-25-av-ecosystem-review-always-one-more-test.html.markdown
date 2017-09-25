So all of last week's ecosystem review blogs were devoted to this new multiple repository feature.  And I think today too.  I hope today is the last since I really want to get on to clearing up some other content work and getting the new Code of Conduct front and center.  So I've just pulled out the latest develop and used the new `rake vcr_billy_caches:reset` command to clean up the caches.  Bit difficult to remember, and I did update the docs?  The answer is no :-( but done now.  So I think I already have tests of rake commands, the style of which I can copy to test our data migration ... unlike [thoughtbot](https://robots.thoughtbot.com/test-rake-tasks-like-a-boss) I've been going with Cucumber, which might run a little slower, but I can more easily see the data setup, so I stamp out a devops feature like so:

```gherkin
@vcr @rake
Feature: Update from github urls to source repository model
  As a website admin,
  I want to move existing github urls to source repositories
  So that the system supports multiple repositories per project

  Background: projects have been added to database
    Given the following legacy projects exist:
      | title        | description         | github_url                                    | status   | commit_count |
      | WebsiteTwo   | awesome autograder  | https://github.com/AgileVentures/WebsiteTwo   | active   | 1            |
      | WebsiteOne   | website one project | https://github.com/AgileVentures/WebsiteOne   | inactive | 1            |
      | edx          | MOOC platform       | https://github.com/edx                        | active   | 1            |
      | Unity        | Unity project       |                                               | active   | 1            |
      | LocalSupport | Scheduler queue     | https://github.com/AgileVentures/LocalSupport | active   | 1            |

  Scenario: Run the data migration to copy github urls to source repositories
    When I run the rake task for migrating github urls
    Then I should see projects with following details:
      | title        | status   | github_url                                    |
      | WebsiteTwo   | active   | https://github.com/AgileVentures/WebsiteTwo   |
      | WebsiteOne   | inactive | https://github.com/AgileVentures/WebsiteOne   |
      | edx          | active   | https://github.com/edx                        |
      | Unity        | active   |                                               |
      | LocalSupport | active   | https://github.com/AgileVentures/LocalSupport |
```

which means I need a couple of new step definitions:

```rb
When(/^I run the rake task for migrating github urls$/) do
  $rake['db:migrate_from_github_url_to_source_repository'].execute
end

Given(/^the following legacy projects exist:$/) do |table|
  # table is a table.hashes.keys # => [:title, :description, :github_url, :status, :commit_count]
  table.hashes.each do |hash|
    project = default_test_author.projects.new(hash.except('author', 'tags'))
    project.save!
  end
end
```

which as usual requires an update to the `@rake` hook:

```rb
Before('@rake') do |scenario|
  unless $rake
    require 'rake'
    Rake.application.rake_require 'tasks/scheduler'
    Rake.application.rake_require 'tasks/migrate_plans'
    Rake.application.rake_require 'tasks/create_plans'
    Rake.application.rake_require 'tasks/db'
    Rake::Task.define_task(:environment)
    $rake = Rake::Task
  end
end
```

which allowed me to isolate one syntax error in my rake task with this error:

```
    When I run the rake task for migrating github urls                        # features/step_definitions/devops_steps.rb:67
      undefined method `create' for #<Project:0x007ffabb789318>
      Did you mean?  created_at (NoMethodError)
      ./lib/tasks/db.rake:5:in `block (3 levels) in <top (required)>'
      ./lib/tasks/db.rake:4:in `block (2 levels) in <top (required)>'
      ./features/step_definitions/devops_steps.rb:68:in `/^I run the rake task for migrating github urls$/'
      features/devops/migrate_github_urls.feature:17:in `When I run the rake task for migrating github urls'
```

so I updated the task to:

```
namespace :db do
  desc "migrate from github url to source repository domain model (copies github_url field to source repo model)"
  task migrate_from_github_url_to_source_repository: :environment do
    Project.all.each do |project|
      project.source_repositories.create(url: project[:github_url])
    end
  end
end
```

which got the task running, but I was not see the projects I wanted:

```
  Scenario: Run the data migration to copy github urls to source repositories # features/devops/migrate_github_urls.feature:16
    When I run the rake task for migrating github urls                        # features/step_definitions/devops_steps.rb:67
    Then I should see projects with following details:                        # features/step_definitions/projects_steps.rb:126
      | title        | status   | github_url                                    |
      | WebsiteTwo   | active   | https://github.com/AgileVentures/WebsiteTwo   |
      | WebsiteOne   | inactive | https://github.com/AgileVentures/WebsiteOne   |
      | edx          | active   | https://github.com/edx                        |
      | Unity        | active   |                                               |
      | LocalSupport | active   | https://github.com/AgileVentures/LocalSupport |

      expected: 0
           got: 1

      (compared using ==)
       (RSpec::Expectations::ExpectationNotMetError)
      ./features/step_definitions/projects_steps.rb:131:in `block (2 levels) in <top (required)>'
      ./features/step_definitions/projects_steps.rb:129:in `each'
      ./features/step_definitions/projects_steps.rb:129:in `/^I should see projects with following details:$/'
      features/devops/migrate_github_urls.feature:18:in `Then I should see projects with following details:'
```

(wishes he could get the green red and blue in github syntax markup for cuke and rspec output ...) and I see that the step definition appears to be hard coded to be doing something for commit counts:

```rb
Then(/^I should see projects with following details:$/) do |table|
  # table is a Cucumber::Core::Ast::DataTable
   projects = table.hashes
   projects.each do | project | 
      updated_project = Project.find_by_title(project["title"])
      expect(updated_project.commit_count).to eq(project["commit_count"].to_i)
   end
end
```

I guess I'll rename that to be more explicit about what it's doing and similarly for my own one (could create some generic method that did what the step said, but not now):

```rb
Then(/^I should see projects looked up by title with first source repository same as github_url:$/) do |table|
  # table is a Cucumber::Core::Ast::DataTable
  projects = table.hashes
  projects.each do | project |
    updated_project = Project.find_by_title(project["title"])
    expect(updated_project.github_url).to eq(project["github_url"])
    expect(updated_project.source_repositories.first.url).to eq(project["github_url"])
  end
end
```
```rb
Then(/^I should see projects looked up by title with the correct commit count:$/) do |table|
  # table is a Cucumber::Core::Ast::DataTable
  projects = table.hashes
  projects.each do | project |
    updated_project = Project.find_by_title(project["title"])
    expect(updated_project.commit_count).to eq(project["commit_count"].to_i)
  end
end
```

which all now passes, but since I created the task before the test, I need to break the test; which I can do by commenting out the rake step and pleasingly that gives me the right fail.  Okay, I think I can pack this up and if the CI passes we can get onto develop and start bigger tests ...


