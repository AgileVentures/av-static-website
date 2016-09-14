---
title: Unexpected Progress and Twin Stakeholder Strategies
date: 2016-07-17
tags: coffeesscript hubot chatbot node express sinatra production staging heroku redis node debugging javascript
author: Sam Joseph
---

Yesterday was one of those relatively rare days when implementing a feature turned out to be less work than expected.  More often than not what seems like it should be simple to achieve, ends up taking 10 times longer than expected (VCR, I'm looking at you).  Yesterday we sat down to work on something that a few premium users had suggested, which was the ability to see a list of AgileVentures Mentors.

Looking through the existing codebase we saw that User model employed the [acts\_as\_taggable\_on](https://github.com/mbleigh/acts-as-taggable-on) gem to associate titles and skills with AgileVentures users:

```ruby
class User < ActiveRecord::Base

  acts_as_taggable_on :skills, :titles

  # rest of class omitted for brevity
end
```

This made it pretty simple to add "Mentor" titles to users and set up a quick "Mentor Only" view with the addition of a new route and model method:

```ruby
# routes.rb

get '/mentors' => 'users#index', defaults: {title: 'Mentor'}
```

```ruby
class User < ActiveRecord::Base

  acts_as_taggable_on :skills, :titles

  def self.filter_if_title title
    return User.all if title.blank?
    User.tagged_with(title)
  end

  # rest of class omitted for brevity
end
```

and the appropriate controller hookup

```
def index
  @users = User.filter_if_title(params[:title])
               .includes(:status, :titles)
               .filter(set_filter_params)
               .allow_to_display
               .by_create
# rest of method omitted for brevity
end
```

This did leave us with a rather complex controller method which CodeClimate flagged for us.  Of course we drove all of the above via a Cucumber acceptance test:

```gherkin
  Background:
    And the following users exist
      | first_name | last_name | email                  | title_list      |
      | Alice      | Jones     | alicejones@hotmail.com | Mentor          |
      | Bob        | Butcher   | bobb112@hotmail.com    | Premium         |
      | John       | Jenkins   | john@hotmail.com       | Premium, Plus   |
    
  Scenario: see mentors
    Given I am on the mentors page
    Then I should see "Alice Jones"
    And I should not see "Bob Butcher"
    And I should see "Mentors Directory"
    And I should see "Check out our 1 awesome mentor from all over the globe"
```

and appropriate unit tests for the model:

```ruby
  describe '.filter_if_title' do
    let!(:mentor) { FactoryGirl.create(:user, title_list: 'Mentor') }
    let!(:user) { FactoryGirl.create(:user, title_list: 'Premium') }

    it 'returns mentors' do
      expect(User.filter_if_title('Mentor')).to include mentor
    end

    it 'does not return non-mentors' do
      expect(User.filter_if_title('Mentor')).not_to include user
    end

    it 'returns all users if title is empty string' do
      expect(User.filter_if_title('')).to include mentor, user
    end

    it 'returns all users if title is nil' do
      expect(User.filter_if_title(nil)).to include mentor, user
    end
  end
```

About 90 minutes to get everything working, and quickly we had something that we can use to see if a list of the AV mentors will have any impact on premium membership sign up.

Initially I was looking at the unit test we added and thinking this doesn't buy us much over the acceptance test, but Michael pointed out the importance of testing the handling of the situation where there is no title parameter; and of course that's the key here.  We're re-using the existing users index view to make all this quick and simple, and we want that to carry on working for normal views of the user index. We're overloading the users index method, but mentors (or premium or premium plus members) are all types of user, and so parameterising the existing listing mechanism to get views of subsets of the resource feels cleaner than setting up other actions on the controller that correspond to non-RESTful routes like `users/mentors` or similar.

Of course what we're seeing here is the emergence of new concepts in our domain model.  In the long run we may well want classes to represent Mentors, Premium Members and so on.  Superficially it would make sense for these classes to inherit from User, and we've got all the fun of Rails single table inheritance to play with, but there's no rush.  Let's see how this functionality actually impacts our ability to sign up additional premium members.

Reflecting on the code I can see that we missed some details about how filtering was already implemented in the UserController.  Just in the process of writing this blog I noticed something we didn't see yesterday, which is that the User model is including a `Filterable` concern:

```ruby
module Filterable
  extend ActiveSupport::Concern

  module ClassMethods
    def filter(filtering_params)
      results = self.where(nil)
      filtering_params.each do |key, value|
        results = results.public_send(key, value) if value.present?
      end
      results
    end
  end
end
```

which is set up to allow filtering the users by timezone, project and whether they are online.  Looking back over this I imagine we can consolidate and reduce the complexity of the old and new filtering.  We probably got the feature out faster by creating our own method on the model.  The filterable module above almost has all the functionality we need, but I'm not immediately sure if it will work with the tagging plugin.  Maybe it would if we adjusted our route like so  :


```ruby
# routes.rb

get '/mentors' => 'users#index', defaults: {title_tags: 'Mentor'}
```

So many options, so much code to experiment with!  So much paranoia that any piece of public code might be sneered at for failing to have DRYed one thing out, or followed a particular code heuristic.   However I can calm my paranoia by focusing on the key metric - are we making AgileVentures financially sustainable?  Is making [refactoring tickets](https://github.com/AgileVentures/WebsiteOne/issues/1203) that you maybe never get to the lowest of the low?  Better than just doing nothing I guess.  At least from yesterday's work we have the tests that will allow us to quickly assess if some alternate consolidating approaches will work.

Seeing the filtering module the following day does underline how one keeps getting new information, and that information keeps changing what looks like a sensible way to refactor.  Refactoring good - rushed refactoring hmmm ...

Given that the above took less time than expected it created space for us to work on the outstanding issues on the AV static site:

[https://github.com/AgileVentures/av-static-website/issues/42](https://github.com/AgileVentures/av-static-website/issues/42)

We'd had a lot of great feedback from a few users during an alpha release, but the workload from the MOOC and agonizing about how to make the Premium AgileVentures model work had been causing me to question the static site strategy.  For a long time there'd been general agreement it would be good to have a "showcase" site aimed at non-profits.  The main AgileVentures site is aimed at developers getting together to help non-profits, and we have a non-profit sign up form for charities to sign up their interest in working with our developers.  However it makes intuitive sense that we can help more non-profits if we have a site explaining how we can help them.  Also part of the premium package is the idea that membership makes you eligible for participation in paid projects, and we'd love to attract non-profits who were willing to pay for higher levels of service and support, which would in turn attract in more premium members.

We've been chatting with sales and marketing specialists who are keen to get our non-profit offering nailed down in lots of detail, which seems to involve specifying every aspect of the offering and man-hour costs up front.  I'd been feeling dispirited about this kind of work up-front when previous experience with paying projects suggests we should be focusing on payment per feature rather than payment for hour.  Also, the premium membership gives us incremental feedback.  As we gradually sign up more members we can tell how we are doing - we can get feedback from each member, slowly evolving and improving the offering.

Lots of documentation work up front to describe exactly how we are going to work with more and more non-profits doesn't feel very Agile, and the biggest danger is that there's a big delay until we can assess the effectiveness of all that work.  So I'd put the static site on a back burner.  However the Mentor titling functionality suddenly makes a lot of non-coding "charity business model" experiments available.  That gave me the emotional energy to tackle some of the detailed feedback about the static site and so we got a few things done including switching from http://showcase.agileventures.org to [http://nonprofits.agileventures.org](http://nonprofits.agileventures.org) - getting the emails all pointing to [nonprofits@agileventures.org](mailto:nonprofits@agileventures.org) (for consistency) and adjusting some of the icons on the site.

I'm now feeling confident that we can finish a last couple of design tweaks today to improve navigation on the site, and switch from the slow "Ghost on Heroku" blog to a middleman blog that will also make the site more maintainable.  There are more content tweaks that will need to go in next week, but it should mean there aren't any more huge coding barriers to pushing both sides of our "twin strategy charity business model" bringing benefits to both developer and non-profit stakeholders.  Maybe we should be carefully focusing on one stakeholder or the other, but you only live once right? :-) 


