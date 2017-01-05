---
title: We Are Charity!
date: 2017-01-04
tags: nonprofit, Stripe, Legacy, Refactoring, Single Table Inheritance, PayPal, Upgrades
author: Sam Joseph
---

It finally happened!  The UK government's charity commission approved AgileVentures as an official UK charity.  About 9 months from our original submission, a few emails back and forth, and an apology from the charity commission for accidentally closing our case, we are now a UK registered charity.  A great way to start 2017, and hopefully something that will make our mission a whole load easier.

![announcing charity status on slack](https://www.dropbox.com/s/13vldre5edvledp/Screenshot%202017-01-04%2010.10.10.png?dl=1)

Our official charity number is 1170963, and my next tasks will be to go round email signatures and websites adding that.  I can re-apply to Slack to get their non-profit tier, however I'm not sure we can afford the per user fees even after the nonprofit discount given we have over 1500 in our Slack instance.  Worth a discussion though.  I'll reach out to all our sponsors and pass on the news and the possibility for tax relief on their contributions.  Same for our Premium members.  Will have to check how all that works. 

The charity approval capped a big day of admin yesterday, shaking off the holiday cobwebs.  I caught up with my email and Slack and reviewed all the open pull requests from premium members on WebSiteOne and LocalSupport.  The big things to sort out are the new Agile Development using Ruby on Rails MOOC, a grant application with the Wikimedia edu dashboard folks, and the pricing and support package for the MetPlus project.  MetPlus is nearing the release stage and that links in to our new [non-profit support plans](http://www.agileventures.org/nonprofit-basic-support), which themselves need fleshing out and some payment endpoints merged into the generic plan support code we were working on before the break.  I'd also love to make more progress on the AsyncVoter bot code, to help projects chug through voting on their ticket backlog, but one thing at a time :-)

The non-profit support plans are an idea from Armando, since he often has non-profit groups wanting codebases maintained after student teams have built things for non-profits in his class and then moved on.  I have a slightly nervous feeling that this is the way to have me spending even more and more time supporting legacy codebases with serious shortcomings, when what I'd really like to be doing is be more involved in the creation of maintainable codebases; but perhaps supporting legacy codebases is actually what I'm destined to do, and we can't fight out destiny :-)

Speaking of legacy codebases the current PR for supporting generic plans includes a feature to allow subscription to the NonProfit Basic Support plan:

```gherkin
@stripe_javascript @javascript
Feature: Subscribe to NonProfit Basic Support
  As a nontechincal admin of a nonprofit or charity
  So that we can maintain our technical offerings
  I would like to subscribe to AV NonProfit Basic Support for charities

  Background:
    Given the following plans exist
      | name                    | id             | amount | free_trial_length_days |
      | NonProfit Basic Support | nonprofitbasic | 2000   | 0                      |

  Scenario: Sign up for nonprofitbasic  support
    Given I visit "subscriptions/new?plan=nonprofitbasic"
    And I click "Subscribe" within the card_section
    When I fill in appropriate card details for nonprofitbasic
    Then I should see "Thanks, you're now an AgileVentures NonProfit Basic Support Member!"
```

The background step is an indicator of how this PR is evolving the simple Plan class (that was hidden at the end of the `SubscriptionsController`) and has changed into a full Active Record model.  Initially I had resisted this change as I was thinking that plan information stored in the Stripe database (and accessible via API) would be our single source of information about Plan data.  However the inclusion of PayPal support made it clear that the concept of a Plan was something firmly in our AgileVentures WebSiteOne domain model.  It seems like PayPal may be sufficiently flexible that plan details such as price and trial length etc. don't need to be stored on PayPal, but the idea of us being dependent on the Stripe API to display information about our Plan pricing etc. seemed unappealing.  Stripe have good servers, but we don't want to wait for a response from them to have authorative information about our Plans.

This actually highlights one odd thing about the Stripe setup.  You can display any number in the Stripe popup, but what actually gets charged depends on the data stored on the Stripe servers.  We've been bitten by this when one Stripe plan had the wrong value and one Premium member was charged incorrectly.  It was quickly fixed, but it's not ideal.  Another issue with Stripe is the pro-rated payment when members upgrade from one plan to another.  This leads to an unexpectedly large payment at the beginning of the next month.   I feel like it would be better to have a single payment at the point of upgrade, at least from a customer surprise point of view.    

I'm not sure that there's any easy fix for either of these two, but some possible solutions to the first might be:

a) have the checkout popup grab the amount displayed from the Stripe server rather than from the local system   
b) have our system lookup the amount on the stripe servers and then fill into the checkout (something we could do ourselves)  
c) allow local systems to specify plan details dynamically rather than via an id lookup (paypal seems to do this)  

As regards the second issue, it would be great if we had a third option (as an alternative to two current two options of either prorating, or upgrading the next month), which would be to charge the customer the cost of the upgrade for the rest of the month now, and then just charge the normal amount of the upgraded amount at the beginning of the next cycle.

So I sent Stripe the above section of this blog, and we'll see what they say.  Getting back to the current PR on WebSiteOne, the refactoring of Plan from "cyst class" to AR model has shrunk the SubscriptionsController down to a more manageable level, and allowed us to delete a number of views and mailer endpoints, so that signing up for a Plan involves a single template for both mailing and web pages, modified according to the plan being subscribed to.  Beautiful DRYness.  We're also dropping [Single Table Inheritance (STI)](http://nonprofits.agileventures.org/2016/09/26/confident-coding/) from the Subscription model, since having plans be different sub-classes implies changes to the codebase to support them, which is not as flexible as we need at this point.  PaymentSources still use STI (PaymentSource::PayPal and PaymentSource::Stripe) and that seems reasonable for the moment as the number of Payment sources will likely expand more slowly and each new one will probably need new code.  So Subscriptions now belong to plans.  I'd love to dash off a quick UML diagram but maybe I'll save that for my book on Agile Project management :-)  I'm already over time on this blog!

So the PR is pretty much good to go. I even added a rake task to migrate the data from the old to the new structure.  Only thing is that the language of the templates for the individual premium users doesn't make so much sense for the organisations that would be signing up for NonProfit Basic Support, so I think we need another field on the Plan model to indicate whether it's a plan for an individual or an organisation and then we can modify the template language correctly, but I'll save those details for another PR.  Also the crossover on templates means we now in principle support Paypal payment for the higher level plans, although that emergent feature isn't covered by feature tests, and we still don't have good upgrade paths, but this PR is already large and needs testing on staging.  Could we hide part of it behind feature flags?   I think at this point we just need to get it out there.

It feels like if we can get this out, add the org/personal element, test all the payment options, and do the upgrade functionality (i.e. make it generic), we'll have most of what we need on the payment side and perhaps we can move on to other features and systems.  Fingers crossed! :-)


###Related Videos:

* ["Martin Fowler" Scrum](https://www.youtube.com/watch?v=OUeZEoN_JbY)
* ["Kent Beck" Scrum](https://www.youtube.com/watch?v=n1Xn5_qBFJE)

###Related Blogs:

* http://nonprofits.agileventures.org/2016/09/26/confident-coding/
* http://nonprofits.agileventures.org/2016/10/27/red-green-refactor/
* http://nonprofits.agileventures.org/2016/11/03/frustratingly-slow-debug-cycle/
* http://nonprofits.agileventures.org/2016/11/04/light-at-the-end-of-the-tunnel/
* http://nonprofits.agileventures.org/2016/11/07/refactoring-ftw/
