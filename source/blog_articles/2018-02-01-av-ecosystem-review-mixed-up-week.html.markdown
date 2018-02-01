Didn't get to the premium subscription data updates at all yesterday; bit of a mixed up week with child sickness and wife birthday.  Let's see if we can make a dent today:

```json
  {
    "email": "...",
    "sponsor_email": null,
    "started_on": "2016-10-11",
    "ended_on": null,
    "plan_name": "Premium",
    "payment_source": "PaymentSource::Stripe"
  }
```

First up a Premium user who's been with us a while, but never upgraded - will check in to say hi.  Next up another long term member who started on Premium and since upgraded to Premium Mob.

```json
  {
    "email": "...",
    "sponsor_email": null,
    "started_on": "2016-08-07",
    "ended_on": null,
    "plan_name": "Premium Mob",
    "payment_source": "PaymentSource::Stripe"
  }
```

I add a new subscription to fix it all up - looking good:

```
irb(main):016:0> pp subscriptions.select {|s| s.user_id == 1072} ; nil
[#<Premium:0x00556f3d560548
  id: 9,
  type: "Premium",
  started_at: Sun, 07 Aug 2016 08:51:18 UTC +00:00,
  ended_at: Thu, 22 Jun 2017 09:37:38 UTC +00:00,
  plan_id: 1,
  sponsor_id: nil>,
 #<PremiumMob:0x00556f42c0c9f0
  id: 71,
  type: "PremiumMob",  
  started_at: Thu, 22 Jun 2017 09:37:38 UTC +00:00,
  ended_at: nil,
  plan_id: 2,
  sponsor_id: nil>]
=> nil
```

and I reach out to that member to check how they are doing.  Next another long term Premium member:

```json
  {
    "email": "...",
    "sponsor_email": null,
    "started_on": "2016-08-30",
    "ended_on": null,
    "plan_name": "Premium",
    "payment_source": "PaymentSource::Stripe"
  }
```

All details correct, let's reach out and say hi ... email done, and now another long term Premium member who switched to Premium Mob a while back.  Need to update them ... done and time to check in with that user too.  That's four done today and now I'll have to move on again ... not sure what the solution is here.  I'd love to have someone to pass this task off to, but I also feel like I need to reach out to all the members.  Will natural growth ever get us to the point that we could hire someone to work on this kind of thing? Or will investment be required ...?

