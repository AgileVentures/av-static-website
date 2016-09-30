---
title: Pride Comes Before a Fall
date: 2016-09-30
tags: production staging develop local nil demeter violation acceptance unit tests factories cucumer steps airbrake heroku migrations rollback
author: Sam Joseph
---

They say pride comes before a fall.  Looking back at yesterdays blog I was feeling pretty pleased about how we'd split things up as regards the development of the new Karma feature.  That was getting deployed in the background yesterday whilst Michael and I paired on completing the improve hangout telemetry ticket we had started.  That turned out to be relatively straightforward, although I worry about how much data we might end up storing.  That's an interesting set up that I've blogged about before and will come back to again, but is a side show to yesterday's main event, which is that in the middle of that work, we experienced a serious problem on production.

The first we heard was that AV CoFounder Thomas was getting a 500 error on his profile page on the main AgileVentures site.  He was surprised to say the least.  We were confused too; all the acceptance tests were passing.  Raoul had just deployed a chunk of changes to production (including upgrade to Ruby 3.1) and I'd kicked off a run of the new karma update rake task.  Michael was seeing failures from the rake task, and noticing that everyone now had zero karma on the main users page.  As Michael was asserting that we had a problem with the rake task, and that the database was screwed up, I took several breaths to keep calm.  Michael's assertions were true, but we had a bigger problem - the 500 errors on the individual user pages.  

I knew that the real data for the users' karma was not lost, since we calculate that from other aspects of the site.  The Karma values are re-summarised each day - they could easily be re-calculated and the ordering on the main users page fixed.  Something was causing all the individual user profile pages to fail.   Airbrake was telling us about the problem with the failing rake task, but telling us nothing about the 500 error on the user profile pages.  Airbrake posted to our #websiteone-notify channel and created a GitHub issue:

https://github.com/AgileVentures/WebsiteOne/issues/1313

![](https://www.dropbox.com/s/2i4ner1w4gka2r6/Screenshot%202016-09-30%2009.07.00.png?dl=1)

We were getting `undefined method `total=' for nil:NilClass` in a run of the KarmaCalculator:

```rb
  def perform
    user.karma.total = 0
    return if user.created_at.blank?

    user.karma.total = sum_elements
  end
```

Our Demeter Violation and a nil object, two things I had been blogging about that morning had bitten us.  I rolled the production code back to the previous deploy.  My priority was getting the user profile pages working again.  Working out the karma calculation could wait.  I was excited to be capturing all of this on the hangout video.   We could have got distracted trying to fix the karma calculation here, and all the while anyone coming to the site trying to look at an individual profile page would get a 500 error.  We had to rectify that before anything else.

I was frustrated that Airbrake was not reporting the 500 error - but that was nothing compared to when I completed the rollback to the previous working version and realised that that didn't fix the problem.  There had been migrations, so even though the code was back in the working state, the database was changed.  There was no longer a karma_points field on the user table, and I couldn't roll back the database without the migrations that were in the latest version of master.

Looking at that now, what I could/should have done is rollback the migrations before taking the code base back.  In principle that would have allowed us to get the site back into the working order it was before the deploy.  As it was, I now faced needing to roll the code forward again, roll back the migrations and then take the code back again, and all that while important parts of the site would be non-functional.  As this was all running through my cortex, I managed to get the error message associated with the individual profile pages in the general heroku logs.  This was it:

```
Started GET "/users/tattsy" for 122.175.241.121 at 2016-09-29 15:05:48 +0000
2016-09-29T15:05:48.174796+00:00 app[web.1]: Processing by UsersController#show as HTML
2016-09-29T15:05:48.256332+00:00 app[web.1]:   Rendered users/profile/_summary.html.erb (10.2ms)
2016-09-29T15:05:48.174869+00:00 app[web.1]:   Parameters: {"id"=>"tattsy"}
2016-09-29T15:05:48.314400+00:00 app[web.1]:   Rendered users/profile/_detail.html.erb (57.5ms)
2016-09-29T15:05:48.314624+00:00 app[web.1]:   Rendered users/show.html.erb within layouts/user_profile_layout (69.6ms)
2016-09-29T15:05:48.314861+00:00 app[web.1]: undefined method `hangouts_attended_with_more_than_one_participant' for nil:NilClass
2016-09-29T15:05:48.314863+00:00 app[web.1]: /app/app/views/users/profile/_detail.html.erb:65:in `_app_views_users_profile__detail_html_erb__30276934721546551_69862305882580'
```

`hangouts_attended_with_more_than_one_participant` was being called on a nil.  We'd set up that method on the user class to delegate to the single Karma object associated with the User.  That Karma object was nil.  The issue was the same as the problem with the karma update rake task was encountering.  In this case a fix for one would likely be a fix for the other, but in my mind it was critical to confirm that.  If the 500 error on the profile pages had been caused by something else we would have been fixing the wrong thing in terms of bringing the site back to functionality as fast as possible.

Now of course if I had taken the site straight back to fully operational status by rolling back the migrations before the code we could have been looking at both issues at our leisure.  However the site was still unoperational and we understood the problem and had a chance to just fix it and complete the production deploy.  We jumped into the production console and ran a procedure to create default Karma objects for all the users:

```
→ heroku run rails c -r production
Running rails c on ⬢ websiteone-production... up, run.1677
Loading production environment (Rails 4.2.6)
irb(main):001:0> User.all.each {|u| u.karma = Karma.new ; u.save! }
```

This removed the nil association from all the users, and the individual profile pages started working again.  If they hadn't (i.e. we had mis-analyzed) I would have rolled back the database and then the code, hopefully getting the site stable in it's old state and then done further analysis.  However our quick fix had worked and confirmed our hypothesis about the problem.  Still the ordering on the users page was out of whack and everyone had zero Karma.  This was easy to fix now that every user object had a non-nil karma object associated.  We took the heart of the karma update rake task and ran it direct:

```
irb(main):002:0>   User.all.each do |usr|
irb(main):003:1*     KarmaCalculator.new(usr).perform
irb(main):004:1>     usr.karma.save!
irb(main):005:1>   end
```

As it ran we could see individual Karma totals being restored.  We were getting out of the woods, but how could this have happened?  We had all sorts of acceptance tests that displayed the individual profile pages.  How could they all be passing, but deploying that code lead to an error on production?

Going over things slowly and carefully it's clear that there are many things that could have prevented this problem from occurring.  Let's look first at why our acceptance tests didn't fail.  Acceptance tests like the following look at individual profile pages:

```gherkin
  Background:
    Given the following users exist
      | first_name | last_name | email                  | 
      | Alice      | Jones     | alice@btinternet.co.uk |   

  Scenario: Having user profile page
    When I click on the avatar for "Alice"
    Then I should be on the "user profile" page for "Alice"
    And I should see the avatar for "Alice" at 250 px
    And I should see "Alice Jones"
```

We would expect a 500 error caused by a failure in the view to be caught here.  However if we look at how the user is created we see that it uses FactoryGirl.  


```rb
Given /^the following users exist$/ do |table|
  table.hashes.each do |attributes|
    FactoryGirl.create(:user, attributes)
  end
end

and it seems that our Cucumber steps are using the same factories as our RSpecs, hmmm.  Let's look at that factory:

```rb
FactoryGirl.define do
  factory :user, aliases: [:whodunnit] do
    transient do
      gplus 'youtube_id_1'
    end

    first_name { Faker::Name.first_name }
    last_name { Faker::Name.last_name }
    email { Faker::Internet.email }
    password '12345678'
    password_confirmation { password }
    display_profile true
    slug { "#{first_name} #{last_name}".parameterize }
    bio { Faker::Lorem.sentence }
    skill_list { Faker::Lorem.words(4) }
    karma { Karma.new }

    after(:create) do |user, evaluator|
      create(:authentication, provider: 'gplus', uid: evaluator.gplus, user_id: user.id)
    end
  end

end
```

Aha, it seems that our acceptance tests are not as end to end as we might like.  A default Karma is being created.  Michael and I added that `karma { Karma.new }` line for the RSpecs, not realising that we'd be impacting the Cucumber acceptance tests.  In LocalSupport we'd always avoided factory girl in Cucumber.  Of course one can say we should have been investigating the steps more thoroughly to anticipate this connection.  

Anyway, so hopefully it's clear why all the acceptance tests passed, and we can wail and gnash our teeth about the use of factories in the acceptances test, but we could have encountered this error well before production if we'd looked at the individual profile pages, or run the rake update karma task, on our local machines, develop, or staging.  That that didn't happen is an oversight, but also a reflection of our trust in the acceptance tests.

Another thing that might have helped would have been completing the full refactoring of the Karma first, although who knows, we might just have pushed out a bigger code change and had more trouble getting to the bottom of things.  Post-mortem's aside we had the remaining problem that a newly created user would not have a Karma object.  We got off a quick PR with the following that Raoul merged in quickly:


```rb
  context 'creating user' do
    it 'should not be possible to save a user with nil Karma' do
      user = User.new({ email: 'doh@doh.com', password: '12345678' })
      user.save!
      expect(user.karma).not_to be_nil
    end
    it 'should not override existing karma' do
      user = User.new({ email: 'doh@doh.com', password: '12345678' })
      user.karma = Karma.new(total: 50)
      user.save!
      expect(user.karma.total).to eq 50
    end
  end
```

```rb
  has_one :karma
  after_save :build_karma, if: -> { karma.nil? }
```

and I noticed that we were still getting some errors from the karma update rake task which looked like they were related to ill-formed participant data.  That wasn't catastrophic, but the karma update rake task would need upgrading to allow it to not get derailed by those errors; at least until we completed the full upgrade of the hangout telemetry making separate models for participants and so forth.  Although that looks like it might be a tricky migration of the legacy data.

Ironically we had been working on the profile pages during the week and they had been working fine, but we'd been jumping back and forth between different branches.  The addition of the premium upgrade button had worked fine, but that had been isolated from any karma changes until it got merged to develop.  It makes me wonder if we shouldn't prefer some kind of continuous deployment structure where we deploy to production more frequently rather than grouping things in bunches.  Or should we see yesterday's problem as an inditement of the "drive-by" coding style, where we try to make the smallest fixes possible, and avoid building on top of things until they are merged to develop?

I still hold out hope for the "drive-by" style in moderation.  We are interleaving it with domain entity development.  If anything yesterday's production fail points to the need for slightly more rigorous manual checks on local, develop and staging servers.  In my experience programming proceeds like this.  There is a period with fewer problems and increasing confidence and you tend to take bigger and bigger steps and move faster and faster, and then sometimes you are brought up short, and that forces you to move more slowly again.  The critical thing is to learn as much as you can when the mistakes happen; and remember that pride often comes before a fall :-)

Related Videos:

* [Pair Programming](https://www.youtube.com/watch?v=sraE18A_BZ8)
* ["Kent Beck" Scrum](https://www.youtube.com/watch?v=GAXcM9HHGyc)








 
