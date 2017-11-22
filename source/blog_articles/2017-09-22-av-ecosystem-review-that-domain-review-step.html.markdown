---
title: AV EcoSystem Review That Domain Review Step
date: 2017-09-22
tags: 
author: Sam Joseph
---

![domain review](../images/domain_review.jpg)

Okay, so I have the multiple repository functionality basically working, but it's time to clear up and make things right ... here's my checklist from yesterday:

* the acceptance test does not check that the second repo has been stored
* the acceptance test is not very readable
* currently the project page does not display all the repos
* the project has various unit tests based on being able to set the github_url
* the project model validates the URL
* the process for safely migrating the existing data on production
* the old hand rolled js from my initial attempt is hanging around

So attacking from the unit test side I notice that we do not mention the `with_github_url` scope anywhere in our tests, which I can rectify with the following:

```rb
  context '.with_github_url' do
    it 'returns all projects that have at least one source repository' do
      project = FactoryGirl.create(:project)
      project.source_repositories.create(url: 'https://github.com/AgileVentures/shf-project')
      project2 = FactoryGirl.create(:project)
      project2.source_repositories.create(url: 'https://github.com/AgileVentures/shf-project2')
      expect(Project.with_github_url).to include(project, project2)
    end

    it 'does not return projects that do not have a source repository' do
      project = FactoryGirl.build(:project)
      expect(Project.with_github_url).not_to include(project)
    end
  end
```

although I can't actually run it as I'm waiting for a cucumber run to complete (got to work on the performance there).  That gap allows me to agonize about whether this unit test is sufficiently rigorous, or if it simply testing the existing Rails architecture?  I think it's the right thing.  It's providing an executable check that the class method `.with_github_url` is doing what we expect it to do.  

Bigger picture quickly, I'm not planning to immediately remove the github_url field from the projects model.  We'll need a task to migrate all of them in to source repository models, and based on previous pain I'll be ensuring we have a follow up task in a different PR that we deploy to production separately to delete that field later on when the dust has settled.

Okay so the cukes finished running.  I got a couple of errors in the way I'm using FactoryGirl/build_stubbed.  Fix those and I've got the following Project RSpec output:

```
→ be rspec -fd spec/models/project_spec.rb 
[Coveralls] Using SimpleCov's 'rails' settings.

Randomized with seed 34619

Project
  #github_repo_user_name
    deals with hyphen gracefully
  #codeclimate_gpa
    returns the CodeClimate GPA
  #search
    should return paginated projects with 5 per page
  #url_for_me
    returns correct url for show action
    returns correct url for other actions
  #members
    returns followers of the project who have a public profile
  #contribution_url
    returns the url for the project github contribution page
  #jitsi_room_link
    returns correct link
  .with_github_url
    returns all projects that have at least one source repository
    does not return projects that do not have a source repository
  #youtube_tags
    returns the tags for project including the project title
  #github_repo
    returns the proper repo name if github url exists
    returns blank if github url does not exist
  #members_tags
    returns the tags for project members with thier youtube user names
  #save
    should not accept invalid Pivotal Tracker URL
    should throw error for incomplete github url
    should be invalid without status
    should be invalid without description
    should not accept invalid github url
    has public-activity enabled
    should respond to #create_activity
    should be a valid with all the correct attributes
    should be invalid without title
    Pivotal Tracker URL
      should accept new pivotal traker url format
      should accept the subject id and convert that into a valid URL
      should correct mistakes in pivotal tracker url
    Updating friendly ids
      should still be able to find the project by its old id
      should regenerate the project's friendly id when the title changes

Finished in 0.65804 seconds (files took 8.15 seconds to load)
28 examples, 0 failures
```

I'm not sure that this output is telling the domain story that I'd like, but let's move on.  I was thinking that we would have to move the github url validation into source repositories, and I think we will ultimatley, but it seems to be currently allowing us to validate urls on the first repo, if I'm reading this all correctly.

Since I've created the test (for the scope) after the code (bad programmer) I quickly break the the scope and check that the test fails, which it does.  This code

```rb
  scope :with_github_url, ->{ includes(:source_repositories).where(source_repositories: { id: nil }) }
```

causes this error

```sh
  1) Project.with_github_url returns all projects that have at least one source repository
     Failure/Error: expect(Project.with_github_url).to include(project, project2)
     
       expected #<ActiveRecord::Relation []> to include #<Project id: 1, title: "Title 1", description: "Warp fields stabilize.", status: "We feel your prese...l, pitch: "'I AM the greatest!' - M. Ali", commit_count: 0, image_url: nil, last_github_update: nil> and #<Project id: 2, title: "Title 2", description: "Warp fields stabilize.", status: "We feel your prese...l, pitch: "'I AM the greatest!' - M. Ali", commit_count: 0, image_url: nil, last_github_update: nil>
       Diff:
       @@ -1,3 +1,2 @@
       -[#<Project id: 1, title: "Title 1", description: "Warp fields stabilize.", status: "We feel your presence.", created_at: "2017-09-22 09:12:29", updated_at: "2017-09-22 09:12:29", user_id: nil, slug: "title-1", github_url: nil, pivotaltracker_url: nil, pitch: "'I AM the greatest!' - M. Ali", commit_count: 0, image_url: nil, last_github_update: nil>,
       - #<Project id: 2, title: "Title 2", description: "Warp fields stabilize.", status: "We feel your presence.", created_at: "2017-09-22 09:12:29", updated_at: "2017-09-22 09:12:29", user_id: nil, slug: "title-2", github_url: nil, pivotaltracker_url: nil, pitch: "'I AM the greatest!' - M. Ali", commit_count: 0, image_url: nil, last_github_update: nil>]
       +[]
       
     # ./spec/models/project_spec.rb:174:in `block (3 levels) in <top (required)>'

```
So then I quickly add a simple test of the SourceRepository:

```rb
describe SourceRepository, type: :model do
  it { is_expected.to belong_to :project}
end
```

which failed in the way I expected when I broke the SourceRepository.  The verbose RSpec output here is much more like what I expect as a description of my domain model:

```
SourceRepository
  should belong to project
```

I add the following to the Project spec:

```rb
  it { is_expected.to have_many :source_repositories}
  it { is_expected.to have_many :documents}
  it { is_expected.to have_many :event_instances}
  it { is_expected.to have_many :commit_counts}

  it { is_expected.to belong_to :user}
```  

and I get some more domain model oriented chatter from RSpec when running the Project spec:

```sh
Project
  should belong to user
  should have many source_repositories
  should have many documents
  should have many event_instances
  should have many commit_counts
```

I remove the hand-rolled js I started this feature on.   I rebase to develop so that I can do `git diff develop` and look through the specific changes I made, which are:

1) upgrade poltergeist and add the cocoon gem
2) adjust the projects controller to allow source repository attributes and to build a single default source repo for new projects
3) adjust the way the `with_github_url` scope works
4) update the project form to give it an id, and replace the current `github_url` text field with a nested source repo field(s) (cocoon gem syntax)
5) added js to update source repository form field names to include numerics
6) added a partial for creating new source repo fields
7) migration to add the source repos table
8) added a create projects feature and moved the create related scenarios there
9) added a remote debugging step for cucumber
10) removed the puffing billy proxy from the poltergeist config to allow remote debugging
11) added factories and specs for source repo model
12) adjusted various tests to work with the updated domain model
13) addded more tests to Project to make the domain model more explicit
14) deleted view specs

Hmmmm.  So the key missing thing in some ways is that we're not displaying the multiple repos and the acceptance tests are not as extensive or as readable as I would like, but I have a hunch that I need to get this into production with some kind of rake task to move the data in the github_url in the Project model into the source_repository, but I've created a new getter that's blocking my access.  This [SO post](https://stackoverflow.com/questions/21835116/how-can-i-overwrite-a-getter-method-in-an-activerecord-model) tells me I can reference the original active-record field via self[:github_url], so I could have a task like this:

```
namespace :db do
  desc "migrate from github url to source repository domain model (copies github_url field to source repo model)"
  task migrate_from_github_url_to_source_repository: :environment do
    Project.all.each do |project|
      project.create(:source_repository, url: project[:github_url])
    end
  end

end
```

btw, creating the task outline with `rails g task db migrate_from_github_url_to_source_repository` was handy - thanks Rails!

So then I agonize about whether I should write a test for this one off task ... of course thoughtbot has an [article](https://robots.thoughtbot.com/test-rake-tasks-like-a-boss) on it.  But I'm out of time, will have to push that to next week, or to a refactoring chore - that's where all the acceptance test changes will be going anyway.  This migration is the pivotal componet so I'll test that first thing on Monday I guess ...




