---
title: Frustratingly Slow Debug Cycle
date: 2016-11-03
tags: Stripe VCR PuffingBilly cache http network payment test cucumber batch
author: Sam Joseph
---

Michael had lots of great ideas about tracking people through the site today, but I wanted to quickly push out the "sponsor other users" feature.  I was following the logic from Monday, where dashing out a PayPal endpoint had opened up some payment streams.  I had one person waiting to use the sponsor others, and I was promising myself a phase of consolidation once that was done.  In fairly short order we stamped out some cucumber scenarios:

```gherkin
Feature: Allow Users to Sponsor other members
  As a user
  So that I can help someone else get premium services
  I would like to be able to pay for their premium service

  Background:
    Given the following users exist
      | first_name | last_name | email                  | github_profile_url         | last_sign_in_ip |
      | Alice      | Jones     | alice@btinternet.co.uk | http://github.com/AliceSky | 127.0.0.1       |

  Scenario: User upgrades another user from free tier to premium
    Given I am logged in
    And I visit Alice's profile page
    And I click "Sponsor for Premium"
    When I fill in appropriate card details for premium
    Then I should see "you have sponsored Alice Jones as a Premium Member"
    Given I visit Alice's profile page
    Then I should see "Premium Member"
    Then I should not see "Basic Member"
    Then I should not see "Sponsor for Premium"
    And I should not see "Upgrade to Premium"

  Scenario: non logged in user upgrades another user from free tier to premium
    Given I visit Alice's profile page
    And I click "Sponsor for Premium"
    When I fill in appropriate card details for premium
    Then I should see "you have sponsored Alice Jones as a Premium Member"
    Given I visit Alice's profile page
    Then I should see "Premium Member"
    Then I should not see "Basic Member"
    Then I should not see "Sponsor for Premium"
    And I should not see "Upgrade to Premium"
```

which could be more declarative, but they were good enough for now.  We then stamped out some even messier code to get them green.  This was all me.  I was following my "get it green, THEN refactor" mantra of the previous week.  Just to show you how messy some things were, take a look at this view:

```html
<% if @user.present? && current_user != @user %>
    <% if @plan == 'premium' %>
        <h2 id='payment_complete'>Thanks, you have sponsored <%= @user.display_name %> as a Premium Member!</h2>
        <p>You're seven day free trial has now started.  Your card will not be charged until seven days have passed.</p>
        <p>An AgileVentures mentor will be in touch shortly to help you receive all of your premium membership benefits.</p>
    <% elsif @plan == 'premiummob'%>
        <h2 id='payment_complete'>Thanks, you have sponsored <%= @user.display_name %> as a Premium MOB Member!</h2>
        <p>An AgileVentures mentor will be in touch shortly to help you receive all of your premium mob membership benefits.</p>
    <% elsif @plan == 'premiumf2f'%>
        <h2 id='payment_complete'>Thanks, you have sponsored <%= @user.display_name %> as a Premium F2F Member!</h2>
        <p>An AgileVentures mentor will be in touch shortly to help you receive all of your premium f2f membership benefits.</p>
    <% else %>
        <h2 id='payment_complete'>Thanks, you have sponsored <%= @user.display_name %> as a Premium PLUS Member!</h2>
        <p>An AgileVentures mentor will be in touch shortly to help you receive all of your premium plus membership benefits.</p>
    <% end %>
<% else %>
    <% if @plan == 'premium' %>
        <h2 id='payment_complete'>Thanks, you're now an AgileVentures Premium Member!</h2>
        <p>You're seven day free trial has now started.  Your card will not be charged until seven days have passed.</p>
        <p>An AgileVentures mentor will be in touch shortly to help you receive all of your premium membership benefits.</p>
    <% elsif @plan == 'premiummob'%>
        <h2 id='payment_complete'>Thanks, you're now an AgileVentures Premium MOB Member!</h2>
        <p>An AgileVentures mentor will be in touch shortly to help you receive all of your premium mob membership benefits.</p>
    <% elsif @plan == 'premiumf2f'%>
        <h2 id='payment_complete'>Thanks, you're now an AgileVentures Premium F2F Member!</h2>
        <p>An AgileVentures mentor will be in touch shortly to help you receive all of your premium f2f membership benefits.</p>
    <% else %>
        <h2 id='payment_complete'>Thanks, you're now an AgileVentures Premium PLUS Member!</h2>
        <p>An AgileVentures mentor will be in touch shortly to help you receive all of your premium plus membership benefits.</p>
    <% end %>
<% end %>
```

Ugh, business logic in the view, lots to DRY out, and now I look at it again, some mistakes in wording.  However, all those changes would be fairly easy to make.  The danger is that we don't get to them.  Michael and I went to the "Kent Beck" scrum and changed roles.  The cukes that had been green for me were now failing for Michael, failing on CI, and failing when I pulled them back onto my machine.

It seemed the full set of Premium related features was failing when run in full batch mode (Stripe iFrame not appearing), but would work when run individually, or from a set in their own folder (i.e. `cucumber features/premium/*`).  Michael was then also getting individual failures related to a re-used Stripe token.  We were in a bit of a mess and burnt the best part of an hour investigating.  More importantly we weren't doing any refactoring of the messy app code!

The functionality was working when we run the site manually.  So frustrating.  Trying to push out something to receive some money, that works manually, but the feature tests don't pass due to some odd bug, which turns into a massive time sink.  I wanted to start trying the [stripe-ruby-mock](https://github.com/rebelidealist/stripe-ruby-mock) which the CraftAcademy folks had had some success with.  It was not necessarily a snap fix, but I was starting to really hate all the many fixture files that came with sandboxing complex network interactions.  Occasional cache leaks would mean that developers would suddenly find a load of extra files on their file system.  Pull requests for new features would have 112 instead of 12 changed files.  Maybe the PR display on GitHub could be adapted to handle that, but I was really starting to feel this was all more trouble than it was worth.

Michael was keen to understand these errors and these tests.  We got down into the PuffingBilly config where I remembered there was this additional step to ignore params on a unique URL that Stripe interactions generated.  I got the tests green again, but then they still failed in batch.  Also, Stripe had told us that they were happy with a load on their test servers that was "within reason".  So this morning I tried taking the VCR and PuffingBilly caches off the tests, which would make our tests sensitive to network failures, just to see if that could get things passing in batch; but that still fails.  The test needs JavaScript and I think we have puffing billy hardwired in to all JS tests at the moment.  I could remove that and maybe I could get all the tests passing, but then again ...

One of the biggest issues here is the slow debug cycle.  If the failures only occur in full batch then we have to run the entire cucumber suite, which takes ten minutes.  That might not seem like a lot, but it burns up time.  I've always seen that as a real red flag.  Being stuck with a slow debug cycle is asking for trouble.  Do anything you can to reduce the amount of time before you get feedback on whether a change you make is working or not!  I think the next thing to try is to create a @no-billy tag to opt out these scenarios from headless browser javascript caching to see if that will pass.  Other options include the stripe-ruby-mock gem, or just not having tests on the payment portions, which I've heard some people advocate.  Fingers crossed for a less frustrating session today ...


###Related Videos

* [Pairing on WebSiteOne, part 1](https://www.youtube.com/watch?v=boCPUJ3sdlE)
* ["Kent Beck" Scrum](https://www.youtube.com/watch?v=20ZeJ9FcOxA)
* [Pairing on WebSiteOne part 2](https://www.youtube.com/watch?v=eAdPTF5A5O8)


 


