---
title: How Much Sandboxing?
date: 2016-09-01
tags: pair programming ruby rails stripe vcr puffingbilly gems sandboxing networking caching
author: Sam Joseph
---

Michael and I were pairing on a new feature for the AgileVentures Stripe integration.  As the number of premium members paying recurring subscriptions via credit card is increasing we come to issues such as wanting to edit credit card details.  Stripe has a handy recipe for allowing users to update their card details:

[https://stripe.com/docs/recipes/updating-customer-cards](https://stripe.com/docs/recipes/updating-customer-cards)

Apparently passing expiry dates (and even changing card numbers?) are already [handled automatically](https://stripe.com/blog/smarter-saved-cards) by Stripe, which is good to know, but users may want to switch to a different card.  Unfortunately for us the recipe for the Stripe card update feature is in PHP.   We managed to figure things out referring to it for reference and to a handy [Stack Overflow post](http://stackoverflow.com/a/28548367/316729).  The load bearing part of the code is this:

```rb
  def update
    stripe_customer_token = current_user.stripe_customer
    customer = Stripe::Customer.retrieve(stripe_customer_token) 
    card = customer.cards.create(card: params[:stripeToken])
    card.save
    customer.default_card = card.id
    customer.save
  rescue Stripe::InvalidRequestError => e
    logger.error "Stripe error while updating card info: #{e.message}"
    errors.add :base, "#{e.message}"
    false
  end
```

It took us a little while to get clear on how this all worked.  The `Stripe::Customer` entity is provided by the Stripe gem and allows our rails server to make a request to the Stripe server to retrieve details about a customer.  The tricky thing is that the update operation can only happen on our server after we've served a form to the user's browser that will make an ajax request to the Stripe servers directly from the users computer.  That form is generated using the following template:

```html
<%= form_tag charge_path(@current_user.name), method: :put do %>
  <script
  src="https://checkout.stripe.com/checkout.js" class="stripe-button"
  data-key="<%= Rails.configuration.stripe[:publishable_key] %>"
  data-name="Agile Ventures"
  data-panel-label="Update Card Details"
  data-label="Update Card Details"
  data-allow-remember-me=false
  data-locale="auto">
  </script>
<% end %>
```

When a user wants to edit their card details we serve this to their browser.  The user's browser loads Stripe's checkout.js library and uses the base details we provide to generate a popup form like this:

![Stripe pop up form](https://www.dropbox.com/s/ja4rsvbra17t8qk/Screenshot%202016-09-01%2008.49.19.png?dl=1)

The user puts their updated details into the form, and when they hit submit that data is sent encrypted to the Stripe servers. We never see it directly on our server, which is clearly good from a security standpoint.  So the Stripe JS library is enabling a direct communication between the user's computer and Stripe.  Once Stripe has processed the data it generates a token and redirects the user's browser back to our server, appending that token to the URL.  That's the point at which the update method above gets called.  We store the Stripe customer id in our user database and can use that to retrieve customer details from the Stripe server on our rails server.  Confused? :-)

Given that additional information we can move on to understand the rest of the update method.  We now call 

```rb
card = customer.cards.create(card: params[:stripeToken])
```

This `params[:stripeToken]` is the token that Stripe generated on its servers after it received the new card details from the user.  It's essentially a "session" that has the card details associated so we can use it to instantiate a card object that represents the customer's new card.  Calling save on it might seem like we are storing something in our local database, but we're not.  We're generating another request to the Stripe servers that this card should be remembered by Stripe and then we're setting it as the default card for the customer and then calling save on customer generates another network request to Stripe to ensure that the new card is used in future.

So there's a lot of networking going on there behind the scenes.  Stripe helpfully provides test tokens and a test Stripe endpoint for testing purposes.  For our automated tests we assume that it's not good practice to keep hitting the Stripe endpoints so we use the [VCR gem](https://github.com/vcr/vcr) to record all the interactions between our rails server and Stripe.  Of course it's not just the Rails server that's communicating with Stripe.  It's also the user's browser via Ajax calls, so to properly sandbox everything in our acceptance tests we use the [puffing billy gem](https://github.com/oesmith/puffing-billy), which acts like VCR but on our headless browser (phantomjs) instead of on our server.

Getting these sandboxing gems all set up to work with the Stripe interactions is not trivial, and requires a fair amount of playing with configuration.  This is because Stripe won't allow the same token to be used twice, and naturally it is very sensitive to attempts to hack it, so if the environment is slightly off what it expects then it will refuse to operate.  We'd previously got the full sandboxed acceptance tests working for our premium and premium plus subscription sign up processes.  Adding this new functionality (which isn't completed yet) is causing me to think through this whole set up again.

We had to start tweaking the config again to get the new tests to pass, and where we are up to currently some of the old tests are now failing.  Maybe that will be an easy fix today and maybe not, but I wonder to what extent other organisations are writing fully sandboxed acceptance tests of this kind of functionality?  The guideline I've been following is that to be a good net citizen you shouldn't allow your acceptance tests to make network calls to third party sites, as that puts unnecessary load on other people's servers, and introduces a dependency and latency into your tests.  But have I been overly-zealous here?  Companies like Google and Stripe expect their services to come under heavy load.  Perhaps they won't even notice the extra load from our test runs? Note: we sandbox our Google Maps interactions in the LocalSupport project.

Avoiding external dependencies and network latency in our test suites sounds good, but if the majority of us have fast internet connections all the time, and those remote services are up close to 24/7 is that really such a big deal?  I guess it all depends on just how hard it is to maintain the acceptance test suites.  One big downside with sandboxing is that if the 3rd party endpoints change then we can have the situation where all our tests pass but the app is broken.  VCR and PuffingBilly at least make it simple to reset in that situation.  Both gems record network interactions to static files and then play them back to simulate the situation of being connected to the network.  Those recordings go out of date, but you can delete them and re-record, then check in the new files and you are bang up to date.  Much faster than having to re-stub individual network calls with something like webmock, but then perhaps our acceptance tests shouldn't try to be so high fidelity, or perhaps we should rely more on unit tests?

PuffingBilly is newer than VCR and it doesn't yet have some of the features that I rely on in VCR such as grouping network recording files in directories named after the test that generated them, and config to clean sensitive data out of the recordings.  I imagine those will come - I've opened some tickets that I'll check again now, but I really wonder how the majority of others are doing things?  I guess I'll ask Stripe what their testing recommendations are, but the thing I think I've really learnt over the last few years is that you should be cautious taking a guideline to the limits.  Pursuing 100% test coverage, or 100% test driven, or perfect adherence to style guidelines often generates more friction than anything else.  I love the idea of fully sandboxed acceptance testing, but is it really practical as a real world system grows and has to cope with an increasing number of corner cases?