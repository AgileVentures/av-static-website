So the APSoc client has approved the MSAccess alpha and the new timescale, so I can spend a week on the AV website and the rest of the ecosystem.  I made some progress with the Premium/Sponsor domain upgrade yesterday, and I suspect I have everything in place, but I'm getting what look suspiciously like a series of intermittent javascript acceptance test failures.  These really do seem to be the bane of our projects.  I've got a series of failures that disappear when I pop in the debugger.  For example:

```
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/WebsiteOne (1429_have_premium_upgrades_close_out_old_subscription)]$ 
→ be cucumber features/premium/upgrade_membership.feature:36

Using the default profile...
@stripe_javascript @javascript
Feature: Allow Users to Upgrade Membership
  As a site admin
  So that we can achieve financial stability
  I would like users to be able to upgrade their premium plans

  As a software developer
  So that I can receive additional support in my professional development journey
  I would like to be able to upgrade my support plan, and understand what that plan includes

  Background:                       # features/premium/upgrade_membership.feature:11
    Given the following plans exist # features/step_definitions/premium_steps.rb:58
      | name        | id         | amount | free_trial_length_days |
      | Premium     | premium    | 1000   | 7                      |
      | Premium Mob | premiummob | 2500   | 0                      |
    And the following users exist   # features/step_definitions/user_steps.rb:228
      | first_name | last_name | email                  | github_profile_url         | last_sign_in_ip |
      | Alice      | Jones     | alice@btinternet.co.uk | http://github.com/AliceSky | 127.0.0.1       |

  Scenario: User upgrades to premium from free tier                 # features/premium/upgrade_membership.feature:36
    Given I am logged in                                            # features/step_definitions/user_steps.rb:63
    When I am on my profile page                                    # features/step_definitions/user_steps.rb:389
    Then I should see a tooltip explanation of Premium              # features/step_definitions/premium_steps.rb:121
    And I click "Upgrade to Premium"                                # features/step_definitions/basic_steps.rb:90
    And I click "Subscribe" within the card_section                 # features/step_definitions/contained_search_steps.rb:33
    When I fill in appropriate card details for premium             # features/step_definitions/premium_steps.rb:6
    Then I should see "Premium Member"                              # features/step_definitions/basic_steps.rb:202
    And my profile page should reflect that I am a "Premium" member # features/step_definitions/premium_steps.rb:131
      expected to find text "Premium Member" in "Anders Persson ABOUT PROJECTS MEMBERS PREMIUM EVENTS GETTING STARTED Anders Persson 0 Premium Sweden Europe/Stockholm (UTC+01:00) Member for less than a minute Edit Set status Basic Member About Skills About me Omnis voluptatem voluptatem in et asperiores eveniet. Anders Persson has no publicly viewable Youtube videos. Learn More About Us Getting Started Dashboard Opportunities Blog Press Kit Social Facebook Twitter Our Sponsors Standup Bot Craft Academy Mooqita RubyMine Becoming a sponsor AgileVentures - Crowdsourced Learning Contact us Send a traditional email to info@agileventures.org. We are a Charitable Incorporated Organisation (CIO) in the UK. Ref #1170963" (RSpec::Expectations::ExpectationNotMetError)
      ./features/step_definitions/premium_steps.rb:134:in `/^my profile page should reflect that I am a "([^"]*)" member$/'
      features/premium/upgrade_membership.feature:44:in `And my profile page should reflect that I am a "Premium" member'
    And I should see button "Upgrade to Premium Mob"                # features/step_definitions/basic_steps.rb:232
    And I should see myself in the premium members list             # features/step_definitions/premium_steps.rb:75
WARN: Screenshot could not be saved. `page.current_path` is empty.

Failing Scenarios:
cucumber features/premium/upgrade_membership.feature:36 # Scenario: User upgrades to premium from free tier

1 scenario (1 failed)
12 steps (1 failed, 2 skipped, 9 passed)
0m18.349s
```

In principle this test could be affected by the changes I've been making to the domain model, but if I stick in a debug breakpoint at the point that the view is trying to check the status of the member, it comes up as Premium and the test passes.

```
    Then I should see "Premium Member"                              # features/step_definitions/basic_steps.rb:202

[68, 77] in /Users/tansaku/Documents/GitHub/AgileVentures/WebsiteOne/app/views/users/profile/_summary.html.erb
   68:                          class: 'user-profile-btn',
   69:                          type: 'button' %></p>
   70:         </li>
   71:     <% end %>
   72:       <% byebug  # the presence of this allows features/premium/upgrade_membership.feature:36 to pass for me locally %>
=> 73:       <li><%= presenter.user.membership_type %> Member</li>
   74:       <% if presenter.user.membership_type == 'Premium' %>
   75:         <li><%= render 'users/profile/premium_mob_upgrade', presenter: presenter  %></li>
   76:       <% elsif presenter.user.membership_type == 'Basic' %>
   77:         <li><%= render 'users/profile/premium_upgrade', presenter: presenter %></li>
(byebug) presenter.user.membership_type
"Premium"
(byebug) c
    And my profile page should reflect that I am a "Premium" member # features/step_definitions/premium_steps.rb:131
    And I should see button "Upgrade to Premium Mob"                # features/step_definitions/basic_steps.rb:232
    And I should see myself in the premium members list             # features/step_definitions/premium_steps.rb:75
```

and similarly if I slap in a byebug at the point that the user's plan is updated we get the same pass:

```
    When I fill in appropriate card details for premium             # features/step_definitions/premium_steps.rb:6

[3, 12] in /Users/tansaku/Documents/GitHub/AgileVentures/WebsiteOne/app/services/add_subscription_to_user_for_plan.rb
    3:   def self.with(user, sponsor, time, plan, payment_source, subscription_klass = Subscription)
    4:     ensure_all_previous_subscriptions_are_ended(user, time)
    5:     user.subscriptions << subscription_klass.new(started_at: time, sponsor: sponsor, plan: plan, payment_source: payment_source)
    6:     user.title_list << plan.name
    7:     byebug
=>  8:     user.save!
    9:   end
   10: 
   11:   def self.ensure_all_previous_subscriptions_are_ended(user, time)
   12:     unended_subs = user.subscriptions.select { |s| s.ended_at.nil? }
(byebug) c
    Then I should see "Premium Member"                              # features/step_definitions/basic_steps.rb:202
    And my profile page should reflect that I am a "Premium" member # features/step_definitions/premium_steps.rb:131
    And I should see button "Upgrade to Premium Mob"                # features/step_definitions/basic_steps.rb:232
    And I should see myself in the premium members list             # features/step_definitions/premium_steps.rb:75
```

So my hypothesis is that the request to check the profile page is going through so fast that the request for data about the user is coming back before the update has taken place.  The changes I've made mean the update does take longer.  If my hypothesis is true, the question is how to make sure that the cuke step to look up the profile page waits until the previous operation has executed.  It occurs to me that Cucumber steps that hit a new endpoint on the server can be treated almost like fire and forget operations. Makes me think of JavaScript non-blocking activity.  Maybe this isn't at all about JavaScript, sorry JavaScript.  Maybe the check that I have `"Then I should see "Premium Member"` is passing too quickly?  Strange though, I'm pretty sure that page won't have Premium Member in it until the transaction has gone through, but is the database in a separate thread?  I didn't think the page would update until the database action completed, but I guess that we go to the create method in the subscriptions controller via a redirect that's triggered from our javascript stripe form ...

So I change the step to be `Then I should see "Thanks, you're now an AgileVentures Premium Member"`, which really can't display until the create method has completed, but I get the same error.  Nice hypothesis, but not supported by the data ... hmm, so I create a new step that will check the member status has changed under the hood:

```
And(/^I am a "([^"]*)" Member$/) do |type|
  expect(@user.membership_type).to eq type
end
```
and that fails too:

```
    Then I should see "Thanks, you're now an AgileVentures Premium Member" # features/step_definitions/basic_steps.rb:202
    And I am a "Premium" Member                                            # features/step_definitions/premium_steps.rb:144

      expected: "Premium"
           got: "Basic"

      (compared using ==)
       (RSpec::Expectations::ExpectationNotMetError)
      ./features/step_definitions/premium_steps.rb:145:in `/^I am a "([^"]*)" Member$/'
      features/premium/upgrade_membership.feature:44:in `And I am a "Premium" Member'
```

Which makes me wonder if this something to do with our database transaction and cleaner.  I was skeptical but I found this [SO answer](https://stackoverflow.com/a/40185853/316729) which suggesting changing our cleaner from `after(:each)` to `append_after(:each)` and it appears to have worked ...?  This is the change:

```rb
  config.append_after(:each) do
    DatabaseCleaner.clean
  end
```

in `spec_helper.rb`, but after two passes, I cleaned up the extraneous steps and I get a fail again.  Rats.  Again, a feature that takes X amount of time to develop, takes 3X time to sort the flaky acceptance tests.  Anyhow, so now I print out the user subscriptions at the point the user has been updated and in the new step to check:

```
    When I fill in appropriate card details for premium                    # features/step_definitions/premium_steps.rb:6
#<Subscription id: 1, type: nil, started_at: "2018-01-16 10:07:10", ended_at: nil, user_id: 2, plan_id: 1, sponsor_id: 2>
    Then I should see "Thanks, you're now an AgileVentures Premium Member" # features/step_definitions/basic_steps.rb:202
    And I am a "Premium" Member                                            # features/step_definitions/premium_steps.rb:144
      ["#<Subscription id: 1, type: nil, started_at: \"2018-01-16 10:07:10\", ended_at: nil, user_id: 2, plan_id: 1, sponsor_id: 2>"]

      expected: "Premium"
           got: "Basic"

      (compared using ==)
       (RSpec::Expectations::ExpectationNotMetError)
      ./features/step_definitions/premium_steps.rb:146:in `/^I am a "([^"]*)" Member$/'
      features/premium/upgrade_membership.feature:44:in `And I am a "Premium" Member'
```

Weird thing here is that both the subscription states look correct, so why does the membership type not report correctly ... which leads me to:

```rb
  def current_subscription
    current_subscriptions = subscriptions.where(ended_at: nil).where('started_at < :now', {now: DateTime.now})
    return nil if current_subscriptions.nil? or current_subscriptions.empty?
    current_subscriptions.first
  end
```

ah, so maybe that `<` should be `<=`.  So that helps it pass, but the problem with an intermittent fail is that a single pass is not enough and I clean up and then I get another fail again ... okay, so I then manage to catch another fail with some more logging:

```rb
  def current_subscription
    puts "all user subs: #{subscriptions.map{|s| s.inspect}}"
    current_subscriptions = subscriptions.where(ended_at: nil).where('started_at <= :now', {now: DateTime.now})
    puts "current subs: #{current_subscriptions.map{|s| s.inspect}}"
    return nil if current_subscriptions.nil? or current_subscriptions.empty?
    current_subscriptions.first
  end
```

```
#<Subscription id: 1, type: nil, started_at: "2018-01-16 10:17:55", ended_at: nil, user_id: 2, plan_id: 1, sponsor_id: 2>
    Then I should see "Thanks, you're now an AgileVentures Premium Member" # features/step_definitions/basic_steps.rb:202
all user subs: ["#<Subscription id: 1, type: nil, started_at: \"2018-01-16 10:17:55\", ended_at: nil, user_id: 2, plan_id: 1, sponsor_id: 2>"]
current subs: []
    And I am a "Premium" Member                                            # features/step_definitions/premium_steps.rb:145
      ["#<Subscription id: 1, type: nil, started_at: \"2018-01-16 10:17:55\", ended_at: nil, user_id: 2, plan_id: 1, sponsor_id: 2>"]

      expected: "Premium"
           got: "Basic"

      (compared using ==)
       (RSpec::Expectations::ExpectationNotMetError)
      ./features/step_definitions/premium_steps.rb:147:in `/^I am a "([^"]*)" Member$/'
      features/premium/upgrade_membership.feature:44:in `And I am a "Premium" Member'
```

So, we can somehow have a situation where the current subs query does not report what we expect.  The datetimes appear to be specfic to seconds ... maybe I should grab all the subscriptions and filter on the ruby side?

```
  def current_subscription
    puts "all user subs: #{subscriptions.map{|s| s.inspect}}"
    now = DateTime.now
    puts now
    current_subscriptions = subscriptions.select{|s| s.ended_at.nil? and s.started_at <= now}
    puts "current subs: #{current_subscriptions.map{|s| s.inspect}}"
    return nil if current_subscriptions.nil? or current_subscriptions.empty?
    current_subscriptions.first
  end
```

but that also fails!  Weird - I even print out the time:

```
all user subs: ["#<Subscription id: 1, type: nil, started_at: \"2018-01-16 10:27:11\", ended_at: nil, user_id: 2, plan_id: 1, sponsor_id: 2>"]
2018-01-16T10:27:11+00:00
s.ended_at.nil?: true
s.started_at <= now: false
current subs: []
    And I am a "Premium" Member                                            # features/step_definitions/premium_steps.rb:145
      ["#<Subscription id: 1, type: nil, started_at: \"2018-01-16 10:27:11\", ended_at: nil, user_id: 2, plan_id: 1, sponsor_id: 2>"]

      expected: "Premium"
           got: "Basic"

      (compared using ==)
       (RSpec::Expectations::ExpectationNotMetError)
      ./features/step_definitions/premium_steps.rb:147:in `/^I am a "([^"]*)" Member$/'
      features/premium/upgrade_membership.feature:44:in `And I am a "Premium" Member'
```

It makes it look like when the time exactly matches the equality is not there.  Guess I should drop to a unit test:

```
    context 'just started a new plan' do
      before { subscription2.ended_at = now ; subscription2.save }
      let!(:subscription3) { FactoryGirl.create(:subscription, user: user, plan: premium_f2f, started_at: now, payment_source: payment_source) }

      it 'returns subscription that has started right now and has not ended' do
        expect(user.current_subscription.id).to eq subscription3.id
      end
    end
```

but that doesn't fail in the way I expect, but it looks like the following tip about converting the datetimes to integers seems to work from the print out I'm getting running the cuke test ...

```
    Then I should see "Thanks, you're now an AgileVentures Premium Member" # features/step_definitions/basic_steps.rb:202
1: s.ended_at.nil?: true
1: s.started_at <= now: false
1: s.started_at.to_i <= now.to_i: true
1: s.ended_at.nil?: true
1: s.started_at <= now: false
1: s.started_at.to_i <= now.to_i: true
    And my profile page should reflect that I am a "Premium" member        # features/step_definitions/premium_steps.rb:131
```

Notice how the `s.started_at <= now` is false while `s.started_at.to_i <= now.to_i` is true.  I got the idea from:

[https://makandracards.com/makandra/1057-why-two-ruby-time-objects-are-not-equal-although-they-appear-to-be](https://makandracards.com/makandra/1057-why-two-ruby-time-objects-are-not-equal-although-they-appear-to-be)

When I use `to_i` to compare the dates all the tests pass in batch and in the CI, making it seem like we've licked the intermittent fail issue, which seems to have been completely unrelated to JavaScript, but a function of the system looking for subscriptions that started in the past, and the cuke tests running fast enough that sometimes (and only sometimes) when the new subscription was created in the same second as the check for the current_subscription, the user would still be reported as not having upgraded to the new subscription.  The fix should have been just changing '<' to '<=' but the date comparison seems to require switching to integers to make it work as we expect.

What's frustrating is that the new unit test checks for the `<=` but somehow using factory girl (or something) does not replicate the exact way that the dates get set up when the whole thing runs in the acceptance test, so I have the system working now, but not a nice unit test precisely nailing the concealed bug ...

Time to wrap up for now - I'll leave notes in the code - I wonder if this is a known Rails issue ... anyway, apologies to JavaScript for thinking it was all your fault - not in this case :-)

