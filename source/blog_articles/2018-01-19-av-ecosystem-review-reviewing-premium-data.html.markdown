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
