---
title: Refactoring FTW!
date: 2016-11-07
tags: pullrequests controller view private test DRY dependencies notificiations functional
author: Sam Joseph
---

Friday was full of pull request reviews.  I'm currently trying to keep tabs on pull requests coming in on the following projects:

* [AsyncVoter](https://github.com/AgileVentures/AsyncVoter/pulls)
* [LocalSupport](https://github.com/AgileVentures/LocalSupport/pulls)
* [ProjectScope](https://github.com/AgileVentures/projectscope/pulls)
* [Redeemify](https://github.com/StrawberryCanyon/redeemify/pulls)
* [WebSiteOne](https://github.com/AgileVentures/WebsiteOne/pulls)

I've started adding extra notifications to the main slack channels about pull requests opening and closing.  We have separate automated notification channels, but I think pull requests should be highlighted.  When a pull request opens everyone involved in the project should have a look if they can, and make a comment, or just a thumbs up.  Then when a pull request gets merged in that's a time for everyone to thank the submitter of the pull request for completing that work, and then it's great to thank them again when the code is deployed to production.

AsyncVoter has gotten really active and there's been a lot to review in our node project recently, particularly in terms of locking down how the REST api will work.  LocalSupport goes in fits and starts.  ProjectScope has a group of Berkeley students working on it, and there are weekly flurries of activity.  Redeemify has a couple of folks pushing away, and WebSiteOne only has a few people working on it.  WebSiteOne used to have a lot more developers, but I pushed to ring-fence it for only premium members, since I was worried about maintaining it in the face of too many people working on it at the same time, particularly if those people had variable levels of commitment.

At least now I'm getting a handle on a codebase that isn't too highly in flux, but there's a lot of stuff that needs doing.  Sasha has been knocking off small tickets pretty regularly the last couple of weeks, which is awesome.  In recent months most of the larger PRs have come from myself and Michael with some notable contributions from Raoul.  There was time for some pairing Friday afternoon, and I invited Ben, my friend from our new sponsor drie.  It ended up being just Ben and myself and we ended up spending a devops sessions looking at drie deploys, and how their different products might match different AgileVentures projects.

What I didn't get onto was the refactoring of the "Sponsor a Premium User" functionality that we finally had green tests for.  With the time change my Friday meetings ended earlier than usual, and so I got a quick end of week refactoring session in as I was gagging to complete the feature to the point it could be deployed.  The big ugly view from earlier of the week got boiled down to:

```html
<% if @sponsored_user %>
    <h2 id='payment_complete'>Thanks, you have sponsored <%= @user.display_name %> as a <%= @plan %> Member!</h2>
    <% if @plan.free_trial? %>
      <p>A seven day free trial has now started.  Your card will not be charged until seven days have passed.</p>
    <% end %>
    <p>An AgileVentures mentor will be in touch shortly to help <%= @user.display_name %> receive all their membership benefits.</p>
<% else %>
  <h2 id='payment_complete'>Thanks, you're now an AgileVentures <%= @plan %> Member!</h2>
    <% if @plan.free_trial? %>
        <p>You're seven day free trial has now started.  Your card will not be charged until seven days have passed.</p>
    <% end %>
    <p>An AgileVentures mentor will be in touch shortly to help you receive all of your membership benefits.</p>
<% end %>
```
although still too much logic for my tastes.  The main if-else block there could be replaced by a call two different templates, although having them side by side in one file highlights the two alternatives here.  I could have pushed and boiled down further, but this part of the codebase is definitely in flux, so there are diminishing returns from pushing too hard on a refactoring binge.  A lot, but not all, of the replication in this view was gone.   I keep saying that it's critical to remember that every time you DRY something out, you also introduce a dependency.  If we start to need highly divergent language for different plans, then some of this DRYing out will need to be undone.

After a duel with code climate warnings on method complexity the charges controller that works the above view looked like this:

```rb
  def create
    @user = User.find_by_slug(params[:user])
    @plan = Plan.new params[:plan]
    @sponsored_user = sponsored_user?

    update_user_to_premium(create_customer, @user)
    send_acknowledgement_email

  rescue Stripe::CardError => e
    flash[:error] = e.message
    redirect_to new_charge_path
  end
```

and sported a couple of new private methods

```rb
  def create_customer
    Stripe::Customer.create(
        email: params[:stripeEmail],
        source: stripe_token(params),
        plan: @plan.plan_id
    )
  end

  def sponsored_user?
    @user.present? && current_user != @user
  end
```

and a new class that I left in the same file as the controller for the moment:

```rb
class Plan
  attr_reader :plan_id

  def initialize(plan_id)
    @plan_id = plan_id
  end

  def to_s
    PLANS[plan_id]
  end

  def free_trial?
    plan_id == 'premium'
  end

  private

  PLANS = {
      'premium' => 'Premium',
      'premiummob' => 'Premium Mob',
      'premiumf2f' => 'Premium F2F',
      'premiumplus' => 'Premium Plus'
  }
end
```

The domain entity of a plan which had previously only really existed on the Stripe servers explicitly, now had a class in our local code.  I was torn between having a class and just going with some functional transformations.  I guess the key thing an instance of plan gives me is the ability to refer to it repeatedly in the view without having to keep passing in the plan_id.  Otherwise it could be collection of methods in a module.  Functional programming tells us to be suspicious of state ... I didn't want to agonise so long we would delay delivering the feature.

I didn't write tests for the Plan class.  I briefly had it in the lib directly, but Rails didn't load it automatically and although I found a [stack overflow post](http://stackoverflow.com/questions/19098663/auto-loading-lib-files-in-rails-4) with suggested fixes, I did not want to start playing with the Rails config at 6pm on a Friday.  What I was aiming for here was effectively a private class  that only this controller was using.  In the same way that we wouldn't write tests for private methods, I didn't want to be doing TDD domain entity evolution at this point.  My objective was DRYing out the view and controller while keeping the cukes green.  I did not want to get into other design questions of PORO in lib or ActiveRecord model.  

Again, all this functionality is in flux.  We've got a restricted set of people working on the codebase.  In a project with a larger team I'd have to button down here and make this a serious object with tests, or even explicitly make it private.  I'll need to do one or the other when we work on this again (and start better integrating PayPal and plan upgrades).  I'm thinking of giving it a week, and perhaps devoting this week to more fixes on the Google Hangout API fiasco.  Not sure.  Either way Raoul got this all deployed over the weekend and we have Sasha's changes up which include the banner from our new sponsor drie, and the ability for members to sponsor each other for Premium - great work Raoul!

Right then, once more unto the breach dear friends!






