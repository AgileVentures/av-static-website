Over Thursday and Friday I pushed out what I'm calling an "escalating call to action" on the main AgileVentures site.  The idea had come up in a discussion with Michael in a scrum earlier in the week in which we'd been talking about what to display when the user was already logged in, because our initial stab at a call to action "Sign up now to start coding" was being shown to all users, independent of their logged in status:

!["Sign up now to start coding" banner button](https://www.dropbox.com/s/yx69fbv0axhe7kc/Screenshot%202017-02-06%2009.37.58.png?dl=1)

The impetus to create/add this single "call to action" had come from a marketing meeting at the beginning of the week where we were looking at the sign up stats, and thinking in terms of funnels through the site.  Of course like all things there were many connections.  Ashley had talked a few months back about the site not really directing folks to take a particular action.  I've been agitating to think about things in terms of the lean startup funnel:

![lean startup funnel](https://leansteps.files.wordpress.com/2012/10/funnel.png)

It seemed to follow naturally that we might make the call to action vary according to the status of who was on the site, like so:

* Guest User --> "Sign Up now to start coding!"
* Logged in User --> "Upgrade to Premium for more support"
* Premium User -->  "Upgrade to Premium Mob for group sessions with a Mentor"

On Thursday evening I spiked the view like so:

```erb
<% if current_user %>
    <% if current_user.membership_type == 'Basic' %>
        <% path = new_subscription_path %>
        <% message = 'Upgrade to Premium for additional support' %>
    <% elsif current_user.membership_type == 'Premium' %>
        <% path = new_subscription_path(plan: 'premiummob') %>
        <% message = 'Upgrade to Premium Mob for group sessions with a Mentor' %>
    <% end %>
<% else %>
    <% path = new_user_registration_path %>
    <% message = 'Sign up now to start coding!' %>
<% end %>

<% if path && message %>
<a href="<%= path %>" class="btn btn-primary btn-block call-to-action " style="margin: auto; margin-bottom: 10px;"><span><%= message %></span></a>
<% end %>
```

Now this is nasty logic in the view, but it was also an experiment.  I wanted to focus on the business logic in one place, not on the process of pulling that business logic into a model or service.  If I had a secure income it might be another story, but I got something basic working.  Then I wrapped it in tests, breaking it as I went along:

```gherkin
@vcr
Feature: Escalating Call to Action
  As a site admin
  So that we can move towards sustainability
  I would like users to see an escalating call to action depending on their subscription

  Background:
    Given the following plans exist
      | name        | id         | amount | free_trial_length_days |
      | Premium     | premium    | 1000   | 7                      |
      | Premium Mob | premiummob | 2500   | 0                      |

  Scenario: Guest user sees call to action which links to sign up page
    Given I am on the "home" page
    When I click "Sign up now to start coding!"
    Then I should be on the "sign up" page

  Scenario: Logged in use sees call to action which links to premium subscription page
    Given I am logged in
    And I am on the "home" page
    When I click "Upgrade to Premium for additional support"
    Then I should be on the "premium sign up" page

  @stripe_javascript @javascript
  Scenario: Premium user sees call to action which links to premium mob subscription page
    Given I am logged in as a premium user with name "John", email "john@gmail.com", with password "12345678"
    When I click "Upgrade to Premium Mob for group sessions with a Mentor"
    Then I should be on the "premium mob sign up" page
```

Ironically the tests took twice as long to write as the code, mainly due to a Capybara issue with the step that checks we've arrive on the correct page, which had to be evolved from this:

```rb
Then /^I should be on the "([^"]*)" page$/ do |page|
  expect(current_path).to eq path_to(page)
end
```

to

```rb
Then /^I should be on the "([^"]*)" page$/ do |page|
  expect(current_fullpath).to eq path_to(page)
end

def current_fullpath
  URI.parse(current_url).request_uri
end
```

after consulting various stackoverflow posts on the subject.  The issue here was that in the (older?) version of Capybara that we are using, the current_path returns the path without the URL params e.g. `plan=premiummob`, which we need due to the current design of the subscription controller.  Of course even just writing this down I come back to thoughts from a couple of months back about refactoring the URLs to nest so we could have `/plan/premiummob/subscriptions/new`.  Gosh, there's just endless extra refactoring we could be doing before being able to conduct experiments on the business logic itself :-)

So, anyway, that was all working and I pushed it out in the background of Friday, and as far as I can tell it's been running over the weekend.  I was in perhaps a hasty rush to put this up.  A last gasp of me being focused on promoting the Premium plans.  There's been no sudden flurry of Premium or Premium Mob sign ups over the weekend :-) It seems like the increase in basic signups is holding steady in a way I wouldn't expect unless the MOOC was active, although that might just be my imagination.  In the same deploy I got the adwords Premium plan conversion live, so if anyone else does convert we can in principle track where they are coming from, but it follows that we should really get the basic sign up adword conversion tracking up, and get better stats on the influx of new users, if we're interested in working out how much impact these site changes and AdWord campaigns are really having.

Then again my latest resurgence of Buddhist/Mindful thought is suggesting that strenuous effort in any direction is likely to be counter-productive.  That's supposed to be particularly true for enlightenment.  I love Steve Hagen's metaphor of an archery target.  With enlightenment apparently the closer you get to the center, the further away you actually are.  Somehow you are closest to dead center when you are totally on the periphery.  A pattern I do perceive is how my streneous attempts to resist things will bring them on more strongly or my attempt to grasp them will push them further from me.  It sort of feels like I am caught in a very strange counter-intuitive mental puzzle.

Desparately struggling to try and increase the number of Premium members in AgileVentures is probably going to make it harder to achieve that end goal.  I think it's time to be a lot more relaxed about all this.  I don't think that hurrying is going to get me anywhere pleasant.  It's probably just going to make the current moment hurried and unpleasant.  Everything just is.

Related Videos:

* ["Martin Fowler" scrum](https://www.youtube.com/watch?v=Gacp_DDpO5M)
* ["Kent Beck" scrum](https://www.youtube.com/watch?v=hzaQlua3_5s)
* ["Bob Martin" scrum](https://www.youtube.com/watch?v=si2W8keQG4o)
