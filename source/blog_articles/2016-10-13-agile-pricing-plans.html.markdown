---
title: Agile Pricing Plans
date: 2016-10-13
tags: agile stripe drafts prototypes clone dup activerecord papertrail gem
author: Sam Joseph
---

Continuing with my non-pairing streak, I got a fair few things sorted today.  A highlight being prototyping the new pricing plans for AgileVentures.  The current draft looks like:

* £10/month: Premium - private slack group, RubyMine license, eligibility for paid and priority projects
* £25/month: Premium 121 - private text chat with mentor, 10 year professional development plan
* £50/month: Premium F2F - face to face hangouts with mentor, voting rights in charity meetings

In creating the draft, I appeared to fall foul of the difference between ActiveRecord `dup` and `clone`.  I was taking the existing pricing static page and copying it for tweaks to the new approach, but I used `clone` instead of `dup` and so ended up over-writing the existing plan, doh!  Fortunately we have versioning via the papertrail gem so I pulled out the old version and then used `dup` to create a version without an id, and save it into place. Phew!

I shared the new plan draft with some of the core team.  On reflection, perhaps I should have been following Scott Klemmer's advice to always share multiple prototypes, but anyway, I'm getting great feedback from Federico about page layout to try and highlight the three-way choice, and from Thomas about a mob programming plan.  I guess an alternate could be something like:

* £10/month: Premium - private slack group, RubyMine license, eligibility for paid and priority projects
* £25/month: Premium Mob - mob programming with mentor, 10 year professional development plan
* £50/month: Premium F2F - face to face hangouts with mentor, voting rights in charity meetings

and mob programming is fun.  Different mentors are likely to have different preferences about whether they want to give 121 text support or hosting mob programming sessions, or 121 pairing hangouts.  Much as I love mobbing and pairing, my intuition from the last 5 years of doing online education is that the majority of people seem to really prefer 121 communication.  Once you can get people bitten by the mobbing or pairing bug, then sure they can convert, but that's potentially a slow uphill process.

We could be throwing in all these options with even more types of plans, but the prevailing wisdom seems to be to have fewer plans to make the decision process easier.  I'm certainly not thinking of imposing 121 support on any of the AV mentors; I just seem to be under near constant pressure from MOOC students to give 121 support, so if I can offer them a plan where I do give them 121 support then that makes my life a lot easier and I'm giving people what they are asking for.  I haven't yet heard from any learners that they want mob programming.  Now maybe what they *need* is mob programming and pair programming.  It's this funny divide between listening to your users and them asking for a "faster horse".

I always thought we'd have more people taking up the free pair programming that AgileVentures tries to support.  Some did, but it never got to the critical mass I imagined.  My current working hypothesis is that it is just too intimidating for many people.  It would never occur to me online to conceal my name, or my face, or that I would be put off from attending a Google Hangout on air because of the message that pops up that says other people can see this broadcast, but over the last few years I've encountered several folks who've said that's something that puts them off.

Now we can say: "Be strong! If you push through that fear you'll discover a brave new world of learning!".  And I really believe that's true, but I think you can only get so far exhorting people to overcome their fears.  In my mind if you have 100 people, there will be five who are happy to say almost anything in front of the group.  You can probably get those five to do almost anything.  They may also do inappropriate things that causes the group to disperse, so watch out for that.  Of the remaining people, if you're charismatic and articulate you can probably get another five to join in a public learning process through convincing them that it is a great thing.

However that leaves the remaining 90 people.  There are some who are never going to participate in public learning.  That said I'd guess there are about 40 who you can help build up to the point that they would stop being held back by a fear of failure, particularly public failure.  It's just that my suspicion is that to do that you will first need to connect to them individually in order to hear about their fears and needs in a private setting.

Now everyone will probably put different numbers on these different types of people (that I've just made up).  I'm going with:

* Happy to learn and fail in public 5%
* Can be convinced to learn and fail in public through exhortation 5%
* Can be convinced to learn and fail in public by personal support 40%
* Unlikely to ever want to learn in public 50%

It would be great to find some experimental studies to work out real values for these numbers, or even conduct such studies.  However, I think my days of doing purely scientific work are over :-)  Some might say I'm just deluding myself that all that 121 support is going to help you bring 40% of folks to a place where they are participating at a new level, when in fact actually that will only get you one or two people and what you'll end up doing is a lot of individual hand holding for not much gain.

Maybe so, I have no omniscient power.  However my intuition says that sustainability will be hard if we're relying on only those few willing to take the plunge for learning in crowd format.  Besides I love connecting with people individually on a 121 basis.  I love hearing about what's going on for them and throwing my cognitive resources at trying to hook them up with the people, learning materials and activities that will help them achieve their goals.

What I can't do, if I want to feed my kids, is spend all my time giving 121 support for free.  It even takes up time just saying no politely to all the people asking for 121 support.  Anyway, brain dump!  The tricky thing now, compared to when I first created the pricing page, is that we have other plans like the "Premium plan" melded into the software of the AgileVentures site, so rolling out a new pricing plan will require coordination with several pull requests, which is not as lightweight as I would like. Hence the creation of the beta pricing page, blogging about this, and so forth.  One thing that feels really great though is that we really are living the Agile process, and big thanks to Stripe for making it straightforward to take credit card payments.  Creating the new plans on the Stripe dashboard will be trivial, while the complexity of rolling them out to our users will be what we make it to be.

