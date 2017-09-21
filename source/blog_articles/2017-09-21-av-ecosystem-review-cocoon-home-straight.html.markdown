So the answer to my question of yesterday, "Why is Acceptance Testing so hard" is that any coding is hard when you can't get error messages.  The remote debug inspector I set up yesterday allowed me to see that our Capybara/Poltergeist/PhantomJS setup could not handle ES6 string interpolations.  It raises the question about why not, but I want to push on with this feature.

The test is now failing on:

```
    And I should see a link to "multiple repo project" on github            # features/step_definitions/projects_steps.rb:84
      undefined method `split' for nil:NilClass (NoMethodError)
      ./features/step_definitions/projects_steps.rb:86:in `/^I (should not|should) see a link to "(.*?)" on github$/'
      features/projects/create_projects.feature:84:in `And I should see a link to "multiple repo project" on github'
```

because the github url is being stored in a new location that's not reflected in the project page.  The project page currently shows the GitHub link via this code:

```erb
<span class="small"><%= link_to "#{@project.github_url.split('/').last} ", @project.github_url %>on GitHub</span></p>
```

which explains the error in the test.  Just to see if it works I throw the following in the project model:

```rb
  def github_url
    source_repositories.first.url
  end
```

and I am rewarded with a completely passing acceptance test.  However I have various issues to address:

* [ ] the acceptance test does not check that the second repo has been stored
* [ ] the acceptance test is not very readable
* [ ] currently the project page does not display all the repos
* [ ] the project has various unit tests based on being able to set the github_url
* [ ] the project model validates the URL
* [ ] the process for safely migrating the existing data on production

and maybe others I haven't thought of yet, so I'm tempted to run the other project acceptance tests and see what breaks ... other acceptance tests are breaking because the create project form has changed and they can't now find the form fields they expect.  Update a few label strings in the tests and that project code to:

```rb
  def github_url
    source_repositories.first.try(:url)
  end
```
and all the project create acceptance tests are green; so I kick off a run of the rest of the project acceptance tests.  Am I being bad here?  Shouldn't I be clearly setting out my domain model and getting all my unit tests in place first?  My defense is that I'm sidling up on that, trying to use the existing tests from the outside in to give me the bigger picture here.  I very definitely don't plan to just hack the app code until all the acceptance tests pass :-)

I get the project show acceptance tests to pass with the following changed step definition:

```rb
Given(/^the following projects exist:$/) do |table|
  table.hashes.each do |hash|
    if hash[:author].present?
      u = User.find_by_first_name hash[:author]
      project = Project.new(hash.except('author', 'tags').merge(user_id: u.id))
    else
      project = default_test_author.projects.new(hash.except('author', 'tags'))
    end
    if hash[:github_url].present?
      project.source_repositories.build(url: hash[:github_url])
    end
    if hash[:tags]
      project.tag_list.add(hash[:tags], parse: true)
    end
    project.save!
  end
end
```

and the edit project acceptance failures look pretty much like form field names just need to be changed, but actually the step definition needs a further change, specifically:

```rb
    if hash[:github_url].present?
      project.source_repositories.build(url: hash[:github_url])
    else
      project.source_repositories.build
    end
```

to ensure the projects created in the background steps have the default single source repository when they start up; and with that I have all the project acceptance tests green.  Now I attack the RSpec unit and integration tests, where I expect a slew of failures that should push me towards updating the domain model effectively.

```
→ be rspec spec/models/project_spec.rb 
[Coveralls] Using SimpleCov's 'rails' settings.

Randomized with seed 60909
.F...F...F....F...........

Failures:

  1) Project#github_repo returns the proper repo name if github url exists
     Failure/Error: expect(project.github_repo).to eq 'AgileVentures/WebsiteOne'
     
       expected: "AgileVentures/WebsiteOne"
            got: ""
     
       (compared using ==)
     # ./spec/models/project_spec.rb:131:in `block (3 levels) in <top (required)>'

  2) Project#github_repo_user_name deals with hyphen gracefully
     Failure/Error: expect(subject.github_repo_user_name).to eq 'shf-project'
     
       expected: "shf-project"
            got: ""
     
       (compared using ==)
     # ./spec/models/project_spec.rb:145:in `block (3 levels) in <top (required)>'

  3) Project#save should throw error for incomplete github url
     Failure/Error: expect{ subject.github_repo_name }.to raise_error(NoMethodError, "undefined method `[]' for nil:NilClass")
       expected NoMethodError with "undefined method `[]' for nil:NilClass" but nothing was raised
     # ./spec/models/project_spec.rb:38:in `block (3 levels) in <top (required)>'

  4) Project#save should not accept invalid github url
     Failure/Error: expect(subject).to_not be_valid
       expected #<Project id: 1020, title: "Title 19", description: "Warp fields stabilize.", status: "We feel your presence.", created_at: nil, updated_at: nil, user_id: nil, slug: "title-19", github_url: "https:/github.com/google/instant-hangouts", pivotaltracker_url: nil, pitch: "'I AM the greatest!' - M. Ali", commit_count: 0, image_url: nil, last_github_update: nil> not to be valid
     # ./spec/models/project_spec.rb:33:in `block (3 levels) in <top (required)>'

Finished in 0.65807 seconds (files took 8.4 seconds to load)
26 examples, 4 failures

Failed examples:

rspec ./spec/models/project_spec.rb:129 # Project#github_repo returns the proper repo name if github url exists
rspec ./spec/models/project_spec.rb:144 # Project#github_repo_user_name deals with hyphen gracefully
rspec ./spec/models/project_spec.rb:36 # Project#save should throw error for incomplete github url
rspec ./spec/models/project_spec.rb:31 # Project#save should not accept invalid github url
```

let's take these in turn:

```
    it 'returns the proper repo name if github url exists' do
      project = build_stubbed(:project, github_url: 'https://github.com/AgileVentures/WebsiteOne')
      expect(project.github_repo).to eq 'AgileVentures/WebsiteOne'
    end
```

I'll adjust to:

```
    it 'returns the proper repo name if github url exists' do
      project = build_stubbed(:project)
      project.source_repositories.create(url: 'https://github.com/AgileVentures/WebsiteOne')
      expect(project.github_repo).to eq 'AgileVentures/WebsiteOne'
    end
```

which passes, and I assume we're not [hitting the database](https://robots.thoughtbot.com/use-factory-girls-build-stubbed-for-a-faster-test).  Using other similar fixes I have all the project specs passing, so I guess I'll run all the other specs to see if there are any other angles I'm missing.  The other thing that occurs to me is that we should have some specs for the new SourceRepository model.  I'm also unsure of the namings.  Should I have called it GitHubUrl or GitHubLink?  Does it matter?  I'd love us to be able to support bitbucket and other source repositories in the future ... anyhow, running the rest of the specs shows me that we've got a few more issues:

```
Failures:

  1) GithubCommitsJob.job stores total commit count only for projects that have a github_url
     Failure/Error: expect(project.commit_count).to be > 1
     
       expected: > 1
            got:   0
     # ./spec/jobs/github_commits_job_spec.rb:28:in `block (3 levels) in <top (required)>'

  2) GithubCommitsJob.job stores commit counts only for users that have a github_profile_url
     Failure/Error: expect(project.commit_counts.map(&:user)).to match_array(@users_with_github_profile_urls)
     
       expected collection contained:  [#<User id: 3, email: "trey@howe.co", encrypted_password: "$2a$04$plCVt32oux72jZ5KZxe9weafqhT1Uo41SIF... receive_mailings: true, timezone_offset: nil, country_code: nil, status_count: 0, deleted_at: nil>]
       actual collection contained:    []
       the missing elements were:      [#<User id: 3, email: "trey@howe.co", encrypted_password: "$2a$04$plCVt32oux72jZ5KZxe9weafqhT1Uo41SIF... receive_mailings: true, timezone_offset: nil, country_code: nil, status_count: 0, deleted_at: nil>]
     # ./spec/jobs/github_commits_job_spec.rb:33:in `block (3 levels) in <top (required)>'

  3) GithubCommitsJob.job stores correct total commit count for projects
     Failure/Error: expect(project.commit_count).to eq 3116
     
       expected: 3116
            got: 0
     
       (compared using ==)
     # ./spec/jobs/github_commits_job_spec.rb:42:in `block (3 levels) in <top (required)>'

  4) GithubCommitsJob.job stores commit counts only for projects that have a github_url
     Failure/Error: expect(project.commit_counts.count).to eq(1)
     
       expected: 1
            got: 0
     
       (compared using ==)
     # ./spec/jobs/github_commits_job_spec.rb:23:in `block (3 levels) in <top (required)>'

  5) GithubCommitsJob.job stores correct commit counts by user and project
     Failure/Error: expect(CommitCount.find_by!(project: project, user: @users[0]).commit_count).to eq 481
     
     ActiveRecord::RecordNotFound:
       Couldn't find CommitCount
     # /Users/tansaku/.rvm/gems/ruby-2.3.1/gems/activerecord-4.2.8/lib/active_record/core.rb:196:in `find_by!'
     # ./spec/jobs/github_commits_job_spec.rb:37:in `block (3 levels) in <top (required)>'

  6) GithubLastUpdatesJob#run shf-project with hyphen has correct last commit date after job run
     Failure/Error: expect(project.reload.last_github_update).not_to be_nil
     
       expected: not nil
            got: nil
     # ./spec/jobs/github_last_updates_job_spec.rb:9:in `block (4 levels) in <top (required)>'

  7) projects/show.html.erb renders a link to the project's github page
     Failure/Error: expect(rendered).to have_link("#{project.github_url.split('/').last}", :href => project.github_url)
     
     NoMethodError:
       undefined method `split' for nil:NilClass
     # ./spec/views/projects/show.html.erb_spec.rb:42:in `block (2 levels) in <top (required)>'

  8) projects/edit.html.erb renders project form labels
     Failure/Error: expect(rendered).to have_text('GitHub link')
       expected to find text "GitHub link" in "edit the Title 400 project Title Image url Description Warp fields stabilize. Status Active Closed Pending Add more repos Issue Tracker link Back $(document).on('ready', function(){ $('#source_repositories').on('cocoon:after-insert', function(e, added_repo) { var sourceRepositories = $('#project_form').find('.nested-fields'); var sourceRepositoriesSize = $(sourceRepositories).size(); if (sourceRepositoriesSize > 1) { for(var i = 1; i < sourceRepositoriesSize; i++){ $(sourceRepositories[i]).find('.repo_field_label').html('GitHub url ('+(i+1)+')') } } }); })"
     # ./spec/views/projects/edit.html.erb_spec.rb:16:in `block (2 levels) in <top (required)>'

  9) projects/edit.html.erb renders project form fields
     Failure/Error: expect(rendered).to have_field('GitHub link')
       expected to find field "GitHub link" but there were no matches
     # ./spec/views/projects/edit.html.erb_spec.rb:26:in `block (2 levels) in <top (required)>'

Finished in 1 minute 4.01 seconds (files took 9.28 seconds to load)
940 examples, 9 failures, 2 pending

Failed examples:

rspec ./spec/jobs/github_commits_job_spec.rb:27 # GithubCommitsJob.job stores total commit count only for projects that have a github_url
rspec ./spec/jobs/github_commits_job_spec.rb:32 # GithubCommitsJob.job stores commit counts only for users that have a github_profile_url
rspec ./spec/jobs/github_commits_job_spec.rb:41 # GithubCommitsJob.job stores correct total commit count for projects
rspec ./spec/jobs/github_commits_job_spec.rb:22 # GithubCommitsJob.job stores commit counts only for projects that have a github_url
rspec ./spec/jobs/github_commits_job_spec.rb:36 # GithubCommitsJob.job stores correct commit counts by user and project
rspec ./spec/jobs/github_last_updates_job_spec.rb:7 # GithubLastUpdatesJob#run shf-project with hyphen has correct last commit date after job run
rspec ./spec/views/projects/show.html.erb_spec.rb:39 # projects/show.html.erb renders a link to the project's github page
rspec ./spec/views/projects/edit.html.erb_spec.rb:11 # projects/edit.html.erb renders project form labels
rspec ./spec/views/projects/edit.html.erb_spec.rb:21 # projects/edit.html.erb renders project form fields
```

Code relating to checking commits on Github is failing ..., although those can probably be fixed by creating the Projects with associated source reposiories, and those view spec failures can probably be addressed by deleting the view specs.  Although I did also need to update this scope on the Project model:

```rb
  scope :with_github_url, ->{ includes(:source_repositories).where.not(source_repositories: { id: nil }) }
```

and with that I think I may have gotten all the specs green.  There's still a lot to do, but I'm reassured that I'm covering the majority of the angles.  I'll push up this code into the PR and tomorrow I can work on cleaning up the domain model, tightening up the unit tests, and maybe even complete the first phase of this feature?  I note also that we have a load of cruft in the spec output - would love to clear that out, but can I prioritise it?

```
Randomized with seed 51520
.....................................................Unused parameters passed to Capybara::Queries::SelectorQuery : ["/users/sign_in"]
Unused parameters passed to Capybara::Queries::SelectorQuery : ["/users/password/new"]
..Unused parameters passed to Capybara::Queries::SelectorQuery : ["/auth/github"]
.............................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................DEPRECATION WARNING: You attempted to assign a value which is not explicitly `true` or `false` ("never") to a boolean column. Currently this value casts to `false`. This will change to match Ruby's semantics, and will cast to `true` in Rails 5. If you would like to maintain the current behavior, you should explicitly handle the values you would like cast to `false`. (called from block (3 levels) in <top (required)> at /Users/tansaku/Documents/GitHub/AgileVentures/WebsiteOne/spec/models/event_spec.rb:244)
.DEPRECATION WARNING: You attempted to assign a value which is not explicitly `true` or `false` ("never") to a boolean column. Currently this value casts to `false`. This will change to match Ruby's semantics, and will cast to `true` in Rails 5. If you would like to maintain the current behavior, you should explicitly handle the values you would like cast to `false`. (called from block (3 levels) in <top (required)> at /Users/tansaku/Documents/GitHub/AgileVentures/WebsiteOne/spec/models/event_spec.rb:239)
........DEPRECATION WARNING: You attempted to assign a value which is not explicitly `true` or `false` ("never") to a boolean column. Currently this value casts to `false`. This will change to match Ruby's semantics, and will cast to `true` in Rails 5. If you would like to maintain the current behavior, you should explicitly handle the values you would like cast to `false`. (called from block (3 levels) in <top (required)> at /Users/tansaku/Documents/GitHub/AgileVentures/WebsiteOne/spec/models/event_spec.rb:158)
.DEPRECATION WARNING: You attempted to assign a value which is not explicitly `true` or `false` ("never") to a boolean column. Currently this value casts to `false`. This will change to match Ruby's semantics, and will cast to `true` in Rails 5. If you would like to maintain the current behavior, you should explicitly handle the values you would like cast to `false`. (called from block (3 levels) in <top (required)> at /Users/tansaku/Documents/GitHub/AgileVentures/WebsiteOne/spec/models/event_spec.rb:174)
.DEPRECATION WARNING: You attempted to assign a value which is not explicitly `true` or `false` ("never") to a boolean column. Currently this value casts to `false`. This will change to match Ruby's semantics, and will cast to `true` in Rails 5. If you would like to maintain the current behavior, you should explicitly handle the values you would like cast to `false`. (called from block (3 levels) in <top (required)> at /Users/tansaku/Documents/GitHub/AgileVentures/WebsiteOne/spec/models/event_spec.rb:142)
............................Unused parameters passed to Capybara::Queries::SelectorQuery : ["/events/event-131"]
..Unused parameters passed to Capybara::Queries::SelectorQuery : ["/projects/title-240"]
......................................*...............................Unused parameters passed to Capybara::Queries::SelectorQuery : ["/auth/github?origin="]
Unused parameters passed to Capybara::Queries::SelectorQuery : ["/auth/gplus?origin="]
.Unused parameters passed to Capybara::Queries::SelectorQuery : ["/users/sign_up"]
Unused parameters passed to Capybara::Queries::SelectorQuery : ["/users/password/new"]
..Unused parameters passed to Capybara::Queries::SelectorQuery : ["/auth/github"]
Unused parameters passed to Capybara::Queries::SelectorQuery : ["/auth/gplus"]
......................................WARNING: Using the `raise_error` matcher without providing a specific error or message risks false positives, since `raise_error` will match when Ruby raises a `NoMethodError`, `NameError` or `ArgumentError`, potentially allowing the expectation to pass without even executing the method you are intending to call. Actual error raised was #<ActiveRecord::StatementInvalid: PG::NotNullViolation: ERROR:  null value in column "user_id" violat...
: UPDATE "authentications" SET "user_id" = $1, "updated_at" = $2 WHERE "authentications"."id" = $3>. Instead consider providing a specific error class or message. This message can be suppressed by setting: `RSpec::Expectations.configuration.on_potential_false_positives = :nothing`. Called from /Users/tansaku/Documents/GitHub/AgileVentures/WebsiteOne/spec/models/authentication_spec.rb:12:in `block (2 levels) in <top (required)>'.
.............................*.......WARNING: Using the `raise_error` matcher without providing a specific error or message risks false positives, since `raise_error` will match when Ruby raises a `NoMethodError`, `NameError` or `ArgumentError`, potentially allowing the expectation to pass without even executing the method you are intending to call. Actual error raised was #<ActiveRecord::RecordNotFound: User has not exposed his profile publicly>. Instead consider providing a specific error class or message. This message can be suppressed by setting: `RSpec::Expectations.configuration.on_potential_false_positives = :nothing`. Called from /Users/tansaku/Documents/GitHub/AgileVentures/WebsiteOne/spec/controllers/users_controller_spec.rb:67:in `block (4 levels) in <top (required)>'.
..................................................................Unused parameters passed to Capybara::Queries::SelectorQuery : ["/events/1574"]
..Unused parameters passed to Capybara::Queries::SelectorQuery : ["/events/1576"]
..Unused parameters passed to Capybara::Queries::SelectorQuery : ["/events/1578"]
.Unused parameters passed to Capybara::Queries::SelectorQuery : ["/events/1579"]
.Unused parameters passed to Capybara::Queries::SelectorQuery : ["/events/1580"]
.Unused parameters passed to Capybara::Queries::SelectorQuery : ["/code"]
.Unused parameters passed to Capybara::Queries::SelectorQuery : ["/grow"]
.Unused parameters passed to Capybara::Queries::SelectorQuery : ["/learn"]
.Unused parameters passed to Capybara::Queries::SelectorQuery : ["/pair"]
.Unused parameters passed to Capybara::Queries::SelectorQuery : ["/events/1585"]
.......................................................Unused parameters passed to Capybara::Queries::SelectorQuery : ["/sponsors"]
...............................
```

hmmm ...

