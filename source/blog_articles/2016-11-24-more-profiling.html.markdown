---
title: More Profiling of My Days
date: 2016-11-24
tags: eliminate simplify delegate automate premium agile ventures planning prioritising profiling measuring sponsors upgrades communication
author: Sam Joseph
---

So I did a second day of profiling.  The details are as follows:

```
09:15 - 10:15 writing blog draft (plus distribution)

10:30 - 11:00 reviewing slack and email - promoting, handling mentors, premiums

11:00 - 11:30 Writing emails to potential sponsors

11:30 - 11:45 Scrum

11:45 - 12:30 WSO hangout with Premium F2F member

12:30 - 12:45 Negotiating with company on potential paid project

12:45 - 13:00 getting CA and PayPal folks into new premium chat instance - notifying Premium Mobs, F2Fs and Pluses about mob programming session

14:15 - 15:15 Fielding questions from Premiums on Slack (got an upgrade to F2F), co-founder 

15:15 - 15:45 PR Review (AsyncVoter, LocalSupport)

15:45 - 16:00 Scrum

16:00 - 17:00 More PRs - fixed AsyncVoter - SHF comments, did AV mailing, updated and posted blog 

18:15 - 18:45 Reviewing ProjectScope PRs
```

Reviewing this again with Eliminate, Simplify, Automate, Delegate an early insight was that I could automate the creation of the list or related videos that I drop into the end of each blog.  Reviewing Slack and Email - I feel like I really have to do that each day.  There was a chunk of stuff to do with setting up a new Premium member and a new Premium Mentor.  I created a [ticket on WSO](https://github.com/AgileVentures/WebsiteOne/issues/1300) to cover the details of what might be automated there.  That looks promising. There's a couple of relatively straightforward coding tasks there, although there is also my desire to refactor the charges controller ... my intuition is that there's a double win for that sort of automation when we do it in a live pairing session in that it helps others see into our codebase, serves as a potential learning platform.   Not that many people pour over our videos, but it's a chance for me to pair with other AV members and I think we all get value from that.

I'm trying to step up writing of emails to potential sponsors after Charles Max Wood of Ruby Rogues talked on the Freelancers show about how that was the critical task that could have the biggest impact that he often didn't get around to.  Don't think I can drop or delegate that yet.  After the scrum I got in a hangout with Matt to unblock him on WebSiteOne.  I burned some time explaining why I don't like the UX of our status feature on WSO.  Maybe that's wasting time, or maybe that's important communication of my values regarding UX design to other team members.  I feel like at least some value was being derived both for WSO and for Matt as we unblocked him and cleared up ambiguity around the latest ticket he was working on.

Then I was negotiating in Slack with a company about a potential paid project.  Maybe that will come to nothing, but my intuition (which I think Michael and Marouen share) is that a few paid projects would make the whole Premium package so much more attractive to those on the fence about whether to sign up.  Then it was more Slack action getting the CA and Paypal Premiums into the new Premium chat instance.  I'd used the Stripe "customer" list to invite folks, so that had left out those not represented in Stripe.  That pushes me to want to improve domain model in WSO so we can properly represent all the current avenues that members can go Premium, so that I can a clear central place for a coherent list, although thinking about it I can see how I can extract that data from the console.  Still as the number of Premium users increases (which is a key goal) we've got to get better at consistency tracking who they are and how they've come to us.

I spent a little time notifying Premium Extras (new term - that's Mob, F2F and Pluses, i.e. everyone eligible for Mob) about the Mob Programming session we'll do later today.  That feels like it could be an important engine of growth.  After lunch Michael wasn't around again and I dithered about throwing myself into programming tasks versus just catching up on admin and station keeping.  I was fielding questions on Slack for almost an hour.  I could argue that I should have been productively coding with that hour, however one Premium member did upgrade to F2F following our conversation.  I think there's something critical in there.  Communicating with those consuming the services you provide is essential.  That doesn't mean doing tech support for everyone on every tier all the time.  However, giving an ear to those who are already committed to support our charity financially and helping them find ways to derive more value ... that feels kind of core.  

I guess ultimately I need some full time programmers, some full time marketing people, some full time "sales" people ... :-) Well I hope we can stay small and friendly for the time being.  After that Slack binge I did get down to some PR review, getting two PRs merged into AsyncVoter and a flurry of comments on LocalSupport.  We had another quick scrum and there was more admin, doing the AV mailing - 7 more 15 minute stabs at that and I'll have emailed everyone in the community and then perhaps I can take a bigger picture look about how we stay in touch people in the long term.  Am I setting an artificial barrier for myself there?

I had forgotten to update and post my blog of the previous day, so I hit that and then grabbed some time later in the evening to review ProjectScope PRs for the Berkeley students.  Armando feels the ProjectScope project metric tracking system is going to be critical to managing his course in the future and the part 3 project course for the MOOC.  I was able to give a flurry of comments and agonise about whether I was pushing on the students too hard.  I need to spend some time deploying their branches and experimenting with real project data.

Also the bot shut down when I closed my computer so we may have lost attempted 3rd votes to the async vote we were trying to run in WSO.  Gotta get that bot deployed permanently.  So anyhow, what could I eliminate:

* posts on PRs that are too long
* scrums running over time
* me ranting
* slow AV mailings

what could I simplify:

* PR posts

what could I automate:

* Blog draft submission
* Blog draft video lists
* AV mailing process
* Premium and Mentor upgrade
* Tracking of current range of Premiums
* Hosting of async bot

and I don't know what, if anything, I can delegate. Everyone's a volunteer in our organisation, and I don't think I can ask anyone to do anything.  I need to wait for people to ask for interesting things to do.  The most successful thing so far has been a process of making tickets of everything I can think of as important on the relevant repos and occasionally pointing to things when people ask :-)

Well, I think that's enough profiling for now.  There's some clear possibilities for automations, and I guess rather than agonise about which to do I should just start knocking them off in some order :-)

###Related Videos:

* ["Martin Fowler" scrum](https://youtu.be/aGjQ5N7nRfQ)
* [WSO Huddle](https://www.youtube.com/watch?v=dPJqeBPFluc)
* ["Kent Beck" scrum](https://www.youtube.com/watch?v=4qn9b8MVbvg)





