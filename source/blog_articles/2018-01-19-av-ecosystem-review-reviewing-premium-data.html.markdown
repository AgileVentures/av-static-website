The new premium multiple subscription model is live in production so I should be able to update all the accounts now to have accurate subscription histories.  I'm also trying to get the monthly report for the AgileVentures Trustees finished, but I've burnt time on a late start today, long breakfast, and the trying to sort out a UPS shipment.  Oh well, maybe I'll catch up on Sunday afternoon.  So I've called up the data on the premiums from the new API:

```
curl -H "Authorization: Token token=..." https://www.agileventures.org/api/subscriptions.json | jq
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 10229    0 10229    0     0   9037      0 --:--:--  0:00:01 --:--:--  9036
[
  {
    "email": "...",
    "sponsor_email": null,
    "started_on": "2017-03-09",
    "ended_on": null,
    "plan_name": "Premium",
    "payment_source": "PaymentSource::Stripe"
  },
```

and so I need to step through each and look at my notes on whether they are represented accurately.  This first premium member hasn't yet changed their subscription level and so there's no change required here, although now I have a spreadsheet I can update some columns such as when we last contacted them and then they last responded.

Next up I have a Premium Mob member:

```
  {
    "email": "...",
    "sponsor_email": null,
    "started_on": "2016-10-25",
    "ended_on": null,
    "plan_name": "Premium Mob",
    "payment_source": "PaymentSource::Stripe"
  },
```

Now they did upgrade at some point so to make the data accurate I should add another subscription for this user to indicate when they started on Premium and when they changed to Premium Mob.  They also stopped paying their subscription in November last year and haven't been responding to follow up communications so I need to think about how to represent that ... one thing I did fix up in Stripe yesterday is to ensure that subscriptions are not permanently cancelled after a single failed round of payments, as seems to be Stripe's default setting.

Looking at the Stripe payment history I can see that their first payment was on 11th October 2016, which presumably was after a seven day free trial, so I'd guess they would have actually signed up on 4th October.  I think the date in the existing subscription is the date when we first deployed the subscription models themselves, so I can correct that.  Their first Premium Mob payment was on 11th May 2017, but I can see there is prorated extra payment so they must have upgraded in the 30 days leading up to that.  Further in the Stripe system I can see the actual upgrade took place on 27th April.

```
irb(main):002:0> u.subscriptions
=> #<ActiveRecord::Associations::CollectionProxy [#<Premium id: 17, type: "Premium", started_at: "2016-10-25 16:46:56", ended_at: nil, user_id: 3276, plan_id: 2, sponsor_id: nil>]>
```

I also notice that we have this confusing legacy `type` field, which has been replaced by plan_id.  I should really get something in there to delete the old types ... anyway, the critical step is that I can adjust this users subscriptions to reflect reality.  I update the existing subscription to indicate when the user upgraded to Premium Mob:

```
irb(main):003:0> s = u.subscriptions.first
=> #<Premium id: 17, type: "Premium", started_at: "2016-10-25 16:46:56", ended_at: nil, user_id: 3276, plan_id: 2, sponsor_id: nil>
irb(main):004:0> s.started_at = DateTime.parse('2017-04-27 21:10:10')
=> Thu, 27 Apr 2017 21:10:10 +0000
irb(main):005:0> s.save
=> true
```

Now I need to create another subscription that will correspond to the period the user was a Premium member:

```
irb(main):006:0> s = Subscription.create started_at: "2016-10-04 17:19:15", ended_at: DateTime.parse('2017-04-27 21:10:10'), user_id: 3276, plan_id: 1
=> #<Subscription id: 67, type: nil, started_at: "2016-10-04 17:19:15", ended_at: "2017-04-27 21:10:10", user_id: 3276, plan_id: 1, sponsor_id: nil>
irb(main):007:0> u.subscriptions << s
=> #<ActiveRecord::Associations::CollectionProxy [#<Premium id: 17, type: "Premium", started_at: "2017-04-27 21:10:10", ended_at: nil, user_id: 3276, plan_id: 2, sponsor_id: nil>, #<Subscription id: 67, type: nil, started_at: "2016-10-04 17:19:15", ended_at: "2017-04-27 21:10:10", user_id: 3276, plan_id: 1, sponsor_id: nil>]>
irb(main):008:0> u.save
=> true
```

So that works, but I notice that the API only returns the subscription that doesn't have the `ended_at` set - which is maybe sensible, in that it's showing current subscriptions, but not exactly what we want for the machine learning analysis.  Ah, it seems I didn't set a payment source - have done that like so:

```
irb(main):014:0> s.payment_source = PaymentSource::Stripe.create type: "PaymentSource::Stripe", identifier: "..."
=> #<PaymentSource::Stripe id: 67, type: "PaymentSource::Stripe", identifier: "...", subscription_id: 67>
irb(main):015:0> s.save
=> true
```

and now both subscriptions show up:

```
  {
    "email": "...",
    "sponsor_email": null,
    "started_on": "2017-04-27",
    "ended_on": null,
    "plan_name": "Premium Mob",
    "payment_source": "PaymentSource::Stripe"
  },
  {
    "email": "...",
    "sponsor_email": null,
    "started_on": "2016-10-04",
    "ended_on": "2017-04-27",
    "plan_name": "Premium",
    "payment_source": "PaymentSource::Stripe"
  }
```

So it makes me think that an event log would be great, but that's a big chunk of work.  Arguably PaymentSources need to support multiple subscriptions, since there's no need to create a new one each time.  Storing an event log would be more flexible in the long run I guess, but less dry, hmmm.   What's frustrating is the time it's taken me to update one record.  Feels like it would be a good week of mornings to get them all sorted and I haven't even begun to address the sponsor relations.  Gonna have to put that aside in terms of the model and just get them down on paper in the AV trustee report ...


