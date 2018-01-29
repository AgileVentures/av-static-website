The APSoc membership database beta is delivered on time, and they've agreed to go ahead with the website development, so that's all pretty good.  Smallish contracts, but enough to grease the wheels.  I'm a bit run down again, not through lack of sleep obsessing about children's soccer, but due to an ongoing digestive issue - am I becoming lactose intolerant?  Or something else.  Not as unpleasant as a nasty head cold, and not enough to make me take to working in bed, but enough to slow me to half speed.  I managed a coupled of hours work on Sunday afternoon which was enough to sort a few critical admin tasks left over from Friday.  Now I have the luxury of not booting up MS Access for a week (unless I want to) and I can focus on AV again (in the mornings).  The items that loom large in my mind are:

1) sorting all the subscription data to get that accurate
2) working out all the sponsor relationships (inc. adjustment to data model)
3) working on the private events feature
4) the new react front end for AV

I think I've got to focus on sorting the subscription data as no one is going to work on that with me, and I can't delegate it.  I've also got ideas about needing to make Friday AM a non-blogging, merging-backlog morning so that my released blog posts are more topical and up to date ..., but I'll return to that on Friday.  So first up I've got a user who initially signed up for Premium, then upgraded to Premium Mob, and then most recently downgraded to Premium Sponsor.  A bit of checking on Stripe and I've got their history set up correctly:

```
#<Premium:0x0055f8cfdcb4f0
  id: 3,
  type: "Premium",
  started_at: Tue, 04 Oct 2016 20:42:35 UTC +00:00,
  ended_at: Fri, 07 Apr 2017 15:22:51 UTC +00:00,
  user_id: 1284,
  plan_id: 1,
  sponsor_id: nil>,
 #<PremiumMob:0x0055f8cfe01050
  id: 4,
  type: "PremiumMob",
  started_at: Fri, 07 Apr 2017 15:22:51 UTC +00:00,
  ended_at: Wed, 13 Dec 2017 10:12:40 UTC +00:00,
  user_id: 1284,
  plan_id: 2,
  sponsor_id: nil>
```

Although I don't have any good mechanism of representing their Premium sponsorship transition in this system unless I specifically assign their sponsorship to a particular individual who is receiving just the simple Premium sponsorship, when what we're doing is bundling sponsorship contributions to support higher Premium levels.  I'll have to come back to that once I've improved the data model.   At least I've managed to clear up one of the headless hanging subscriptions.

Then I move on to another complex case, a user that started off as Premium, then upgraded to Premium F2F, then got an extra half hour on top of that, then cancelled that and is currently continuing Premium F2F.  It involves creating a new plan in the db to represent the half hour upgrade, but their data is now sorted out like this:

```
#<Premium:0x0055f8cffcc3a8
  id: 2,
  type: "Premium",
  started_at: Thu, 07 Jul 2016 12:16:45 UTC +00:00,
  ended_at: Thu, 15 Dec 2016 16:07:47 UTC +00:00,
  user_id: 21,
  plan_id: 1,
  sponsor_id: nil>,
 #<Subscription:0x0055f8cffcc128
  id: 6,
  type: nil,
  started_at: Wed, 25 Jan 2017 16:30:25 UTC +00:00,
  ended_at: Wed, 15 Nov 2017 16:12:20 UTC +00:00,
  user_id: 21,
  plan_id: 2,
  sponsor_id: nil>,
 #<PremiumF2F:0x0055f8ca833f88
  id: 31,
  type: "PremiumF2F",
  started_at: Thu, 15 Dec 2016 16:07:50 UTC +00:00,
  ended_at: nil,
  user_id: 21,
  plan_id: 3,
  sponsor_id: nil>
```

So that's two fixed up this morning, and I suspect even if I use every morning this week I won't complete all these, but at least as I go along I'm checking in with individual Premium members, and getting everything sorted out.  A few more weeks, and hopefully the current set up means that the data will be maintained in a consistent state - although I guess there's always going to be corner cases ...



