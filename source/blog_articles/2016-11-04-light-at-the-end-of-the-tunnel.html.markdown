---
title: Light at the End of the Stripe Tunnel
date: 2016-11-04
tags: Stripe VCR PuffingBilly cache http network payment test cucumber batch CraftAcademy StripeRubyMock
author: Sam Joseph
---

My hopes for a less than frustrating day were dashed as I worked through the ongoing Stripe test failures.  Michael wasn't around and I solo'd through some different alternatives.  Rather than just burn time repeatedly in the long debug cycle of seeing if the Stripe acceptance tests would all run in batch after random tweaks, I identified some different possible options:

* Run the existing tests without sandboxing (create a @poltergeist_no_billy tag)
* Slowly re-add code pieces to develop (since batch cukes are green there)
* Delete the sandbox caches for larger and larger sets of acceptance tests, seeing if regenerating them fixed things
* Use the StripeRubyMock gem as an alternative

I started with the first option reckoning that I could at least quickly test whether the existing tests would run without sandboxing.  To re-cap, the develop branch had about 10 Stripe acceptance tests that ran fine with sandboxes; working individually and in batch.  Our new feature for users sponsoring each other introduced 2 new stripe acceptance tests, neither of which did anything fundamentally different from the existing tests.  These 2 new tests ran fine individually, in a group with all the other Stripe acceptance tests, but then the entire set of Stripe tests (new and old) would fail (no Stripe iFrame appearing) when run in batch with all the other Cucumber tests.

It felt to me like there must be some timing issue here.  Some race condition such as often plagues JavaScript acceptance tests.  Although this wasn't intermittent failure - the whole thing still doesn't make sense to me.  To try and make sense I wanted to get some other data points.  Getting the Stripe tests to run without sandboxing might allow me to exclude sandboxing as the source of the problem.  Not that sandboxing didn't have other usability issues that I've blogged about recently, but when trying to solve any big problem it's useful to run experiments on the subsets of the components to see try and narrow down the area in which the error is occurring.

Removing sandboxing was not as easily said as done, as I'd previously hooked the PuffingBilly gem directly into all Cucumber Javascript tests like so:

```rb
test_options = {
    phantomjs_options: [
        '--ignore-ssl-errors=yes',
        "--proxy=#{Billy.proxy.host}:#{Billy.proxy.port}"
    ],
    phantomjs: Phantomjs.path,
    js_errors: true,
}

Capybara.register_driver :poltergeist_billy do |app|
  Capybara::Poltergeist::Driver.new(app, test_options)
end
```

Both WebSiteOne and LocalSupport have been plagued, and continue to suffer to some extent, from intermittent JavaScript acceptance test failures.  There's a [good blog post](https://bibwild.wordpress.com/2016/02/18/struggling-towards-reliable-capybara-javascript-testing/) on why this is a tricky problem.  My own take earlier in the year was that it made sense to nail down all sources of variation.  Tests that make network connections are depending on the network and 3rd party services.  Sandboxing those network connections (recording them and playing them back in test mode) using the VCR and PuffingBilly gems reduces the variability of the test, and makes it more deterministic.  Earlier this year, as part of trying to reduce the overall variability in the WebSiteOne acceptance test, Michael and I had completely re-implemented the Cucumber config and hooks for WebSiteOne.

To my chagrin this had still not completely removed the occasional intermittent acceptance failure, and the generation of additional cache files on test failure or on other occasions of test variation was confusing for all developers, complicated to manage, and made some PRs unreadable due to the overload of files.  In several smaller projects I'd preferred sandboxing over stubbing with WebMock, which has it's own issues of maintaining lots of individual hand-written network calls.  Anyhow, in order to remove the sandboxing I had to get another Capybara JavaScript driver set up.  I ran through a series of alternatives:

* Default (Selenium) - opened firefox (v47) browser displayed nothing
* Poltergeist - not working on my OSX - absolutely bizarre DB errors, with users appearing and disappearing
* Poltergeist with PhantomJS - Stripe tests passed, but redirects to success page did not work - underlying permissions fail?
* Poltergeist with PhantomJS and PuffingBilly - going back to this got green, but still failing in batch

That was a frustrating hour.  I knew that with extra work I could probably get the Selenium testing working, but that headed browser approach wasn't going to work on our CI.  The pure Poltergeist errors with data appearing and disappearing from the DB were bizarre and a big red flag.  Poltergeist with PhantomJS looked promising.  We were avoiding sandboxing, but there was likely some permissions error at some level.  I could probably find that, although I was also encountering some ambiguity about which driver was being used - how other hooks were integrating with the existing Capybara Javascript hook:

```rb
Before('@poltergeist_no_billy') do
  Capybara.javascript_driver = : poltergeist_no_billy
  Capybara.current_driver = Capybara.javascript_driver # this is my copy of the existing @javascript hook
end
```

I wasn't liking any of the options very much.  I thought I'd give stripe-ruby-mock a spin.  Working from the [gem README](https://github.com/rebelidealist/stripe-ruby-mock/blob/master/README.md), [Thomas' CraftAcademy blog](https://medium.com/craft-academy/keeping-it-simple-3e7d9b186015#.4rc29xv6j), and probably most importantly the associated [CraftAcademy repo](https://github.com/CraftAcademy/sf-online-august/) I got first a single Stripe acceptance test working, and then another.  All would be for nothing if I couldn't get them running in batch.  In batch a large number of Stripe acceptance tests were still failing, but importantly the ones using StripeRubyMock were not.  I had a lifeline, a light at the end of the tunnel.  If I could convert all the Stripe acceptance tests to StripeRubyMock I might have something where the entire test suite would go green.

I had created a custom `@stripe_javascript` tag to ensure that our StripeRubyMock tests would not use PuffingBilly, while allowing the other existing tests to keep using it:

```rb
Before '@stripe_javascript' do
  Capybara.javascript_driver = :poltergeist
  Capybara.current_driver = Capybara.javascript_driver
  StripeMock.start
  @stripe_test_helper = StripeMock.create_test_helper
end

After '@stripe_javascript' do
  Capybara.javascript_driver = :poltergeist_billy
  StripeMock.stop
  Capybara.send('session_pool').each do |_, session|
    next unless session.driver.is_a?(Capybara::Poltergeist::Driver)
    session.driver.restart
  end
end
```

I was not without concern for some of the other changes.  App code in the ChargesContller had to be adjusted to accommodate testing with StripeRubyMock:

```rb
def stripe_token(params)
  Rails.env.test? ? generate_test_token : params[:stripeToken]
end

def generate_test_token
  StripeMock.create_test_helper.generate_card_token
end
```

The charges controller would now ignore the incoming stripe token from the javascript redirect, and use a StripeRubyMock generated token in test mode.  It was pretty innocuous but a different code path would run in test, compared to development and production.  This was going against another general coding guideline.  Not the end of the world, but a warning flag.  Still this was starting to seem like the most hopeful route back to a green Cucumber suite.  I also came away with a modified poltergeist driver based on the CraftAcademy code:

```rb
Capybara.register_driver :poltergeist do |app|
  Capybara::Poltergeist::Driver.new(app, js_errors: false,
                                    phantomjs: Phantomjs.path,
                                    phantomjs_options: ['--ssl-protocol=tlsv1.2', '--ignore-ssl-errors=yes'])
end
```
Another key difference was that I now had to explicitly create our Premium plans using the StripeRubyMock as part of the setup for the cuke scenarios:

```gherkin
  Background:
    Given the following plans exist
      | name        | id          |
      | Premium     | premium     |
      | PremiumMob  | premiummob  |
      | PremiumF2F  | premiumf2f  |
      | PremiumPlus | premiumplus |
```

```rb
Given(/^the following plans exist$/) do |table|
  table.hashes.each do |hash|
    @stripe_test_helper.create_plan(hash)
  end
end
```

This is interesting as previously the cache of VCR and PuffingBilly would contain the plan information encoded in HTTP responses from the Stripe servers.  That had ensured the Stripe servers were the single authoritative source for this information.  As it happens specifying this in the Cucumber steps makes our business logic description here more complete.  It leaves us with a data dependency, whereby we need to ensure that the plans specified in our Cucumber scenarios match those on the Stripe servers.  Another heuristic red flag, but the prospect of a green test suite was driving me forward.  I wasn't quite there yet, our update card details was failing, and we needed more changes to how we created a Premium user for testing purposes:

```rb
Given /^I am logged in as( a premium)? user with (?:name "([^"]*)", )?email "([^"]*)", with password "([^"]*)"$/ do |premium, name, email, password|

  @current_user = @user = FactoryGirl.create(:user, first_name: name, email: email, password: password, password_confirmation: password)
  if premium
    subscription = Premium.create(user: @user, started_at: Time.now)
    customer = Stripe::Customer.create({
                                           email: email,
                                           source: @stripe_test_helper.generate_card_token
                                       })
    customer.subscriptions.create(plan: 'premium')
    payment_source = PaymentSource::Stripe.create(identifier: customer.id, subscription: subscription )
  end
```

A card token from the Stripe test server would no longer work and the fix was using the stripe test helper to generate a card token.  This test code was super ugly, and I was still really bothered by the factories being shared between cukes and specs.  Anyhow, I also had to modify the app code for updating cards, because we were now generating a different kind of error.  I added a `NoMethodError` to the update card error handling:

```
  def update
    customer = Stripe::Customer.retrieve(current_user.stripe_customer_id) # _token?
    card = customer.sources.create(card: stripe_token(params))
    card.save
    customer.default_card = card.id
    customer.save
  rescue Stripe::InvalidRequestError, NoMethodError => e
    logger.error "Stripe error while updating card info: #{e.message} for #{current_user}"
    @error = true
  end
```

and we were finally all green! Although I did still get an intermittent fail on the very last build on CI at the end, so this isn't over JavaScript Acceptance tests (shakes fist)!  In summary though the full test suite was green for me locally, and after a re-build on semaphore the build passed.  StripeRubyMock was working for us, and I don't have much appetite for burning more time on the complete sandboxing alternative.  What's frustrating is how much time that goes into this that could be going into refactoring the code for maintainability or improving the user interface experience.  However with the test suite green, we can potentially refactor with confidence (until the next time!).

My concerns about RubyStripeMock is will it be maintained and stay up to date with the Stripe API itself?  To get things to pass I had to change app code that now hasn't been tested in production.  Thomas has updated [his blog post](https://medium.com/craft-academy/keeping-it-simple-3e7d9b186015#.4rc29xv6j) to talk about using RubyStripeMock completely network independently.  Since Stripe told me they are happy with a few hits from tests, and that removing network dependency has not fixed our intermittent failures I'm less concerned about that, than just having code paths that operate in production but don't run in test.  I changed `customer.cards` to `customer.sources` in the app code to get the RubyStripeMock acceptance tests to work.  I will need to use the StripeRubyMock ability to switch to live tests against the Stripe servers to test that still works:

```rb
RSpec.configure do |c|
  if c.filter_manager.inclusions.rules.include?(:live)
    StripeMock.toggle_live(true)
    puts "Running **live** tests against Stripe..."
  end
end
``` 

The advantage of a full VCR/PuffingBilly sandbox, is that you are stubbing at the network level.  Your app is effectively running in the exact same environment as it would with a live network connection.  You can dump the sandbox at any time to re-create an accurate network sandbox.  However it becomes challenging with a complex service like Stripe that's trying to be secure and is using a high degree of indeterminacy in terms of the network connections it makes.  The sandbox cache files have to be checked into git to ensure everyone gets a benefit from the sandbox, so test failures or complex system like Stripe can lead to challenging git churn.  Not checking in those files would be an alternative - CI would still hit live network servers, and on second and subsequent runs developer machines would avoid re-hitting the live network, but then network fails could be encoded into caches.  I starting to think sandbox caches are another challenging source of variability when used with services like Stripe.

In a final analysis it's all about trading off these different heuristics:

* Keep test code as close to production code as possible
* Keep test environment as close to production environment as possible
* Avoid hitting 3rd party servers during test
* Ensure development flow is comprehensible and manageable for developers on your team
* Keep your full test suite green
* Keep the running time of your test suite down to avoid delayed debug cycle hell

and various others.  None of these heuristics always trumps all the others.  As Kent Beck says, "it depends ...".  I say, keep looking for the light at the end of the tunnel :-) 


###Related Videos


* [Solo on WebSiteOne part 1](https://www.youtube.com/watch?v=fDUd9N5iDTA)
* ["Kent Beck" scrum](https://www.youtube.com/watch?v=hz_i4DagxkY)
* [Solo on WebSiteOne part 2](https://www.youtube.com/watch?v=iaRDd35qF_g)