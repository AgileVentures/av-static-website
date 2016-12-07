---
title: Speed Coding and Blogging
date: 2016-12-06
tags: CSS stylesheets controller Stripe paypal refactoring Cucumber regression characterisation legacy Twitter YouTube
author: Sam Joseph
---

Monday was the usual fight against torpor, but did get an hour's pairing in with Michael on WSO where Michael drove a finish to putting the Paypal payment button on the same page as the Stripe credit card payment.  Before he started on that I got him to pull out the refactoring of the ChargesController to SubscriptionController into a separate pull request.  In the background I had half an eye on the builds of that which were failing due to some regressions on changes to cucumber steps.  We pared down the feature for putting the Paypal element front and center to:

```gherkin
@stripe_javascript @javascript
Feature: Subscribe Self to Premium
  As a developer
  So that I can get recurring professsional development support and code review
  I would like to take out an AV Premium Subscription

  Background:
    Given the following plans exist
      | name        | id          |
      | Premium     | premium     |

  Scenario: Pay by card
    Given I visit "subscriptions/new"
    And I click "Subscribe" within the card_section
    When I fill in appropriate card details for premium
    Then I should see "Thanks, you're now an AgileVentures Premium Member!"
    And the user should receive a "Welcome to AgileVentures Premium" email
    # And my member page should show premium details # TODO IMPORTANT - require login?

  Scenario: Pay by Paypal
    Given I visit "subscriptions/new"
    Then I should see a paypal form within the paypal_section
    # And the user should receive a "Welcome to AgileVentures Premium" email
    # And my member page should show premium details # TODO IMPORTANT - will need hookup
```

Leaving in a few comments pointing at important future work.  Feel a bit messy about that - would it be better to be pointing to tickets as part of the bigger premium epic there?  Over lunch I left Michael pairing with Matt who'd been observing our pairing session.  They were looking at the feature on preventing tweets of dud videos that Sasha had left, and rather than get involved in a complex refactoring I suggested that the way forward was a new branch that wrapped the existing Twitter functionality in acceptance tests.  "Characterisation tests" of legacy code as Michael reminded us.  We'll see if all this breaking out of smaller PRs bites us with merge conflicts or makes review easier.  Raoul's been really busy recently so we might have to look at our PR review and deploy process to take account.

After lunch I solo'd to add a sad path to the refactoring of the Premium branch that we'd identified since one missing part of the refactoring wasn't caught by the tests.  I got that green and then started on tweaking the CSS for the Premium payment page now that it included both Paypal and Stripe buttons.  Working with the CSS was frustrating, as our controller specific stylesheets don't load automatically on WSO, and even though I identified the code in the layout that would, turning that on produced [weird breakages throughout the cucumber tests](https://github.com/AgileVentures/WebsiteOne/issues/1445).  There's definitely something a little strange going on with the layout - which I think is also affecting performance on my local machine when running in develop mode.  Could this be affecting performance on the main system?  Perhaps a review of our layouts is in order.

I hacked out a reasonable solution pulling in the controller specific stylesheet directly in the view:

```html
<%= stylesheet_link_tag params[:controller], 'data-turbolinks-track' => true  %>
```

and found that the bootstrap `well` class produced the sort of effect I wanted:

![payment buttons](https://www.dropbox.com/s/eelk7vro2owibuv/Screenshot%202016-12-06%2009.40.56.png?dl=1)

The missing piece going forward is getting the Paypal payment to hit a webhook and update our WSO backend when payment completes.  From the Paypal documentation this looks not too hard.  It makes me think that there might have been a simpler way to get set up with Stripe buttons.  Do we actually have to hit Stripe's server from our server to sign someone up to a subscription?  Could there be a pure button equivalent?  Although all the docs I'm looking at suggest that you still have to explicitly create the customer with a server side call.  Anyhow, further Paypal integration is the priority if we are going to support Paypal based sponsoring of other members.

Tinkering with the CSS all burnt a lot of time and I reflected that I'm behind on sending out emails to AV members, pushing older blogs to medium and social media, and there are a lot of Premium pull requests to review, let alone project management for LocalSupport, AsyncVoter and making sure we are delivering to are commitments to Sponsors.  Short of making more time in the day I think I have to limit the time I spend writing the daily blog to a tight 20 minutes.  People keep telling me I'm too verbose generally so perhaps that will be an improvement :-)

###Related Videos

* ["Martin Fowler" Scrum](https://youtu.be/KqT5XWirMRg)
* [Pairing on WSO](https://www.youtube.com/watch?v=y7bHXz0kCQA)
* [Soloing on WSO](https://www.youtube.com/watch?v=EOlcarJgn8s)
* ["Kent Beck" Scrum](https://www.youtube.com/watch?v=RQc_9isrGJY)




