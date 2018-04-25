---
title: AV EcoSystem Review Digging into Premium Data
author: Sam Joseph
---

![digging in](../images/digging-in.jpg)


Yesterday I got stuck in to reviewing the Premium data from our API.  It was slow going, but at least I started.  Hoping I can plow through a chunk today.  Starting with our first ever Premium member, I reach out them to say hi and check how they are doing.  The next is a long term Premium who cancelled recently.  I see that they also deleted their account from AgileVentures a few days before reaching out to me in person.  I can sense that members would love to be able to cancel without having to interact with a human - perhaps we should prioritise that functionality?

I also notice that the starting dates for a few members are off, probably reflecting us adding in data structures after the first few members had signed up.  Using the Stripe dashboard to get the initial creation dates I adjust that data.

```
irb(main):007:0> u.subscription.ended_at = Date.parse('15-11-2017')
=> Wed, 15 Nov 2017
irb(main):008:0> u.save
=> true
irb(main):009:0> u.subscription.started_at = Date.parse('01-08-2016')
=> Mon, 01 Aug 2016
irb(main):010:0> u.save
=> true
```

I reach out to those members too and update my big file of notes about those members.  Next up we have a hanging subscription that's not associated with any user and the date doesn't match anything in Stripe and PayPal.   Looks like an accidental creation on the day I was retrospectively filling in data for the earliest few members.  I checked that it's not associated with any members, even deleted ones, so I delete that subscription entity:

```
irb(main):022:0> s = Subscription.find(20)
=> #<Premium id: 20, type: "Premium", started_at: "2016-10-25 16:46:56", ended_at: nil, user_id: nil, plan_id: 2, sponsor_id: nil>
irb(main):023:0> User.with_deleted.all.count
=> 6704
irb(main):024:0> User.with_deleted.all.select{|u| u.subscription && u.subscription.id == 20}
=> []
irb(main):025:0> s.destroy
=> #<Premium id: 20, type: "Premium", started_at: "2016-10-25 16:46:56", ended_at: nil, user_id: nil, plan_id: 2, sponsor_id: nil>
irb(main):026:0> s = Subscription.find(20)
ActiveRecord::RecordNotFound: Couldn't find Subscription with 'id'=20
```

and I'm managing to update about five records a day at the current rate ... hmmm
