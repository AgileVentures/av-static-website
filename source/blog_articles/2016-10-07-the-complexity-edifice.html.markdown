---
title: The Complexity Edifice
date: 2016-10-07
tags: rspec cucumber stripe factory girl acceptance integration unit test bisect git
author: Sam Joseph
---

I assumed getting to safe green shores with this PremiumPlus upgrade button might be a little involved.  I still held out hope that we might be able to address some of the code smells we were encountering and/or generating.  Here were some of my concerns at the end of the last session:

1. Cukes using the same factories as specs
2. Model specs full of a mixture of unit and integration tests
3. FactoryGirl object creation having side-effects on how single table inheritance classes were reporting their class
4. VCR/Billy file mess from recording all the Stripe interactions
5. lack of confidence in code using `current_user`

Some [solo googling](http://stackoverflow.com/a/20101117/316729) had showed me that there should be some simple fixes for mixing FactoryGirl and STI; although I was still nervous about the fairylight connections between all these different factories that required special tweaking to behave like objects in production.  I knew we had a potential fix for the insecure [current_user](https://github.com/plataformatec/devise/issues/4317#issuecomment-251667866), and a possible alternate for [mocking Stripe](https://github.com/rebelidealist/stripe-ruby-mock), but all of these were arguably distractions while we were still trying to get the tests green.

We'd managed to avoid aggravating the Cuke usage of factories by adjusting the step that created a premium member like so:

```rb
Given /^I am logged in as( a premium)? user with (?:name "([^"]*)", )?email "([^"]*)", with password "([^"]*)"$/ do |premium, name, email, password|

  @current_user = @user = FactoryGirl.create(:user, first_name: name, email: email, password: password, password_confirmation: password)
  subscription = Premium.create(user: @user, started_at: Time.now)
  payment_source = PaymentSource::Stripe.create(identifier: 'cus_8l47KNxEp3qMB8', subscription: subscription )

  visit new_user_session_path
  within ('#main') do
    fill_in 'user_email', :with => email
    fill_in 'user_password', :with => password
    click_button 'Sign in'
  end
end
```

Now I still wasn't completely happy with this, but I was feeling a little suspicious of factories and so just creating the Subscription and PaymentSource objects directly felt a little safer.  Of course, the entire acceptance test was slightly compromised by reaching in to the database anyway.  A cleaner test might have gone through the entire sign up process for premium, before trying a premium plus upgrade.  The trade off here is running time, and the question is can we get the database into a state that corresponds to what would have been the case if a full premium signup had happened.

Factories in cukes had bitten us hard last week.  In the LocalSupport project we avoided every scenario having to repeat sign in by reaching in to the capybara session cookies.  Here in WebsiteOne we were stepping directly through the sign in operation, but creating the users with factories.  In LocalSupport we create the users with simple object creation.  I'd been burnt in the early days by Rails fixtures, to which factories were supposedly the solution, although apparently even [fixtures are being rehabilitated](http://chriskottom.com/blog/2014/11/fixing-fixtures/).  The right tool is all dependent on context of course :-)

I think the issue here is partly the general one of descriptions of encapsulated things.  We see it in the naming of methods, the naming of steps; it's all about the extent to which the details of what happen under the hood are inferable from the description.  So for example in the above code the step is something like `Given I am logged in as a premium user with name "John", email "john@john.com", with password "asdf1234"`  (and yes, that password should be defaulted), and this is reasonable, we would expect to have a logged in user who is signed up for premium.  Then inside the step we have things like `Premium.create(user: @user, started_at: Time.now)` which creates a premium subscription for a user starting now.  Maybe it would be better as `Subscription::Premium` but still, my fear is that `FactoryGirl.create(:user, first_name: name, email: email, password: password, password_confirmation: password)` is hiding a lot of complexity from me.  It happens to also create a G+ authentication and a Karma object.

I guess the solution there is better names for our factories, rather than throwing out factories themselves.  The creation of the Karma object should just be removed from the factory, but we could call this factory :user_authenticated_with_gplus to make things a little more transparent.  I'm still uncomfortable about features and specs sharing factories, but I'm also not entirely clear if we can separate them.  We're using the `factory_girl_rails` and so FactoryGirl appears to be available as a singleton throughout our specs and cucumber steps, hmmm.

So anyway, that's all preamble to the late starting pairing of the day.  The actual fail that we'd left things on the day before was this:

```
  Scenario: User upgrades to premium plus from premium                                                       # features/premium/upgrade_membership.feature:40
    Given I am logged in as a premium user with name "John", email "john@john.com", with password "asdf1234" # features/step_definitions/user_steps.rb:5
      uninitialized constant Premium (NameError)
      ./features/step_definitions/user_steps.rb:8:in `/^I am logged in as( a premium)? user with (?:name "([^"]*)", )?email "([^"]*)", with password "([^"]*)"$/'
      features/premium/upgrade_membership.feature:41:in `Given I am logged in as a premium user with name "John", email "john@john.com", with password "asdf1234"'
```

Turns out for Rails(?) to load the STI subscription classes they have to be in their own files.  I created a `premium.rb` file containing just:

```rb
class Premium < Subscription
end
```

and we were moving forward, although the module for payment source appeared to allow us to group together the STI there in a single file

```rb
module PaymentSource

  class PaymentSource < ActiveRecord::Base
    belongs_to :subscription
  end

  class CraftAcademy < PaymentSource
  end

  class Stripe < PaymentSource
  end
end
```

and with that it was a few short steps to get the premium plus upgrade passing.  Our charges controller needed to store the change in plan in our new domain objects:

```rb
  def upgrade
    current_user.subscription.type = 'PremiumPlus'
    current_user.save
    customer = Stripe::Customer.retrieve(current_user.subscription.payment_source.identifier)
    subscription = customer.subscriptions.retrieve(customer.subscriptions.first.id)
    subscription.plan = "premiumplus"
    subscription.save
  end
```

This code was still awful, but we had green.  What made the day complicated was that several of the regressions now failed.  That's what they are supposed to do of course, catch how your changes are breaking other places.  Some of them were fixed by updating how we originally create premium customers in the charges controller

```rb
  def update_user_to_premium(stripe_customer)
    return unless current_user
    current_user.subscription = Premium.new(started_at: Time.now)
    current_user.subscription.payment_source = PaymentSource::Stripe.new(identifier: stripe_customer.id)
    # current_user.stripe_customer = stripe_customer.id
    current_user.save
  end
```

but we were also getting an acceptance fail on upgrade from basic to premium, and it was the stripe iframe popup that was not showing up.  It was working in the normal sign up section, and we tried to drop back to see when it had been working.  Michael was driving at this point, and trying a git bisect.  It was starting to look like it had never worked, but upgrade from basic to premium had been deployed to production.  It had gone through CI.  It was working when we ran the full rails server locally.  Here's that popup for reference:

![stripe pop up](https://www.dropbox.com/s/h5zv1ge3rhrbgly/Screenshot%202016-10-07%2009.45.13.png?dl=1)

It's the thing that I speculate that most people don't test in their acceptance tests because sandboxing it effectively requires custom work in puffing billy that I've blogged about before.  Well, who knows, maybe everyone's doing it.  I've often made the mistake of thinking we're doing something clever or hard, only to find lots of others are doing it and finding it trivial :-)

Michael's faulty modem sound alert was going off, and my post-operative knee was in quite a lot of pain.  I suggested a "pair off".  We should separate, work on this individually and then compare notes at the scrum.  I'm bedridden this week, and I find the single laptop (I'm usually docked to three monitors) very constraining in a pairing session.  We separated and prodded at the system separately.  Michael was in bye bug confirming that the dialogue really didn't pop up in the test environment:

```
*** Capybara::Poltergeist::ObsoleteNode Exception: The element you are trying to interact with is either not part of the DOM, or is not currently visible on the page (perhaps display: none is set). It's possible the element has been replaced by another element and you meant to interact with the new element. If so you need to do a new 'find' in order to get a reference to the new element.
```

I was looking at the traces in the sandbox.  I had rolled back to our stable develop branch and it was clear to me that Stripe was rejecting the request from the headless browser to get the info to display the popup (which involves a request to the Stripe servers):

```
→ more features/support/fixtures/req_cache/allow_users_to_upgrade_membership/user_upgrades_to_premium_from_free_tier/get_checkout.stripe.com_0c264be81b93fe5a9dad2f95b498add8679d7c11.yml
---
:scope: 
:url: https://checkout.stripe.com/v3/MmIlwJCFOGIGxL58rFJw.html?distinct_id=2639e53d-dc62-49f3-ee65-08369e21cd0b
:body: ''
:status: 403
:method: get
``` 

Michael pointed to the [stripe logs](https://dashboard.stripe.com/test/logs) which showed that we weren't even hitting the test server.  We didn't fix it before the scrum, but afterwards I did some solo work that appeared to get to the bottom of it.  I could see from the VCR sandbox that we were generating a different distinct_id for Stripe than we had previously.  I was not sure what had changed, but we were getting VCR cache misses with URLs like `https://checkout.stripe.com/v3/MmIlwJCFOGIGxL58rFJw.html?distinct_id=956c69c0-fb75-bf58-cce8-6871b7d0cb73`.

I'm still not completely confident about the fix, but adjusting the VCR config to ignore the `distinct_id` parameter made everything suddenly start working:

```rb
VCR.configure do |c|
  c.hook_into :webmock
  c.cassette_library_dir = 'features/support/fixtures/cassettes'
  c.ignore_localhost = true
  c.default_cassette_options = {
      :match_requests_on => [
          :method,
          VCR.request_matchers.uri_without_param(:imp, :prev_imp, :distinct_id)
      ]
  }
end
```

The VCR cache no longer leaked, the tests passed and it was now a simple fix to sort out the other failing regression (for card update), by switching to the new domain objects.  I'm not 100% confident that if we dumped the entire cache that the freshly recorded caches would necessarily pass.  I'm nervous that a new developer checking out develop right now would get a fail.  However the feature branch is now passing everything locally and in CI.

It was satisfying to go green, but there are some things here that I don't think I can push off to refactoring tickets:

1) we need some sad paths for upgrade failure 
2) we've got to look carefully at Demeter violations in the way we use the Stripe API and our own domain objects
3) patching develop with the VCR config fix

In the scrum I was talking about Donald Norman's Design of Everyday Things, in which he laments how people blame themselves for not understanding poorly designed things.  Git, Stripe, Rails, Acceptance testing.  There's some real complexity there.  I'm not saying they are poorly designed, but the edifice of concepts that someone needs to understand to work with our acceptance tests ... maybe the problem is not the tools, but the way we are building on top of them?

Stripe says that they're okay with our tests hitting their test API at reasonable levels.  Other developers I respect have said that acceptance testing Stripe is too hard, and so just leave that out; testing everything around it.  Do I have a bad habit of pushing some things to the limit when others would sensibly give up and so get myself into trouble?  There's so much here.  Is it even vaguely comprehensible in blog form?  I think it will be at least another week before I'm comfortable releasing this feature, but in the meantime I'll keep blogging!


Related Videos 


* [Pairing Video](https://www.youtube.com/watch?v=nQEC5HjwTnI)
* ["Kent Beck" Scrum](https://www.youtube.com/watch?v=HCIaCdN6GW4)
