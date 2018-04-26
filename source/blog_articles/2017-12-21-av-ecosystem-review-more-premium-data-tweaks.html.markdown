---
title: AV EcoSystem Review Premium Data Tweaks
author: Sam Joseph
---

![tweak](../images/tweak.jpg)

Kids off school - slightly earlier to the computer, but no earlier to the blogface.  Although, I guess I did stop to review all my Slack and email.  Urgh.  Feel like I need to take a free day from Xmas to get this all completed.  Let's push on trying to update the Premium user data in the system, updating my notes on Premium users, and reaching out to those I haven't spoken to in a while.  Next up I have three Premium members who got 12 months of Premium membership bundled with the CraftAcademy bootcamp fees:

```
  {
    "email": "...",
    "sponsor_email": null,
    "started_on": "2016-11-07",
    "ended_on": null,
    "plan_name": "Premium",
    "payment_source": "PaymentSource::CraftAcademy"
  }
```

So now I have the trouble of needing to represent those members who've started paying their own way since their sponsored membership ended.  That's a refactoring of our data model.  Will have to leave that, but can mark those members who've not renewed as having ended.

```
irb(main):001:0> u = User.find_by email: '...'
irb(main):002:0> u.subscription.ended_at = Date.parse('2017-11-07')
=> Tue, 07 Nov 2017
irb(main):003:0> u.save
```

Then I come to a user who had Premium sponsored for a year by another user, and has since had initial sponsorship layered on top by a different sponsor.  Another situation where we need the user `has_many` subscriptions relationship, but I can add the sponsor relation and the fact that the basic Premium subscription will end after 12 months ... should I stick future `end_dates` in there? I guess so - it's obvious from our experience with sponsorship that we need to be clearer with end points.  I think it might also help people make more effective use of their sponsorship; having a time limit, that is.  Anyhow, I start by linking up the sponsorship relation for this subscription:

```
irb(main):013:0> u = User.find_by email: '...'
irb(main):014:0> s = User.find_by email: '...'
irb(main):015:0> u.subscription.sponsor = s
irb(main):016:0> u.save
=> true
```

Still only making my way through about five members a day - gonna need to find a clear day to complete this ...
