So I reckon I've spent about 40 minutes each morning this week working through Premium members.  I think I've reviewed 20 and where possible got their data updated and correct in the WSO system so that that we have a "single source of truth" for data abour Premium members, and that that can be exposed over the secure Premium API for use by the machine learning system.  Of course we're throwing up various changes that are needed in the data model and there's going to need to be more hooking up to the Slack and Paypal APIs in order to ensure that changes to Premium statuses can be coordinated directly through the WSO site both by admins and by the individual members themselves.  I'll forge on this morning.  The latest is an orphaned subscription:

```
  {
    "email": null,
    "sponsor_email": null,
    "started_on": "2017-02-28",
    "ended_on": null,
    "plan_name": "Premium Mob",
    "payment_source": "PaymentSource::Stripe"
  }
  ```
  
Matching up the timings I can see that this is an orphaned subscription created when a Premium Mob member upgraded to Premium F2F.  I won't be able to clear this up until we have a different relation in the database model.  I wonder if that's the thing to start on.  I find a relevant ticket in the waffle board and check out a branch for that and then change the relation between users and subscriptions to a `has_many`:

```
class User < ActiveRecord::Base
    has_many :subscription, autosave: true
```

That creates a series of failures in the specs where attempts to get `user.subscription.plan` are not working because we would now have to talk about `user.subscriptions`.  I guess we should replace `user.subscription` with something like `user.current_subscription` where we'd find the user's subscription that didn't have an ended_at date.  We'd also need to ensure that the user only had one active subscription at a time.  So various changes, and I could dive into those, but maybe making the connection to as many premium members as possible is the key thing ...

I'm caught for a moment about whether I should try and update the ended_at date for the old Premium Mob subscription for the member who upgraded to F2F, which I decided to do for consistency:

```
irb(main):002:0> pp Subscription.all.select{|s| s.user.nil?} ; nil
[#<Subscription:0x007faf45ddd0c8
  id: 40,
  type: nil,
  started_at: Tue, 28 Feb 2017 02:00:22 UTC +00:00,
  ended_at: nil,
  user_id: nil,
  plan_id: 2,
  sponsor_id: nil>,
 #<PremiumF2F:0x007faf3f729b60
  id: 32,
  type: "PremiumF2F",
  started_at: Tue, 03 Jan 2017 18:00:24 UTC +00:00,
  ended_at: nil,
  user_id: nil,
  plan_id: 3,
  sponsor_id: nil>,
 #<Premium:0x007faf3f729930
  id: 3,
  type: "Premium",
  started_at: Tue, 25 Oct 2016 16:46:56 UTC +00:00,
  ended_at: nil,
  user_id: nil,
  plan_id: 1,
  sponsor_id: nil>,
 #<Subscription:0x007faf3f7288a0
  id: 58,
  type: nil,
  started_at: Thu, 10 Nov 2016 11:11:00 UTC +00:00,
  ended_at: nil,
  user_id: nil,
  plan_id: 1,
  sponsor_id: nil>,
 #<Subscription:0x007faf45deff20
  id: 46,
  type: nil,
  started_at: Wed, 03 May 2017 18:47:13 UTC +00:00,
  ended_at: nil,
  user_id: nil,
  plan_id: 2,
  sponsor_id: nil>,
 #<Subscription:0x007faf45def8b8
  id: 44,
  type: nil,
  started_at: Tue, 25 Apr 2017 08:41:15 UTC +00:00,
  ended_at: nil,
  user_id: nil,
  plan_id: 1,
  sponsor_id: nil>]
irb(main):003:0> s = Subscription.find(40)
=> #<Subscription id: 40, type: nil, started_at: "2017-02-28 02:00:22", ended_at: nil, user_id: nil, plan_id: 2, sponsor_id: nil>
irb(main):004:0> u = User.find_by email: '...'
irb(main):005:0> u.subscription.started_at
=> Fri, 14 Jul 2017 01:17:42 UTC +00:00
irb(main):006:0> s.ended_at = u.subscription.started_at
=> Fri, 14 Jul 2017 01:17:42 UTC +00:00
irb(main):007:0> s.save
```

Next up I have a member who got three months of Premium from CraftAcademy South Africa, but they are in the system as paying via Stripe since that's how CA SA paid.  The member is now paying their own way, and again we need support for multiple subscriptions per user to update that.  The next member is also a CraftAcademy South Africa graduate who is paying their own way, and subsequently upgraded to Premium Mob.  Again, I won't be able to fix these up without the domain model changes.  I guess I push on and fix up what I can and then take another pass once I get the domain model fix out.

I note what I'm not doing recently is remember to work with Mob level members on their professional development plans ... although I don't know how much folks really appreciate that.  It's kind of a challenging thing for busy people to keep up with ...

I think the last one for today now.  A Premium Plus member who downgraded to Premium F2F and that all needs to be represented to.  That domain model ...
