Urgh, up in the night with sick child - forgot we had an early session of the webdev mob set for today since tomorrow is day off for wife's birthday.  Kernel panic at the start of that mob - urgh, do I need a new computer ... very late to the blog, let's see if I can make a couple more corrections to our faulty historical subscription data.

One thing I notice immediately is that one of the subscriptions is not showing up for the PremiumF2F user who I was working on yesterday ... I wonder if that's because the new plan I created doesn't have a corresponding type ... although it's more likely it's because it doesn't have a payment source ... yep, that fixes it:

```
irb(main):019:0> subscriptions = Subscription.includes(:user).joins(:payment_source).all ; nil
=> nil
irb(main):020:0> pp subscriptions.select {|s| s.user_id == 21} ; nil
[#<Subscription:0x0055eb91123540
  id: 6,
  type: nil,
  started_at: Wed, 25 Jan 2017 16:30:25 UTC +00:00,
  ended_at: Wed, 15 Nov 2017 16:12:20 UTC +00:00,
  plan_id: 13,
  sponsor_id: nil>,
 #<PremiumF2F:0x0055eb91121650
  id: 31,
  type: "PremiumF2F",
  started_at: Thu, 15 Dec 2016 16:07:50 UTC +00:00,
  ended_at: nil,
  plan_id: 3,
  sponsor_id: nil>,
 #<Premium:0x0055eb8b8849c0
  id: 2,
  type: "Premium",
  started_at: Thu, 07 Jul 2016 12:16:45 UTC +00:00,
  ended_at: Thu, 15 Dec 2016 16:07:47 UTC +00:00,
  plan_id: 1,
  sponsor_id: nil>]
=> nil
```

Okay, so time to fix up one more Premium Plus who started at Premium Plus and downgraded to Premium Mob to switch their Plus contribution to sponsorships.  Accurately recorded the downgrade but still not in a position to precisely sort out their sponsorships ... will need another pass to sort that.

```
irb(main):033:0> subscriptions = Subscription.includes(:user).joins(:payment_source).all ; nil
=> nil
irb(main):034:0> pp subscriptions.select {|s| s.user_id == 885} ; nil
[#<PremiumPlus:0x0055eb8b6f3cf0
  id: 8,
  type: "PremiumPlus",
  started_at: Wed, 15 Jun 2016 23:05:00 UTC +00:00,
  ended_at: Fri, 15 Dec 2017 23:06:55 UTC +00:00,
  user_id: 885,
  plan_id: 4,
  sponsor_id: nil>,
 #<PremiumMob:0x0055eb8b6ebc80
  id: 70,
  type: "PremiumMob",
  started_at: Fri, 15 Dec 2017 23:06:55 UTC +00:00,
  ended_at: nil,
  user_id: 885,
  plan_id: 2,
  sponsor_id: nil>]
```

Do I sort out the partial things we can handle for sponsorships? Guess I push on and try and get the subscription data accurate ... 
