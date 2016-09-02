---
title: More Sandboxing
date: 2016-09-02
tags: pair programming ruby rails stripe vcr puffingbilly gems sandboxing networking caching
author: Sam Joseph
---

So, following up on yesterday's [post on sandboxing](http://nonprofits.agileventures.org/2016/09/01/how-much-sandboxing/) acceptance tests of Stripe's credit card functionality in Rails, Michael and I did another pairing session.  In a couple of hours we had the tests passing, and the basic card update functionality in place, but not without a little jiggery-pokery with the way that the PuffingBilly gem stores its HTTP cache files.  The point of departure was that having added an extra Cucumber scenario for the card update operation, a related scenario about signing up for premium plus failing with the common refrain of:

```
You cannot use a Stripe token more than once: tok_18CsXM... (Stripe::InvalidRequestError)
```

Now I am not 100% certain, but I had strong suspicions that this was because the Billy cache is shared between different scenarios, and so a token that gets used in one scenario gets re-used in another.  As I described in some detail yesterday, Stripe interactions involve network connections to the Stripe servers from the users browser via Ajax, and from our server side.  VCR caches and plays back http connections that our server makes, but does nothing for the Ajax requests made by the headless browser as part of our Cucumber/Capybara/Poltergeist/PhantomJS acceptance test.  

I found the [billy github issue](https://github.com/oesmith/puffing-billy/issues/152) where I had been asking about functionality to support different caches for different scenarios, and saw ronwsmith's suggestion about changing the cache_path in a before block.  VCR does this automatically, and I guess we could get this into Billy by default with a PR, but in the first instance we created the following:

```rb
Before('@javascript') do |scenario, block|
  Billy.configure do |c|
    feature_name = scenario.feature.name.underscore
    scenario_name = scenario.name.underscore
    c.cache_path = "features/support/fixtures/req_cache/#{feature_name}/"
    Dir.mkdir(Billy.config.cache_path) unless File.exist?(Billy.config.cache_path)
    c.cache_path = "features/support/fixtures/req_cache/#{feature_name}/#{scenario_name}/"
  end
end
```

This took several passes and a bit of poking around in the Billy code to reveal that we had to make the directories incrementally, but with this in place we had separate http caches set up for each scenario.  Although this means checking in some cache files repeatedly it ensures our tests are independent, and this allowed all of our charge related scenarios to pass both individually and in batch.  We still hadn't finished the scenario to test all the way through to changing the customers card.  Pushing on there we still got the same error in a single scenario when we tried to have more than one interaction with the Stripe server.  I'm not 100% sure we have the ideal setup, but we have a big load of Billy config to ignore the URL params on many Stripe related requests like so:

```rb
Billy.configure do |c|
  c.cache = true
  c.cache_request_headers = false
  c.ignore_params = ['https://api.stripe.com/v1/tokens',
                     'https://q.stripe.com/',
                     'https://js.stripe.com/v2/',
                     'https://checkout.stripe.com/api/outer/manhattan',
                     'https://checkout.stripe.com/api/account/lookup',
                     'https://checkout.stripe.com/',
                     'https://checkout.stripe.com/v3/',
                     'https://checkout.stripe.com/v3/data/locales/en_gb-TXHkb1MWMa7xOQfCZf1DFA.json',
                     'https://checkout.stripe.com/v3/data/locales/en_us-tZLon0RoQY0knbOURjQ.json',
                     'https://checkout.stripe.com/v3/data/locales/en_gb-LkmkoD88BacHIqnX4OXm6w.json',
                     'https://checkout.stripe.com/v3/BFV9gQSjIO6MQNzvbBr9GA.html',
                     'http://checkout.stripe.com/v3/BFV9gQSjIO6MQNzvbBr9GA.html',

  ]
  c.persist_cache = true
  c.cache_path = 'features/support/fixtures/req_cache/'
  c.non_successful_cache_disabled = false
end
```

Our VCR it set up to ignore all parameters on requests:

```rb

VCR.configure do |c|
  c.hook_into :webmock
  c.cassette_library_dir = 'features/support/fixtures/cassettes'
  c.ignore_localhost = true
  c.default_cassette_options = {
      :match_requests_on => [
          :method,
          VCR.request_matchers.uri_without_param(:imp, :prev_imp)
      ]
  }
end
```

I did open a [ticket](https://github.com/oesmith/puffing-billy/issues/147) to request a similar feature on Billy.

Anyhow, getting the Billy cache to work with Stripe seems particularly tricky.  Without many of the ignore params specifications in the Billy config each new run of the tests will generate new entries in the Billy cache, because they are requesting a resource over the network with different ids in the URL params.  The tokens and ids from these requests then generate new requests that VCR caches, and so we have a cache leak situation.  Without the right set of ignore_params in Billy we are still hitting the network every time, and there are confusing extra files lying around which other developers don't know what to do with.  To check in?  To delete?  Note also that some of the Stripe requests have hash ids in the URL themselves, so we have to create the test, run once, check the URL from the cache and then add that specific config.  Also there are other requests that Stripe JS makes based on the locale of the developer.  Locking this stuff down is really helped by the fact that Michael is based in the US and I'm in the UK, so we can push the code back and forth quickly.  That fact really helped us address some of the tricky timezone issues we've had in AgileVentures WebSiteOne.

So if you look carefully you can see the individual locale requests that we're adding to the Billy config to ensure that developers in en_gb and en_us don't experience cache leaks.  For developers in other locales we'll have to add extra elements to the Billy config, or get that [general param ignore functionality](https://github.com/oesmith/puffing-billy/issues/147).

Now I could go in there and start removing elements from the Billy config to see if we really need all those different ignores, but having spent the best part of two hours on it yesterday, that's not very appealing.  It's a slow process of running a series of acceptance tests, checking to see if there are new files generated, investigating the contents of those files, adjusting the config, deleting all the cache files in two places, and then re-running the acceptance tests.  Maybe some of that could be automated if we linked the two caches somehow (same naming conventions?).  And is anybody doing this besides us?  It's not rocket science, but it is fiddly.

The time we spent on caching could have been spent on a few of the other things that I'd love to have in place, such as getting all our AV logos for Stripe sorted, linking the card update into the UI flow (although that probably should wait till we've done a live test on production), refactoring out a separate Stripe customer table (since the User table is now a bit bloated), migrating the Stripe customer data from a separate store, and so on.

Stripe customer support is pretty solid actually.  They'd previously given helpful advice that even our test (vs live) tokens should probably not be checked into version control, which we've tried to avoid, although I have a feeling that Billy doesn't have the VCR-like ability to [strip sensitive tokens from the http caches](https://github.com/oesmith/puffing-billy/issues/143).  We were also wondering if it was okay to store stripe Customer ids in our database directly, or if we should be encrypting them like we do with passwords.  Stripe support emailed back to say that they should be in a secure database, but wouldn't usually need more security than usernames and emails, which is one less thing to worry about. Thanks Stripe!

I think the key thing I now need to ask Stripe is what there thoughts are on fully sandboxed acceptance tests.  It's pleasing that we've managed to get a green test suite with no cache leaks (fully sandboxed) but I'm not sure how much it buys us.  We have a strong set of regression tests so if there is some change in our codebase that breaks these tests we'll know about it pretty fast; but the real acid test is whether the customer can do what they want on our site, and we won't know that for sure when a customer tried to update their card details for real.   Will all of our work testing really pay off?

