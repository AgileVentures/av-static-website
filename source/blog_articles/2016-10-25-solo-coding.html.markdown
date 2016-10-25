---
title: Solo Coding
date: 2016-10-25
tags: rspec stripe london chicago TDD services let allow except receive before migration demeter refactoring
author: Sam Joseph
---

Yak shaving aside I managed to make time for some solo coding.  The feature was allowing users to upgrade from premium to premium plus.  Michael and I had designed some extra domain features in a previous session, but realising we didn't need them yet had pulled them out into this PR, which would.  The new domain entities of Subscription and PaymentSource were necessary to distinguish between users being premium or premium plus without having to contact the remote Stripe API to confirm.  If someone wants to upgrade the last thing we want to do is have the site freeze because the Stripe API isn't responding.  That said we're still using the Stripe API to communicate the intention to upgrade the plan, but having a coherent way of storing the members' desire to upgrade in our database seems like a good move.

You might say why didn't you build this all in from the start?  Because we were following the Agile process of only building what we needed for the immediate future.  Now that we've validated that people will sign up for premium and premium plus plans, we're moving on to gamble that some of them might be willing to upgrade.  Actually the feedback I'm getting suggests that the premium plus plan is too expensive, so next up we'll be inserting some intermediate plans, but anyway, one feature at a time.  The [PR for this feature](https://github.com/AgileVentures/WebsiteOne/pull/1323) has been open a dangerously long time due to my knee operation and it needing fixing up.  It had taken a fair amount of pulling out my hair to get the feature tests all green.  Standing in the way of the release were the data migration that would take existing members stripe ids from the User model to the Subscription/PaymentSource model.  There was also a fair amount of gnarly code that was suffering from Demeter violations.

My internal dialogue was as follows:

> We could do the migrations on the production server manually, but it would be error-prone, and we got in trouble with the Karma migrations last time.  That said, this time we are not deleting the old db column.  Even so, what we really should have done last time was a dry-run of the migration, so if we have a script of it this time, it'll make it easier to dry-run it and/or rerun it.

I decided to get the migration code done, and also to write a cucumber test for it.  I'd be following up with a later migration to remove the db column and the migration test in a few weeks.  Exhilarating to be writing code that I plan to delete soon:

```gherkin
@rake
Feature: Migrate the stripe data
  As the admin
  So that users can get premium related functionality related to the new data schema
  I want to migrate to the new data structure

  Background:
    Given the following users exist
      | first_name | last_name | email                  | stripe_customer |
      | Alice      | Jones     | alice@btinternet.co.uk | 345rfyuh        |

  Scenario: Migrate the stripe data to the new architecture
    When I run the rake task for migrating stripe
    Then "alice@btinternet.co.uk" shoud have "345rfyuh" in their subscription
```

here are the relevant step definitions:

```rb
When(/^I run the rake task for migrating stripe$/) do
  $rake['db:migrate_stripe'].execute
end

Then(/^"([^"]*)" shoud have "([^"]*)" in their subscription$/) do |email, stripe_id|
  user = User.find_by_email(email)
  expect(user.stripe_customer_id).to eq stripe_id
end
```

and the rake hook:

```rb
Before('@rake') do |scenario|
  unless $rake
    require 'rake'
    Rake.application.rake_require 'tasks/scheduler'
    Rake.application.rake_require 'tasks/migrate_stripe_customer_ids'
    Rake::Task.define_task(:environment)
    $rake = Rake::Task
  end
end
```

and the rake task itself:

```rb
namespace :db do
  desc "Migrate stripe user ids from User model to Subscription model"

  task :migrate_stripe => :environment do
    User.all.each do |user|
      if user.stripe_customer
        user.subscription = Premium.new(started_at: Time.now) 
        # Time.now not ideal but can set manually later
        user.subscription.payment_source = PaymentSource::Stripe.new(identifier: user.stripe_customer)
        user.save
        Logger.new(STDOUT).info user.display_name.bold.blue + " stripe customer id migrated"
      end
    end
    Logger.new(STDOUT).info "migration has been completed".green
  end
end
```

I got this working, but was unhappy that I'd replicated gnarly Demeter violations from the charges controller into the migration task itself.  However I had the migration task green, and now I could refactor with more confidence.  I was tempted for a moment to pull this upgrade logic into the User class itself.  It sort of makes sense to say something like `user.upgrade_to_premium`, but our User model is already overblown with responsibilities, and really this is a process that involves manipulating and setting up several domain entities.  I went for a service, and I test drove it in the London style with the following code:


```rb
describe UpgradeUserToPremium do

  subject(:upgrade_user_to_premium) { described_class.with(user, time, stripe_id, payment_source_klass, subscription_klass) }

  let(:subscription_klass) { class_double(Premium) }
  let(:payment_source_klass) { class_double(PaymentSource::Stripe) }

  let(:stripe_id) { instance_double('StripeID') }
  let(:time) { instance_double(Time) }
  let(:user) { instance_double(User) }
  let(:subscription) { instance_double(Premium) }
  let(:payment_source) { instance_double(PaymentSource::Stripe) }

  before do
    allow(payment_source_klass).to receive(:new)
    allow(subscription_klass).to receive(:new)
    allow(user).to receive(:subscription=)
    allow(user).to receive(:save)
  end

  it 'creates a payment source' do
    expect(payment_source_klass).to receive(:new).with({identifier: stripe_id})
    upgrade_user_to_premium
  end

  it 'creates a subscription of the appropriate type' do
    allow(payment_source_klass).to receive(:new).and_return(payment_source)
    expect(subscription_klass).to receive(:new).with(started_at: time, payment_source: payment_source)
    upgrade_user_to_premium
  end

  it 'sets the user subscription' do
    expect(subscription_klass).to receive(:new).and_return(subscription)
    expect(user).to receive(:subscription=).with(subscription)
    upgrade_user_to_premium
  end

  it 'saves the user' do
    expect(user).to receive(:save)
    upgrade_user_to_premium
  end
end

```

to be honest I actually wrote out a sketch of the service in a text editor before driving the above in RubyMine, but here's how the TDD'd version ended up:

```rb
class UpgradeUserToPremium
  def self.with(user, time, stripe_id, payment_source_klass = PaymentSource::Stripe, subscription_klass = Premium)
    payment_source = payment_source_klass.new(identifier: stripe_id)
    user.subscription = subscription_klass.new(started_at: time, payment_source: payment_source)
    user.save
  end
end
```

I know that some of my colleagues prefer the Chicago style, and some people might thing that the above involves a ridiculous amount of test code for a three line class method.  The RSpec suffers from not being as comprehensible as it might be by people who aren't comfortable with RSpec concepts like `let`, `allow`, `receive` and so forth.  What I can say in its defence (and I am still on the fence) is that there is a coherent shape in my mind here that has some level of intrinsic beauty.  The RSpec unit test of the service is following the heuristic that each it statement should test only one thing.   The `let` statements make the set of collaborating entities completely explicit.  The `before` block sets up to stub all the outgoing collaborators of the method, so that each of the four `it` statements does not actually reach any other part of the system, making this a unit and not an integration test.  Then each `it` block tests each of the four key things that happen in the method.  

Here we could argue that the method is doing too much - it does four things and should have a single responsibility (according to the SOLID principles).  I could break out some smaller methods, but I think this is actually the right level of granularity.  We have a concept in the domain that is upgrading a user to premium, and these are the four things that will need to happen to set that up and persist it.  The Demeter violations of the earlier code are gone, and the tests are still green, showing that the migration is working.  Furthermore, we can use the same service in the charges controller which goes from this:

```rb
  def update_user_to_premium(stripe_customer)
  return unless current_user
  current_user.subscription = Premium.new(started_at: Time.now)
  current_user.subscription.payment_source = PaymentSource::Stripe.new(identifier: stripe_customer.id)
  current_user.save
end
```

to this:

```rb
def update_user_to_premium(stripe_customer)
  return unless current_user
  UpgradeUserToPremium.with(current_user, Time.now, stripe_customer.id)
end
``` 

and our migration cleans up like so:

```rb
namespace :db do
  desc "Migrate stripe user ids from User model to Subscription model"

  task :migrate_stripe => :environment do
    User.all.each do |user|
      if user.stripe_customer
        # setting time as now not ideal but can set manually later
        UpgradeUserToPremium.with(user, Time.now, user.stripe_customer)
        Logger.new(STDOUT).info user.display_name.bold.blue + " stripe customer id migrated"
      end
    end
    Logger.new(STDOUT).info "migration has been completed".green
  end
end
```

So far so good.  We're left with some Demeter violations in the upgrade to premium plus code in the charges controller:

```rb
def upgrade
  current_user.subscription.type = 'PremiumPlus'
  current_user.save
  customer = Stripe::Customer.retrieve(current_user.stripe_customer_id)
  subscription = customer.subscriptions.retrieve(customer.subscriptions.first.id)
  subscription.plan = "premiumplus"
  subscription.save
end
```

I don't like this, and I don't like how we've ended up overloading the charges controller.  The former is the Stripe API that perhaps we're using incorrectly, but I don't know that I can justify putting an adapter on this right now.  The charges controller needs a bigger refactoring, but I'm making the judgement call that these two things go into refactoring tickets.  In the ideal world I'd also love more sad path tests in places, but again, this PR has been open long, we need the data migration in and from a charity/business perspective we're likely to bring in more revenue by actually releasing the individual sign up pages for the new intermediate plans, and they'll have to be a fair amount of work going on on top of this code to handle the new sequence of upgrades that are possible, so like Sandi Metz suggests, I'm hedging my bets about avoiding too much refactoring in an area where the code is in flux.

Of course now Google Hangouts on Air have switched to YouTube live and our automated distribution of hangouts is failing - we've got a manual override but it has a bug I blogged about last week which will drag me away from pushing out these other plans ... gah!  It's all about the prioritization ... At least I quite enjoyed writing all the above code :-)

Related Videos:

* ["Kent Beck" scrum](https://www.youtube.com/watch?v=Qt1QmFyBdpY)






