---
title: Dodging a Bullet
date: 2016-09-27
tags: pairing tickets refactoring domain model cucumber steps scenarios heuristics stripe git rspec
author: Sam Joseph
---


So Monday was the day to see if our new domain model components would ease the process of delivering a new feature. As warm up I made a quick refactoring requested by Raoul on our Karma calculation changes.  There was the possibility of re-using some cucumber steps.  We'd submitted the following:

```rb
When(/^I run rake task "([^"]*)"$/) do |task|
  $rake[task].invoke    
end

When(/^I run the rake task for calculating karma points$/) do
  $rake["karma_calculator"].execute
end
```

and Raoul was suggesting re-using the the first step.  Looking it over I actually modified it to the following:


```rb
When(/^I run the rake task for calculating karma points$/) do
  $rake["karma_calculator"].execute
end
        
When(/^I run the rake task for fetching github commits$/) do
  $rake["fetch_github_commits"].execute
end
```

Following the idea that the high level cucumber scenarios should be as readable English as possible, preferring `When I run the rake task for calculating karma points` to `When I run the rake task for "karma_calculator"`.  Trivial?  Maybe.  I'm conflicted.  These are related to cucumber stories we have in our dev-ops section which I'm hoping provides some testable documentation of the operational aspect of the application that's used by app admins rather than end users.  I'm also going with not re-using step-definitions within step-definitions.  Two more heuristics there to add to the list.

I mention this warm up partly because I feel like there's clean up for the Cucumber stories we wrote on Monday.  As it happens, Raoul approved that above change and merged the new Karma calculation PR in, making it possible that we could get started on refactoring work there.  But anyhow, here, ultimately, are the Cucumber scenarios that defined our Monday's work:

```gherkin
  Scenario: User is on free tier and looking at own page
    Given I am logged in
    And I am on my profile page
    Then I should see "Basic Member"
    And I should not see "Premium Member"

  Scenario: User is on free tier and looking at other persons profile page
    Given I am logged in
    And I visit Alice's profile page
    Then I should not see "Basic Member"
    And I should not see "Premium Member"
    And I should not see "Upgrade to Premium"

  Scenario: User upgrades to premium from free tier
    Given I am logged in
    And I am on my profile page
    And I click "Upgrade to Premium"
    When I fill in appropriate card details for premium
    Then I should see "Premium Member"
    Given I am on my profile page
    Then I should not see "Basic Member"
    And I should not see "Upgrade to Premium"
```

We didn't get to these all at once.  We'd put the fruits of our InsideOut work aside (the new domain elements Subscription and PaymentSource) and reverted to OutsideIn.  This was the set of experiences we wanted for the end user, in association with the [ticket](https://github.com/AgileVentures/WebsiteOne/issues/1261) we were working on.  We'd actually started with two scenarios, one about upgrade to premium and the other about upgrade to premium plus, and in my mind had been the idea that we would ultimately make both types of upgrade work using our new domain entities.

However that was actually scope creep off the ticket, and actually there was a chunk of work specific to the ticket (upgrade from basic to premium) that could be implemented in the existing system without any reference to the new domain entities, so we got that done.  It was mainly a change to the view to add a report about the user's current membership status, and an upgrade button:

```html
    <% if current_user && current_user == presenter.user %>
        <li><%= current_user.membership_type %> Member</li>
        <% unless current_user.membership_type == 'Premium' %>
            <li><%= form_tag charges_path(plan: 'premium') do %>
                  <script src="https://checkout.stripe.com/checkout.js" class="stripe-button"
                          data-key="<%= Rails.configuration.stripe[:publishable_key] %>"
                          data-description="A month's subscription"
                          data-amount="1000"
                          data-currency="GBP"
                          data-locale="en-US"
                          data-name="Premium Membership"
                          data-label="Upgrade to Premium"></script>

              <% end %>
            </li>
        <% end %>
    <% end %>  
```

I'm itching to pull this into a partial ([refactoring ticket](https://github.com/AgileVentures/WebsiteOne/issues/1306)), and I also worry we're not being confident enough with our logic - of which there is also too much in the view.  Devise's `current_user` method is maybe the source of our timidity, since if no one is logged in, it returns nil.  Perhaps it would be better if it returned an AnonymousUser object.  Michael started looking into how we might override it, but I demurred, writing an email to Stripe about our new approach of locking the data-locale to "en-US".  All that was actually after we'd dropped to the RSpec level to create the necessary supporting operations for the view fragment above.  Let's just take a look at them:

```rb
describe User do
  describe '#membership_type' do
    subject(:user) { FactoryGirl.create(:user) }
    it 'returns membership type' do
      expect(user.membership_type).to eq 'Basic'
    end
    context 'premium member' do
      subject(:user){FactoryGirl.create(:user, stripe_customer: "sdfdfds")}
      it 'returns premium' do
        expect(user.membership_type).to eq 'Premium'
      end
    end
  end
do
```

```rb
class User < ActiveRecord::Base
  ...
  def membership_type
    return "Basic" unless stripe_customer
    "Premium"
  end
  ...
end
```

and finally a little addition to the charges controller, so that when users sign up for premium, that we store their customer id in the user table:

```rb

  def create
    @plan = params[:plan]

    customer = Stripe::Customer.create(
        email: params[:stripeEmail],
        source: params[:stripeToken],
        plan: @plan
    )

    update_user_to_premium(customer)

    send_acknowledgement_email

  rescue Stripe::CardError => e
    flash[:error] = e.message
    redirect_to new_charge_path
  end

  private

  def update_user_to_premium(stripe_customer)
    return unless current_user
    current_user.stripe_customer = stripe_customer.id
    current_user.save
  end
```

The upshot of which was that using only the existing aspects of the system we would now store the stripe customer id of the premium member in the user table, and the premium users would see their status on their profile page, and non-premium users would see the upgrade button.  It was all working without the aid of the new domain entities.  As I mentioned above, Michael and I had other concerns, but with my eye on the clock I suggested we get this in as a pull request, and then use the new domain entities to support work on a subsequent [ticket of allowing premium users to upgrade to premium plus](https://github.com/AgileVentures/WebsiteOne/issues/1303).

I had other concerns dripping out of me, such as whether we should be testing Stripe differently, whether we should be making our scenarios more declarative.  I guess part of the challenge with our increasing list of heuristics for good coding and good project management is that it can feel like you are being pulled in multiple directions at once, and you can be forgiven for a kind of paranoia about which really is the most important issue to work on first.

Submitting the PR for just the above code involved pulling out the commits for the new domain model entities.  One thing that was easy to prioritise for me was to put only the smallest set of necessary elements in the pull request.  I didn't want Raoul's code review to get distracted by having a set of un-used domain entities in the incoming code.  Michael found a [cool technique for cherry picking a set of commits](http://stackoverflow.com/a/1994491/316729) and we got in a PR with just the code above, and moved the domain entities onto a new branch associated with the ticket for creating the premium plus upgrade button.  Will we fall foul of premature refactoring?  Did we just dodge a bullet?

It's pretty clear to me that the current stripe_customer field on user can't support information about different membership plans.  Hopefully our InsideOut work will bear fruit in the next session!


Related Videos:


* [Pairing on the above](https://www.youtube.com/watch?v=UprAXzePQmo)
* ["Kent Beck" scrum](https://www.youtube.com/watch?v=JPvkCffsHOo)
