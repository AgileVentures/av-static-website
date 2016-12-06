---
title: Refactoring and Pairing
date: 2016-12-05
tags: activity controller create credit cucumber custom definitions feature paypal premium refactoring sponsor step subscriptions upgrade communication
author: Sam Joseph
---

So hot on the heels of our first AV member sponsoring other AV members for Premium support, another Premium member was talking about sponsoring some of their co-workers for Premium membership.  Now this particular Premium member prefers to pay through Paypal, which we had set up previously.  The Paypal endpoint in the AgileVentures site is just a custom button from the Paypal toolkit, on a custom method in the charges controller.  We'd just thrown that in, unlinked to the rest of the site, to serve as a quick check that the functionality would work and that the individuals requesting Paypal payment would actually use it.  That purpose was served admirably and was used by two Premium members, so tracer-bullet success.

Now if we wanted to allow more folks to sign up with Paypal, and indeed sponsor other members via Paypal, we had some integration to do.  Not only that, it was clear that the charges controller needed some serious refactoring.  I had originally created the charges controller on a spike, following the instructions in the Stripe documentation for creating a charge on a user credit card.  I'd immediately hooked things up to ensure that the user would be paying for a subscription to a plan.  We'd wrapped that whole functionality in VCR/PuffingBilly sandbox tests and then evolved that to RubyStripeMock, as well as adding the ability to sponsor others, and change credit card details.  We'd also evolved a domain model that started to capture some of our "business" logic, relating to the different kinds of Subscriptions and PaymentSources that we were supporting.  What we hadn't managed to refactor was the ChargesController itself which was breaking the Rails convention of having controllers manipulate resources of the corresponding name.

Our ChargesController was actually manipulating Subscriptions and CreditCards.  Over the weeks I felt pretty certain we wanted to rename our ChargesController to SubscriptionController, since that was the key resource that most of the controller methods were manipulating.  The CreditCard stuff should ultimately be moved to a different controller and the Paypal endpoint integrated into some RESTful manipulation of Subscription resources.  Michael and I started off driving the change by creating a `subscribe_self_to_premium.feature` file.  The main cucumber feature file was inappropriately named `charge_activity.feature`.  Again this was all part of my initial rush to spike out some charge pathway and demonstrate that revenue could be generated.  Having demonstrated that it was clearly time to clean up the name space.  The new feature file took inspiration from the scenarios in the existing `charge_activity` file and looked like this:

```gherkin
Feature: Subscribe Self to Premium
  As a developer
  So that I can get recurring professsional development support and code review
  I would like to take out an AV Premium Subscription

  Scenario: Pay by card
    Given I visit "subscriptions/new"
    And I click "subscribe" within the card_section
    When I fill in appropriate card details for premium
    Then I should see "Thanks, you're now an AgileVentures Premium Member!"
    And the user should receive a "Welcome to AgileVentures Premium" email
    And my member page should show premium details

  Scenario: Pay by Paypal
    Given I visit "subscriptions/new"
    And I click "subscribe" within the paypal_section
    Then I should be redirected to Paypal's payment screens
    And the user should receive a "Welcome to AgileVentures Premium" email
    And my member page should show premium details
```

These are still a little imperative, but it's generally too challenging to try and fix too many issues at once.  Right here we were trying to get the name space fixed up.  Tweaking Cucumber scenarios to be more declarative could come later.  As it happened, we did already have a paypal feature test:

```gherkin
Feature: Charge Users Money
  As a site admin
  So that users can pay for premium services via paypal
  I would like to be able to sign them up for a recurring plan via paypal

  Scenario: Sign up for premium membership via paypal
    Given I visit "/charges/paypal"
    And I should see "Agile Ventures Premium Membership"
    Then I should see a paypal form
```

My hope was that ultimately we'd be replacing this feature and the contents of `charge_activity.feature` with this new file, that had a stronger indication of the high level goal in its name, i.e. Subscribe Self to Premium.  Of course that didn't stop me from agonizing whether the `Given I visit "subscriptions/new"` step was too high level, or whether writing it to force us to code a new URL endpoint and thus controller was the wrong thing to be doing at that stage.  I do believe strongly that the URLs are part of the end user interface experience for both developers and users.  The counter-argument in my mind was that we should get the cukes green before doing a refactoring, but really this entire activity was a refactoring of both tests and application code to consolidate the two payment methods (that both worked independently) into a single coherent entity.  Perhaps we should have only been refactoring tests and app separately?  

You might also be wondering what happened to the feature to sponsor other members via Paypal.  Discussing it, we'd decided that we needed to consolidate the existing functionality first before taking on that more complex task.  Either which way, the missing `/subscriptions/new` endpoint prompted a refactoring of the ChargesController to SubscriptionsController, which we executed automatically through RubyMine. RubyMine got most of the changes for us, but there was some mopping up to do with additional changes in the Cucumber files, and a few of the views.  Then we got stuck on a regression test failure for a while.  We weren't making any progress on the high level integration of Paypal and Stripe payments:

```
  Scenario: User decides to change card details                                                                     # features/premium/charge_activity.feature:60
    Given I am logged in as a premium user with name "tansaku", email "tansaku@gmail.com", with password "asdf1234" # features/step_definitions/user_steps.rb:5
    And I visit "subscriptions/tansaku/edit"                                                                        # features/step_definitions/basic_steps.rb:65
    And I click "Update Card Details"                                                                               # features/step_definitions/basic_steps.rb:74
    When I fill in updated card details for premium for user with email "tansaku+stripe@gmail.com"                  # features/step_definitions/charge_steps.rb:18
    Then I should see "Your card details have been successfully updated"                                            # features/step_definitions/basic_steps.rb:182
      No route matches [PUT] "/subscriptions.tansaku-rodriguez" (ActionController::RoutingError)
      features/premium/charge_activity.feature:65:in `Then I should see "Your card details have been successfully updated"'

Failing Scenarios:
cucumber features/premium/charge_activity.feature:60 # Scenario: User decides to change card details
```

There was some kind of routing issue.  It was frustrating to be blogged, but also, this is what all these tests are here for.  We'd refactored the core controller of the payment framework, and it seemed we'd broken the credit card details update in the process.  Without the tests we'd have pushed this out to production and not known until a user encountered a problem.  Eventually we tracked the issue down.  It was a pluralization error in one of the views.  Our routes now looked like this:

```rb
  match '/subscriptions/paypal' => 'subscriptions#paypal', :via => [:get]
  match '/subscriptions/upgrade' => 'subscriptions#upgrade', :via => [:put]
  resources :subscriptions
```

giving us this set of endpoints:

```
           subscriptions_paypal GET         /subscriptions/paypal(.:format)                             subscriptions#paypal
          subscriptions_upgrade PUT         /subscriptions/upgrade(.:format)                            subscriptions#upgrade
                  subscriptions GET         /subscriptions(.:format)                                    subscriptions#index
                                POST        /subscriptions(.:format)                                    subscriptions#create
               new_subscription GET         /subscriptions/new(.:format)                                subscriptions#new
              edit_subscription GET         /subscriptions/:id/edit(.:format)                           subscriptions#edit
                   subscription GET         /subscriptions/:id(.:format)                                subscriptions#show
                                PATCH       /subscriptions/:id(.:format)                                subscriptions#update
                                PUT         /subscriptions/:id(.:format)                                subscriptions#update
                                DELETE      /subscriptions/:id(.:format)                                subscriptions#destroy
```
We had both `subscription_upgrade` and `subscriptions_upgrade`.  It was frustrating to have been stuck on this issue, but it was also an interesting lesson about how the custom `upgrade` endpoint was technical debt that we were having to pay off here.  We'd thrown in that custom endpoint to enable a member to change their credit card in a hurry, but the presence of the custom endpoint had confused us during a refactoring.  If we'd refactored that out earlier to a CardController we probbaly wouldn't have got stuck here.  So overall not a bad object lesson on the value of tests and of refactoring to a clean domain model!

While we were doing all this (and waiting for test suites to run etc.) Michael and I were discussing work on the project in general.  Michael was commenting that he had felt more motivated in the longer running work on LocalSupport.  It sounded like aspects of the "drive-by" coding style we had been employing recently were frustrating him, i.e. that we were only working on things that could be done in a hour or two.  Also, he said he didn't feel like doing additional work on the project at the weekend as he anticipated I would be too critical of decisions he might make.  This was great feedback for me, as the last thing I want to be doing is de-motivating people to work on the project.  I'd like to think of my criticism of design decisions as constructive, but it's a delicate matter.

I guess Michael has sensed my frustration at earlier phases of the WebSiteOne project which seemed to have involved long running large pull requests and the addition of more features than I was comfortable with.  I've definitely been burnt by this in the past; pushing on people too hard over the approach that I think is right.  It seems sensible to me that we don't just merge in code to a project when concerned over the future maintainability of that code, but then again if our push back is too strong then no one will want to work on the project with us.  Where do we draw the line?  Everyone is going to have a different level of tolerance for criticism, constructive or otherwise.   And we're all going to have variable abilities to detect whether what we are saying is making others uncomfortable or de-motivating them.  Perhaps I'm coming from a place of too much tension for the entire AgileVentures undertaking?  In the past it was a part-time fun activity, and I was more relaxed about my future income from HPU or MakersAcademy.  Ironically at that point I was less sensitive about whether others might feel negative about discussion of the pros and cons of code or design.  Now I like to think I've become more sensitive but the stakes are higher, and I really want to create codebases that will enhance our processes rather than be a burden in future.

Gosh, what a complex thing to try and get right.  I thought managing a VCR/PuffingBilly cache was hard, but getting that inter-personal teamwork balance right ... well that's the real trick, ain't it? ... :-)

###Related Videos

* ["Martin Fowler" Scrum](https://www.youtube.com/watch?v=OfvuX1rNtu0)
* [Pair Programming on WSO](https://www.youtube.com/watch?v=Q36xbc8pUZ4)
* [Kent Beck Scrum](https://www.youtube.com/watch?v=3BVth-ZXJk8)
