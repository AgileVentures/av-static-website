---
title: Non Invasive Programming
date: 2016-10-05
tags: rspec cucumber stripe factory girl acceptance integration unit test
author: Sam Joseph
---



So I was back in shape for a bit of pair programming again and Michael and I continued what we'd been working on last week, which was allowing [Premium members to upgrade to PremiumPlus](https://github.com/AgileVentures/WebsiteOne/issues/1303).  It took us 15 minutes or so to get up to speed, closing out a few experimental PRs, checking that the airbrake errors weren't anything serious etc.  I had been assuming we'd have to focus on other things, so I was pleased when we could continue with a task related to premium.  I was speculating that the small spike in new sign ups we had might have been related to rolling out the "Upgrade to Premium" button on all the individual user profile pages.

I was half tempted to start ferreting through our Google Analytics data and checking with the new premium members about this, but put a pin in that and tried to make progress on the outstanding task.  DriveBy heuristic, finish what you started, and find the shortest path to completion.  Hmm, maybe it should be called "WhistleStop", or "non-invasive", anyhow ... it was an interesting operation because what we were hoping was to pull in the new domain objects we'd created the week before (Subscription and PaymentSource).  We had a failing acceptance test that was expecting a premium user's profile page to now show an "upgrade to Premium Plus" button: 

```gherkin
  Scenario: User upgrades to premium plus from premium
    Given I am logged in as a premium user with name "John", email "john@john.com", with password "asdf1234"
    And I am on my profile page
    And I click "Upgrade to Premium Plus"  # failing step
    Then I should see "Premium PLUS Member"
    Given I am on my profile page
    Then I should see "Premium Plus Member"
    Then I should not see "Basic Member"
    And I should not see "Premium Member"
    And I should not see "Upgrade to Premium"
    And I should not see "Upgrade to Premium Plus"
```

I was still not completely happy with the level of abstraction here, but I jumped into the view to start tinkering with the existing logic:

```html
<% unless current_user.membership_type == 'Premium' %>
  <li><%= render 'users/profile/premium_upgrade' %></li>
<% end
```

Not that we wanted logic in our views.  In the previous session we had already extracted the premium upgrade button into a partial `_premium_upgrade.html.erb`:

```html
<%= form_tag charges_path(plan: 'premium') do %>
    <script src="https://checkout.stripe.com/checkout.js" class="stripe-button"
            data-key="<%= Rails.configuration.stripe[:publishable_key] %>"
            data-description="A month's subscription"
            data-amount="1000"
            data-currency="GBP"
            data-locale="en-US"
            data-name="Premium Membership"
            data-label="Upgrade to Premium"></script>

<% end %>
```

I had specifically avoided DRYing that out with the similar button with the view we had in the main Premium payment page as I wanted us to stay tightly focused on one thing and I knew that our business logic was in flux.  Looking at the logic we had, both Michael and I were thinking about making something like:


```html
<% if current_user.can_upgrade? %>
  <li><%= render "users/profile/#{current_user.next_upgrade}" %></li>
<% end
```

and how we wanted to be extracting the business logic e.g. the current upgrade path of "Basic" to "Premium" to "PremiumPlus".  Michael was pushing me to stop talking about it and just get it done, as we all know I have a tendency to run off at the mouth.  I did the super simple step of creating an additional premium upgrade partial and tweaked the logic, thinking we'd still get a failure:

```html
<% if current_user.membership_type == 'Premium' %>
  <li><%= render 'users/profile/premium_plus_upgrade' %></li>
<% elsif current_user.membership_type == 'Basic' %>
  <li><%= render 'users/profile/premium_upgrade' %></li>
<% end %>
```

with a new partial `_premium_plus_upgrade.html.erb` that was a copy of the one above, but with script elements tweaked for Premium Plus.  I was reflecting about how that business logic would need to be DRYed out, but not doing that now; putting that one aside to see how this failed.  I'm not sure quite what I was thinking, but surprisingly the step actually passed, or at least got us on a few more cucumber steps.  We laughed about the importance of proceeding in simple steps.  If we'd got sidetracked extracting business abstractions we'd have had some missing components in our understanding, or at least in my understanding.  Not that we don't want to extract the business logic eventually, but we don't want to do it prematurely.  The funny thing then was that we realised that actually we didn't want another credit card payment button for the Premium user who was upgrading to Premium Plus.  We already had their credit card details. We could craft our own button and endpoint to do the subscription upgrade as described by Stripe in an example in their docs:

```rb
subscription = Stripe::Subscription.retrieve("sub_3R3PlB2YlJe84a")
subscription.plan = "premium_monthly"
subscription.save
```

So suddenly we were back-peddling.  I could feel the "I want to keep coding" urge and forced myself to pong the code over to Michael and he wrote a new `_premium_plus_upgrade.html.erb` partial that looked like this:

```html
<%= form_tag charges_upgrade_path, method: "put" do %>
    <%= submit_tag "Upgrade to Premium Plus", class:"stripe-button"%>
<% end %>
```

We'd decided to create a new action on the charges controller.  What was really interesting here, reflecting on our piecemeal work on the charges controller, we realised that we'd got our RESTfulness totally out of whack.  We'd originally put our subscription sign-up in a "charges" controller from one Stripe example that was all about making single one off charges.  What we had would be more accurately called a "Subscription" controller.  Let's look at all the code that's accumulated there:

```rb
class ChargesController < ApplicationController

  before_filter :authenticate_user!, only: [:edit, :update]

  def new
    render 'premiumplus' and return if premiumplus?
    render 'premium'
  end

  def edit
  end

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

  def update
    customer = Stripe::Customer.retrieve(current_user.stripe_customer) # _token?
    card = customer.cards.create(card: params[:stripeToken])
    card.save
    customer.default_card = card.id
    customer.save
  rescue Stripe::InvalidRequestError => e
    logger.error "Stripe error while updating card info: #{e.message} for #{current_user}"
    @error = true
  end
```

So it looks like a CRUD controller, and you might expect it to be CRUDing charges, but actually it's creating customers, subscriptions and updating customer credit card details; urgh, embarrassing!  It's not immediately clear if what we need are separate controllers for Subscriptions, Customers and Cards; or a StripeController that takes care of all of them.  We'll need to reflect carefully on this.  At the risk of adding insult to injury we went for just adding a non-RESTful action called `upgrade`.  Trying to be "non-invasive" we just wanted to get this one feature completed adding a new route to `routes.rb`:

```rb
match '/charges/upgrade' => 'charges#upgrade', :via => [:put]
```
 
and a new action:

```rb
  def upgrade
    customer = Stripe::Customer.retrieve(current_user.stripe_customer)
    subscription = customer.subscriptions.retrieve(customer.subscriptions.first.id)
    subscription.plan = "premiumplus"
    subscription.save
  end
```

It took us a while to get to that logic in the upgrade action. It was failing and we had to drop into the debugger where the error message from the Stripe API told us to do something different from their docs; weird.  And total Demeter violation territory. `customer.subscriptions.retrieve(customer.subscriptions.first.id)` in particular was looking very brittle.  Surely there would be something like customer.subscriptions.first that we could use instead?  We'd circle back - this was working and I had an eye on whether we couldn't get this feature green without using any of our new domain objects.  Was that crazy?  I was thinking that it would give us a safe space to reflect, and improve our understanding of the Stripe API.  With the upgrade code in place the only thing we needed was for the user object to be able to correctly report its membership type.  It currently did this:


```rb
  def membership_type
    return "Basic" unless stripe_customer
    "Premium Plus"
  end
```

We updated it like so:

```rb
  def membership_type
    return "Basic" unless stripe_customer
    plan = Stripe::Customer.retrieve(stripe_customer).subscriptions.first.plan.id
    return "Premium" if plan == "premium"
    "Premium Plus"
  end
```

which should have worked, but even using the debugger to check it step by step, it wouldn't.  We were getting things like this:

```
> plan
"premium"
> plan == "premium"
false
``` 

Something very strange was going on.  We were on shaky ground here, pushing at model logic with an acceptance test, for something that we were probably going to throw out anyway.  We threw out that code and I pulled in the new domain objects and got the `membership_type` method under an integration test:

```rb
    describe '#membership_type' do
      subject(:user) { FactoryGirl.create(:user) }

      it 'returns membership type' do
        expect(user.membership_type).to eq 'Basic'
      end

      context 'premium member' do
        subject(:user) { FactoryGirl.create(:user) }
        let!(:premium) { FactoryGirl.create(:subscription, user: user) }

        it 'returns premium' do
          expect(user.membership_type).to eq 'Premium'
        end
      end
    end
```

I had to play with a lot of factories, and the above spec was passing with the following:

```rb
  has_one :subscription

  def membership_type
    return "Basic" unless subscription
    subscription.type
  end
```

but we were running out of time, and we had to finish pairing without getting the acceptance tests green, because the cuke steps were going to need a whole load of refactoring to be creating premium users in terms of setting up these new domain objects like subscription and payment source.  I had several places of discomfort that I hope we can address in future sessions:

1. Cukes using the same factories as specs
2. Model specs full of a mixture of unit and integration tests
3. FactoryGirl object creation having side-effects on how single table inheritance classes were reporting their class
4. VCR/Billy file mess from recording all the Stripe interactions
5. lack of confidence in code using `current_user`

But which are the ones that we can address "non-invasively" to stay focused on getting this single feature green?  So much going on here.  Just how many refactoring tickets will we generate? All to be revealed in the next pair programming session!



Related Videos:

* [Pair Programming](https://www.youtube.com/watch?v=bKu3e_jLrQw)
* ["Kent Beck" scrum](https://www.youtube.com/watch?v=8EkSfYVt9D0)
